Simos, Merkourios, Alberto Silvio Chiappa, and Alexander Mathis. “Reinforcement Learning-Based Motion Imitation for Physiologically Plausible Musculoskeletal Motor Control.” arXiv:2503.14637. Version 1. Preprint, arXiv, March 18, 2025. [https://doi.org/10.48550/arXiv.2503.14637](https://doi.org/10.48550/arXiv.2503.14637).
>[!tip] Important prerequisite (NICHT SICHER)
>- **Motion imitation** = trial & error (RL) + reward = “stay close to reference motion”
>- **Imitation learning** (behavioral cloning) = copy actions from dataset directly


>[!example] ## Techniques to address challenges in motion imitation
>Solutions to problems posed by the richness of the motion datasets :
>- Reference state initialization
>- Early termination
>- Progressive learning regimes 
>
>Other techniques:
>- Adversarial learning to shape motion behavior
>- latent representations to capture  the relationships between states, goals and actions (recent)
>
>*(References in the paper)*

>[!quote] **Datasets**
>- **KIT Motion Dataset**: KIT is a collection of MoCap datasets under a unified representation framework, containing a variety of full-body motor skills. 
>- **KIT-locomotion**: locomotion-specific subset of 1084 reference motion sequences extracted from **KIT motion dataset** representing 1.9 hours of MoCap data. KIT-Locomotion consists of five broad locomotion skills: (1) walk, in which the subject walks forward at varying speeds, (2) gradual turn, in which the subject turns to the left or right while walking, (3) turn in place, where the subject walks in a straight line, then performs a U-turn and resumes walking, (4) walk backwards, where the subject takes backward steps, and (5) run, where the subject breaks into a short running stride. We extracted all motions from the AMASS dataset [41], which use the SMPL-H body format [38].

# RL framework
- >[!Observation space] 
	>$o_t \in \mathbb{R}^{309} \implies$ **309 !!!!!!!!!!!!!** and only describing proprioceptive states. It includes:
	>- **the joint kinematics** (position, velocity, angular position and angular velocity for each joint, 234 values), 
	>- **the feet contact forces** (4 values) at time t
	>- **the target reference pose** (absolute and relative, 63 values) at time t + 1. (*We have this from the dataset $\implies$ Big advantage of motion imitation*) 

- >[! Control signal] 
	>action $a_t \in A \subset  \mathbb{R}^{80}$
	>- **Indirect control scheme: ==$a_t$ represents the desired next-step muscle lengths==**. 
	>- A **PD-controller** is responsible to find the force which a muscle should apply for the joint to reach the desired position following this equation $$F_i = F_i^{peak}(k_p(a_i-l_i)-k_dv_i)$$ where i denotes the muscle index, F peak denotes the maximum force that the muscle can exert, l denotes the current muscle length, v denotes current muscle velocity, while kp and kd are scaling factors that are empirically determined and remain fixed. 
	>- To **convert muscle force into muscle activity** $m_t \in \mathbb{R}^{80}$, we use the force-length-velocity relationship: $$m_i = \frac{F_i-F_P(l_i)}{F_L(l_i)F_V(v_i)}$$ where $F_P$ denotes the force exerted by the muscle’s passive element as a function of its length, $F_L$ denotes the active isometric force exerted by the muscle as a function of its length, and $F_V$ denotes the active force exerted by a maximally activated muscle at its resting length, as a function of its velocity. These parameters are provided in MyoLeg.
	> - The simulation runs at 150 Hz, while the control policy runs at 30 Hz, meaning that the control signal is repeated for 5 simulation frames.

- >[!Reward]  
	>- ### Imitation component: 
	>	- In a motion imitation task, the goal is to reproduce the target movement as faithfully as possible. 
	>	- The **imitation component** is usually defined as **the sum of the negative Euclidean distance between the body’s joint position vector $p^i_t$ and the position vector $\hat{p}^i_{t+1}$ of the target pose:**  $$r_t^{pos}=exp(-k_{pos}\sum_i||p_t^i - \hat{p}^i_{t+1}||)$$
	>	- The joints included in the sum are the pelvis, the knees, the ankles, and the toes. 
	>	- an exponential kernel to make the rewards positive and avoid encouraging early termination
	>	- The scaling factor $k_{pos}$ controls the rate at which the reward decreases with higher distance from the target motion frame
>
> - ### Velocity reward : 
>	$$r_t^{vel}=exp(-k_{vel}\sum_i||v_t^i - \hat{v}^i_{t+1}||)$$where $v_t^i$ and $\hat{v}^i_{t+1}$ denote the joint velocities of the same keypoints for the skeleton and target pose.
>	
>  - ### Energy regularization:
> > [!abstract] Avoid co-activation
> > Since the musculoskeletal model is overactuated, with antagonist muscles controlling the same joints, there are infinite sequences of muscle activation that produce the same motion. To distinguish between these solutions and discourage simultaneous co-activation of antagonist muscles, we add **L1 and L2 regularization** in terms of the expended energy at each time step through the reward component: $$r_t^e = -||m_t|| - ||m_t||_2$$
> $m_t$ is the vector containing the muscle activation signals for time step t
>
 > - ### Upright reward component : 
	 because the upper body is not tracked and because we want to encourage stable posture :$$r_t^{up}=exp(-k_{up}(\theta_{f,t}^2 - \theta_{s,t}^2))$$ where $\theta_{f,t}$ and $\theta_{s,t}$ denote the forward and sideways tilt of the pelvis in radians, at time step t.
>    ---
>
> The total reward at each time step is a weighted sum of these four components


- > [!Training] 
>- For training, we apply the motion imitation approach by Luo et al., using a training set D with reference motion sequences, including global translation and local joint rotations. 
  >> [!todo] ## TODO !!!! VERY IMPORTANT 
> >Zhengyi Luo, Jinkun Cao, Kris Kitani, Weipeng Xu, et al. Perpetual humanoid control for real-time simulated avatars. In Proceedings of the IEEE/CVF International Conference on Computer Vision, pages 10895–10904, 2023. 1, 2, 4, 5, 6, 13
>
>  - Training episodes begin by sampling a random reference motion from D and a random starting frame, setting the musculoskeletal model to the reference state, and initiating environment dynamics.
>  - **Early termination** if any tracked keypoint deviates more than 0.15 meters from its reference
>  
> > [!Danger] ### Model architecture:
> > - ==6-hidden layer [2048, 1536, 1024, 1024, 512, 512] multilayer perceptron (MLP)==
> > - Same architecture for policy function and value function 
> >  - PPO and Lattice (exploration method adapted for high-dimensional control)
> > 	 - Lattice is used to increase the efficiency of policy exploration and to avoid scenarios where random perturbations in different actuators cancel each other out
>>  	 - Lattice is a method to inject noise into the latent state of the policy network, so that the learned correlations across actuators shape the policy’s covariance matrix, promoting more effective exploration.
> >  - **Hard negative mining:** Second reason why to read Luo et al ![[Pasted image 20260413185252.png]]
> >  - **Mixture of experts (MoE):** It's a model used combine to the policy networks obtained from hard mining. It's trained on the entire training set. The MoE network is a three-layer [1024, 512, 256] MLP that takes as input the current observable state and outputs a vector abt ∈ Rn that determines which expert’s output will be used to actuate the model. We use a softmax transformation so that abit denotes the probability of using the i’th expert’s output as the selected action. Essentially, the MoE network acts as a gating mechanism that stochastically selects an expert based on the current observations.
> 
>  - We used 128 parallel environment instances, training the first expert for 10’000 epochs, the two subsequent experts for 5’000 epochs, and 1’800 epochs for the gating network (MoE). Each epoch consisted of experience accumulation for 51’200 steps gathered in total from all environments, followed by 10 rounds of policy optimization using PPO.
	
	 
## EVALUATION
Very interesting
![[Pasted image 20260413192340.png]]
**Synergies** $\implies$ instead of the common signal reconstruction metric, it is much more informative to quantify the dimensionality of a control signal by the number of components that are required to retrieve full task performance