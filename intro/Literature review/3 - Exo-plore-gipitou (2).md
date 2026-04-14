# Exo-Plore

> [!abstract] **Main idea**
> Exo-Plore is a **simulation-based framework to optimize hip exoskeleton control parameters** without doing long human-in-the-loop experiments.  
> It combines:
> - a **gait data generator** based on **neuromechanical simulation + Deep RL**
> - a **surrogate optimizer** that searches the exoskeleton parameter space more robustly
>
> **Goal:** find exoskeleton parameters \((\kappa, \Delta t)\) that minimize **metabolic cost of transport (CoT)** while keeping the simulated adaptation **human-aligned**.

> [!info] **Why this matters for my PhD**
> This paper is interesting because it is **not just imitation of a reference motion**.  
> They try to build a simulation that can:
> 1. generate **human-like locomotion adaptation** under assistance,
> 2. estimate **metabolic benefit**,
> 3. optimize exoskeleton parameters in simulation,
> 4. generalize to **pathological gait**.
>
> So for my work, this is useful as an example of:
> - how to combine **neuromechanical simulation + RL + optimization**
> - how to make the simulation **match experimental trends**
> - how to use a learned simulator to explore **controller design space**
>
> But it is **not directly a force-control-at-the-neural-level paper**.  
> It is more about **simulation-based exoskeleton assistance optimization**.

## Core idea

The exoskeleton controller is very simple:

$$
\tau_{exo}(t) = \kappa \, u(t-\Delta t)
$$

with

$$
u(t) = \sin(\theta_r) - \sin(\theta_l)
$$

- \(\theta_r, \theta_l\): filtered right and left hip angles
- \(\kappa\): gain / torque scaling
- \(\Delta t\): delay

So the exoskeleton is parameterized only by:
- **gain** \(\kappa\)
- **time delay** \(\Delta t\)

> [!note]
> This is a **low-dimensional controller design problem**.  
> They are **not learning the exoskeleton policy end-to-end**.  
> Instead, they learn how the **human adapts** to a given exoskeleton controller, then optimize the controller parameters.

---

# Framework

## 1) Gait data generator

The framework uses a **musculoskeletal model with 164 muscles** and a hip exoskeleton.

The human controller has 3 modules:
- **PoseNet**: predicts PD target joint positions
- **PD controller**: computes desired joint torques
- **MCN (Muscle Coordination Network)**: maps target torques to muscle activations

Important detail:
- the PD torque is corrected by subtracting the exoskeleton torque, so the muscles only produce the **remaining required torque**

$$
\tau^{(j)}_{MCN} = \tau^{(j)}_{PD} - \tau^{(j)}_{exo}
$$

> [!important]
> This means the human controller is learning to move **with the exoskeleton in the loop**, not ignoring it.

### State
The PoseNet state includes:
- skeleton kinematics
- muscle-related quantities
- gait features
- recent **history of exoskeleton assistive torques**

So unlike a plain locomotion model, the controller explicitly sees the assistance history.

### Action
They use the **Generative GaitNet** residual action formulation:
- phase increment \(\Delta \phi\)
- residual pose displacement \(\Delta M\)

So action is not direct muscle activation.  
It is more structured:
- modify gait phase
- modify target pose

This target is then converted to torques and then to muscle activations through PD + MCN.

> [!compare]
> Compared to papers like Kinesis or pure direct-muscle RL:
> - **more structured control**
> - closer to a hierarchical controller
> - less raw than directly outputting muscle activations

---

## 2) Reward design

The total reward is:

$$
r_{total} = w_{gait} r_{gait} + w_{arm} r_{arm} + w_{energy} r_{energy} + w_{HEI} r_{HEI}
$$

### a) Gait reward
The gait reward is:

$$
r_{gait} = r_{step} \cdot r_{vel} \cdot r_{head} \cdot r_{sway}
$$

It encourages:
- target step length
- target walking speed
- stable head motion
- normal body sway

So they do **not** imitate a full MoCap trajectory directly.  
Instead they constrain locomotion through higher-level gait descriptors and stability-related terms.

### b) Arm imitation reward
They add an arm reward to avoid unnatural arm motion:

$$
r_{arm} = \exp\left(-\sum_{j \in arm} \left(\| \hat q_j - q_j \|/\sigma_{arm}\right)^2 \right)
$$

> [!note]
> Interesting detail: arm motion is the only place where they explicitly imitate reference motion here.

### c) Energy reward
They define a metabolic-energy-like term:

$$
r_{energy} = 1 - k_{energy}\cdot MEE
$$

with

$$
\frac{d}{dt}MEE(a;\alpha,\beta)=\sum_i m_i^{\alpha} a_i^{\beta}
$$

- \(m_i\): muscle mass
- \(a_i\): activation
- \(\alpha,\beta\): tunable exponents

They tune \((\alpha,\beta)\) so the simulator reproduces the **human preferred walking speed (PWS)** and realistic CoT-vs-speed trends.

> [!important]
> This is a key idea of the paper:
> **they tune the reward model itself to match known human experimental trends**.

### d) Human-Exoskeleton Interaction reward
This is one of the most original parts:

$$
r_{HEI} = 1 + \frac{1}{\kappa}\sum_{k\in\{L,R\}} \min(0, P_k)
$$

- \(P_k\): exoskeleton mechanical power on each side

This reward penalizes **resistive power**, not just rewards assistive power.

> [!abstract]
> Their hypothesis is that humans adapt in a way that tends to **avoid resistive interaction** with the exoskeleton.
>
> This is based on a **resistance-minimization / loss-aversion inspired idea**:
> humans are more sensitive to power losses than to equivalent gains.

> [!success]
> According to the paper, this reward is what allowed the simulated adaptation trends to best match human experimental data.

---

## 3) MCN loss

The MCN is trained in supervised fashion to map desired torque to muscle activations:

$$
L_{MCN} = \| \tau_{MCN} - \tau(a) \|^2 + w_{reg}\|a\|^2 + w_{IMR}L_{IMR}
$$

The last term is an **intramuscular regularizer** to keep line-muscles from the same anatomical muscle group coherent.

> [!important]
> This is there to improve **physiological plausibility**.  
> Without it, different segments of the same muscle could activate in unrealistic ways.

---

# Sim-to-real matching

This section is maybe the most useful one for me conceptually.

They do not simply train an RL controller and trust it.  
They explicitly try to make the simulation reproduce **known human behaviors**.

## Matching 1: preferred walking speed
They choose the metabolic model parameters \((\alpha,\beta)\) so that:
- CoT vs walking speed looks human-like
- the minimum CoT occurs near human preferred walking speed

Final choice:

$$
(\alpha,\beta) = (1.5, 1.0)
$$

## Matching 2: adaptation to assistance
They validate whether simulated gait adapts to exoskeleton delay/gain the way humans do.

They compare things like:
- assistive moment
- assistive power
- resistive power
- metabolic reduction

and show that **their HEI reward** reproduces the trends better than:
- no HEI reward
- a naive assist-maximization reward

> [!takeaway]
> So the paper is really about:
> **how to design the simulator/reward so that adaptation under assistance becomes realistic enough to optimize exoskeleton control in silico**.

---

# Exoskeleton optimizer

Once they have the gait generator, they sample many \((\kappa,\Delta t)\) values and compute CoT.

Then they train a **surrogate neural network** to predict CoT from exoskeleton parameters.

Why not Gaussian Processes?
- GP is good in low-data regimes
- but here they have lots of simulated data
- GP memory scales poorly
- NN is more scalable and differentiable

They use:
- **Latin Hypercube Sampling** for better coverage
- **Huber loss**
- **gradient penalty** to make the surrogate landscape smooth

Loss:

$$
L_{surrogate} = L^{\delta}_{h}(\hat y, y) + \lambda_{grad}\|\nabla_x \hat y\|_2^2 + \lambda_{L1}\sum_i |w_i| + \lambda_{L2}\sum_i w_i^2
$$

Then they do **gradient-based optimization** on the surrogate instead of optimizing directly on noisy RL rollouts.

> [!important]
> This is clever because the raw simulation landscape is noisy and full of local minima.  
> The surrogate gives a smoother optimization surface.

---

# Main results

## 1) Unassisted gait
They first check whether the simulator produces realistic gait without assistance.

They compare against human data:
- joint kinematics
- muscle activations
- GRFs
- CoT vs speed

Main message:
- overall gait trends are realistic
- some mismatches remain, especially in hip internal rotation and some muscle activations

## 2) Assisted gait
Under exoskeleton assistance, they reproduce trends for:
- hip flexion angle and velocity
- assistive moment
- assistive power
- metabolic reduction

Their HEI reward gives the best correlation with human experiments.

> [!note]
> The absolute values are not perfect, but the **trend reproduction** is the main goal.

## 3) Able-bodied optimization
For healthy gait, they optimize exoskeleton parameters over walking speed.

Main result:
- the **optimal delay decreases as walking speed increases**

## 4) Pathological gait
They test:
- equinus
- waddling
- crouch
- calcaneus
- foot drop

Main result:
- in **4 out of 5** pathologies, optimal gain changes approximately linearly with pathology severity
- foot drop is less stable because toe-ground collisions create too much variability

> [!interesting]
> This is maybe one of the most exciting claims of the paper:
> the framework is not just fit to healthy gait, but also used to explore **pathology-specific assistance trends**.

---

# Strengths

> [!success] **What is strong in this paper**
> - Strong **system-level idea**: RL + neuromechanical simulation + surrogate optimization
> - Explicit **sim-to-real matching**
> - Goes beyond plain imitation by modeling **adaptation to assistance**
> - Handles **pathological gait**, not only healthy locomotion
> - Surrogate optimization is smart and practical
> - The HEI reward is a neat attempt to encode human adaptation behavior
> - Useful for exploring exoskeleton design space **without expensive human trials**

# Weaknesses / limitations

> [!failure] **What is limited**
> - Still depends heavily on **hand-designed reward structure**
> - Exoskeleton controller itself is **very low-dimensional** (\(\kappa,\Delta t\)); this simplifies the problem a lot
> - Human controller is not truly learned from neural data
> - No direct modeling of **physiological neural control** or alpha-motor-neuron level mechanisms
> - Sim-to-real is validated mainly through **trend matching**, not full subject-specific validation
> - Some gait features remain unrealistic (e.g. hip internal rotation, rectus femoris overactivation)
> - Pathological generalization is promising, but still only simulation-based
> - Real patient validation is still missing

---

# Relation to my PhD

> [!question] **How relevant is this for my PhD?**
> Very relevant as a **methodological inspiration**, but not necessarily as the final direction.

## What I can reuse conceptually
- Reward / objective shaping to get **human-aligned locomotion**
- Using simulation to predict **adaptation to assistance**
- Surrogate optimization on top of RL-generated data
- Testing how controller parameters vary with **condition severity**
- Thinking carefully about **sim-to-real alignment**, not just policy success

## What is missing relative to my project
- No explicit **neural data-model fusion**
- No **alpha-motor neuron** / spiking neural control
- No real emphasis on **physiologically correct force control at the neural level**
- Human-like behavior is encouraged indirectly through reward engineering, not built from a neural model

> [!summary]
> So I would place Exo-Plore like this:
>
> - **Kinesis**: good for physiological locomotion generation / imitation-style locomotion control
> - **Exo-Plore**: good for **assistance optimization in a human-aligned simulator**
> - **My project**: probably needs to go deeper into **neural + musculoskeletal force control**, and maybe use ideas from both

---

# Short comparison with Kinesis

## Kinesis
- strong motion-imitation focus
- indirect muscle control through desired muscle lengths
- reference dataset is central
- optimized to reproduce physiological locomotion

## Exo-Plore
- not full-body motion imitation in the same sense
- structured gait controller + reward engineering
- focuses on **adaptation under exoskeleton assistance**
- adds a surrogate optimizer on top
- more directly useful for exoskeleton parameter search

> [!important]
> So Exo-Plore is closer to:
> **“simulate how humans adapt to assistance, then optimize assistance”**
>
> while Kinesis is closer to:
> **“learn to generate physiological locomotion from reference motion data”**

---

# Keywords / concepts to remember

- neuromechanical simulation
- exoskeleton optimization
- human-in-the-loop replacement in simulation
- sim-to-real matching
- human-exoskeleton interaction reward
- resistance minimization
- preferred walking speed matching
- surrogate model for CoT landscape
- pathological gait generalization
- structured locomotion control

---

# Questions / ideas for discussion

> [!todo]
> - Could the **HEI reward** be reformulated in a more mechanistic neuro-muscular way?
> - Instead of tuning reward terms to match experiments, could we learn this adaptation from **human neural / biomechanical data**?
> - Could a more expressive exoskeleton controller than just \((\kappa,\Delta t)\) still be optimized with this framework?
> - Could this framework be combined with **Kinesis-like motion priors** or with a **neural DHT**?
> - For my PhD, can this surrogate-optimization idea be reused once we have a more physiological simulator?

