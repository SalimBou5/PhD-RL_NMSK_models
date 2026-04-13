
> [!Overall Objective]+
We develop and optimize a new generation of autonomous robots that replicate the architecture and actuation structure of the human musculoskeletal (MSK) system, equipped with robust, neuromechanically-inspired force controllers i.e., outperforming 
rigid robots in real-life locomotion and manipulation.
>
**a robotic evolution that uses artificial muscles combined with an articulated skeleton, as force-controlled by neural mechanisms as seen in animals and humans**
>
**==$\Rightarrow$ Here, we propose to create the first computationally optimized hybrid rigid-soft robot powered by electrofluidic actuators (EFAs) withboth a denser degree of freedom (DoF) and more agility than any other robot to date==**

Human-inspired neural control policies and rapid ML-based robot optimization will require an interdisciplinary collaboration that brings in a deep comprehension of the human neuro-MSK system and hybrid rigid-soft robotic system design.

>[!ABSTRACT]  **Learn human-like force control at the neural level $\implies$ generate policies $\implies$ transfer to robots**
>So the final final goal is to develop a robot that moves in a biologically plausible way somehow and to do so, there are 2 parts hardware (not my task) and software (control). So we need to learn how to move in a biologically plausible way $\implies$ We need a simulation on which we can train the policies and since we want it as biologically plausible as possible we need to go deeper than just the muscle level, we want to go to the level of the alpha motor unit (**neurons**). 
> > [!todo] **My tasks (or partly) I think**:
> >  $\implies$ We need a good understanding of how the human nervous system commands mechanical force. This would provide the highest temporal and spatial resolution for robust force control which is critical to develop control strategies for MSK robots. $\implies$ **Data-model fusion approach** will gives us in-vivo characterization of how limb movements are controlled in the muscle-force domain by the $\alpha$-motor neurons **(Task 5)** $\implies$ This in-vivo characterization will enablebuilding numerical models of how spinal neurons control complex, non-linear MSK systems (spiking NN for the control of MSK force) **(Task 6)**  
> >As soon as we have this simulation, it can be integrated within a new RL framework based on myosuite and MSK motor control.
> > >[!success] RL-powered simulations will reproduce not only realistic kinematic data but also the underlying MSK impedances (the movements and environment interactions will be based on realistic values for joint torque, stiffness, damping and force.) **(Task 7)**
> > > >[!failure] If DHT (Digital Human Twin) does not achieve realistic movements, simplify the amount of neuro-mechanical variables to be reproduced in-silico (simulation of realistic joint impedance properties (torque, stiffness) is prioritized)
> >
> > Of course, it should be robust to perturbations and will be state- and environment- dependent.  
> > ![[Pasted image 20260410165009.png]]
> 
> The whole purpose of this is I think to create a scalable thing, we don't want to have a model adapted to a specific robot. So we want to have a model working on a DHT that we would then transfer to anz robot provided that we have a model linking the robot to our DHT. and for the HASEL specifically, this is **WP3**.



### My task in all this : (c) a novel neuro-mechanical modeling approach that uses (d) RL and machine learning-based co-optimization for synthesizing key human neuro-muscular mechanisms of movement control into robot’s design and force-control schematics.

Tasks in which I may be involved:
- WP2: Neural data-driven MSK modeling: The central idea is to drive the in silico central nervous system (CNS) framework using in vivo neuro-muscular recordings including decoded α-motor neuron discharges [114, 139], and estimation of skeletaljoint forces using inverse dynamics facilitated by data recorded with wearable force sensors and inertial measurement units 
  > [!INFO] I am not sure I would be involved in this but it would definitely be involved in this to understand what lies behind the model 

```mermaid
flowchart TB
  %% =========================
  %% STYLES
  %% =========================
  classDef you fill:#dbeafe,stroke:#2563eb,stroke-width:2px,color:#111827;
  classDef core fill:#dcfce7,stroke:#16a34a,stroke-width:1.5px,color:#111827;
  classDef support fill:#f3f4f6,stroke:#6b7280,stroke-width:1px,color:#111827;
  classDef output fill:#ede9fe,stroke:#7c3aed,stroke-width:1.5px,color:#111827;
  classDef real fill:#e0f2fe,stroke:#0284c7,stroke-width:1.5px,color:#111827;

  %% =========================
  %% YOUR CORE: WP2
  %% =========================
  subgraph WP2["WP2 — Your core PhD space"]
    direction TB

    A["Human experimental data<br/>HD-EMG, motor unit discharges, kinematics,<br/>joint forces, IMUs"]
    B["T5 — Neural-data driven modeling<br/><b>PhD2</b><br/>Build subject-specific neural/MSK representations"]
    C["T6 — Neuro-MSK system model<br/>PD1<br/>Reflexes, neural pathways, spiking NN,<br/>feedforward + feedback models"]
    D["T7 — RL for Digital Human Twins<br/><b>PhD2 + PD1 + SE</b><br/>Train RL policies on neuro-MSK models<br/>for locomotion/manipulation"]
    
    E["Digital Human Twin (DHT)<br/>Neuro-mechanically consistent human model"]
    F["Your main scientific output<br/>Human-like control policies<br/>+ realistic movement/impedance"]

    A --> B
    B --> E
    C --> E
    E --> D
    D --> F
  end

  class A,B,C,D,E,F core
  class B,D you

  %% =========================
  %% WHERE WP2 GOES NEXT
  %% =========================
  G["WP3 interface<br/>Robot Digital Twin / robot-side control stack"]
  H["WP4 interface<br/>Design co-optimization uses simulated performance"]
  I["T15 — Sim-to-real control<br/><b>PhD2 + PD1 + PD2 + SE</b><br/>Transfer learned control to real systems"]
  J["Real-world systems<br/>MSK robot / exoskeleton / human-in-the-loop setting"]

  class G,H support
  class I you
  class J real

  %% Main links outward
  E --> G
  F --> G
  F --> H
  F --> I
  G --> I
  I --> J

  %% =========================
  %% LIGHT CONTEXT ONLY
  %% =========================
  K["WP1<br/>Artificial muscles / actuators"]
  L["WP5 hardware integration<br/>Physical robotic platforms"]

  class K,L support

  K --> G
  L --> I
```


```mermaid
flowchart TB

%% =========================
%% COLOR DEFINITIONS
%% =========================
classDef goal fill:#fde68a,stroke:#f59e0b,stroke-width:2px,color:#111827;
classDef you fill:#bfdbfe,stroke:#2563eb,stroke-width:2px,color:#111827;
classDef dependency fill:#bbf7d0,stroke:#16a34a,stroke-width:2px,color:#111827;
classDef support fill:#e5e7eb,stroke:#6b7280,stroke-width:1px,color:#111827;
classDef output fill:#ddd6fe,stroke:#7c3aed,stroke-width:2px,color:#111827;
classDef real fill:#bae6fd,stroke:#0284c7,stroke-width:2px,color:#111827;

%% =========================
%% FINAL GOAL
%% =========================
G["🎯 FINAL GOAL<br/>Human-like MSK robot control<br/>Biologically plausible movement"] 
class G goal

%% =========================
%% YOUR CORE PIPELINE
%% =========================
subgraph YOU["🧠 YOUR PHd CORE (WP2 + T15)"]
direction TB

A["Human data<br/>EMG, motor units, kinematics"] 
B["T5 — Data-model fusion<br/><b>You</b><br/>Map neural signals → muscle force"]
C["T6 — Neuro-MSK model<br/>Spiking NN, reflexes<br/>(PD1 — you depend on it)"]
D["Digital Human Twin (DHT)<br/>Neuro-mechanical simulation"]
E["T7 — RL training<br/><b>You</b><br/>Learn human-like control policies"]
F["Human-like policies<br/>+ realistic impedance (torque, stiffness, damping)"]
end

class A,B,E you
class C dependency
class D,F output

%% Flow inside your PhD
A --> B --> D
C --> D
D --> E --> F

%% =========================
%% LINK TO ROBOT SIDE
%% =========================
R1["WP3 — Robot Digital Twin<br/>+ actuator modeling (HASEL)<br/>Bridge human ↔ robot"]
class R1 support

F --> R1

%% =========================
%% SIM TO REAL (YOU AGAIN)
%% =========================
S["T15 — Sim-to-real transfer<br/><b>You</b><br/>Robustness, adaptation,<br/>domain randomization"]
class S real

R1 --> S
F --> S

%% =========================
%% FINAL SYSTEM
%% =========================
Real["Real robot / exoskeleton<br/>+ human interaction"]
class Real real

S --> Real
Real --> G

%% =========================
%% HARDWARE (CONTEXT ONLY)
%% =========================
H["WP1 + WP5 hardware<br/>Artificial muscles + robot body"]
class H support

H --> R1
```

```mermaid
flowchart TB

%% =========================
%% COLOR DEFINITIONS
%% =========================
classDef goal fill:#fde68a,stroke:#f59e0b,stroke-width:2px,color:#111827;
classDef you fill:#bfdbfe,stroke:#2563eb,stroke-width:2px,color:#111827;
classDef dependency fill:#bbf7d0,stroke:#16a34a,stroke-width:2px,color:#111827;
classDef output fill:#ddd6fe,stroke:#7c3aed,stroke-width:2px,color:#111827;
classDef support fill:#e5e7eb,stroke:#6b7280,stroke-width:1px,color:#111827;
classDef real fill:#bae6fd,stroke:#0284c7,stroke-width:2px,color:#111827;

%% =========================
%% FINAL GOAL (CENTER)
%% =========================
G["🎯 FINAL GOAL<br/>Biologically plausible MSK robot control"]
class G goal

%% =========================
%% YOUR PIPELINE (SEQUENTIAL)
%% =========================
subgraph CORE["🧠 YOUR CORE PIPELINE (WP2)"]
direction TB

A["Human data<br/>HD-EMG, motor units, kinematics"]
B["T5 — Data-model fusion<br/><b>You</b><br/>Neural → muscle-force mapping"]
C["T6 — Neuro-MSK model<br/>Spiking NN, reflexes (PD1)"]
D["Digital Human Twin (DHT)"]
E["T7 — RL training<br/><b>You</b><br/>Learn human-like control"]
F["Policies<br/>Realistic force & impedance"]

A --> B --> C --> D --> E --> F
end

class A,B,E you
class C dependency
class D,F output

%% =========================
%% ROBOT INTERFACE (WP3)
%% =========================
R["WP3 — Robot Digital Twin<br/>Map human control → robot (HASEL, actuators)"]
class R support

F --> R

%% =========================
%% SIM2REAL (YOU AGAIN)
%% =========================
S["T15 — Sim-to-real transfer<br/><b>You</b><br/>Robustness, adaptation"]
class S real

F --> S
R --> S

%% =========================
%% REAL SYSTEM
%% =========================
Real["Real robot / exoskeleton"]
class Real real

S --> Real
Real --> G

%% =========================
%% HARDWARE CONTEXT
%% =========================
H["WP1 + WP5 hardware<br/>Muscles + robot body"]
class H support

H --> R
```
## Task 5
This is the **data → model step**

- Input:
	- HD-EMG
	- motor neuron discharges
	- kinematics
- Output:
	- motor unit properties
	- neural activation patterns

💡 This is:  
👉 **data-model fusion**

## Task 6
This is the **structured neuro-control model**

- spiking neural network
- sensory + motor neurons
- feedback loops
- reflexes

BUT — and this is important:

👉 It is **built using outputs from T5**

## 🧩 T5 gives:

👉 **what the system does (data)**

## 🧩 T6 builds:

👉 **a system that can reproduce that behavior**

```mermaid
flowchart LR
  A["T5 (you)<br/>Data-driven modeling<br/>Motor units, EMG, neural signals"]
  B["T6 (PD1)<br/>Neuro-MSK model<br/>Spiking NN + reflexes"]
  C["DHT<br/>Digital Human Twin"]
  D["T7 (you)<br/>RL training"]

  A --> B --> C --> D
```
