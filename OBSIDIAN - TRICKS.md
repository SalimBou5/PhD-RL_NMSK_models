<progress value="75" max="100"></progress>PROGRESS BAR

<mark style="background: #FF5555; color: white">CRITICAL</mark>

> [!Overall Objective]+


> [!SUCCESS] Key Advantages
> * **High Agility:** Replicates MSK structures.
> * **Efficiency:** Powered by `EFAs`.
> * **Force Control:** Neuromechanically-inspired.

> [!ABSTRACT] 

>[!tip]

> [!faq]


>[!danger]


>[!missing]

> [!FAILURE] 
> FAILURE

> [!QUESTION]
> qUESTION

>[!BUG]
>BUG

>[!EXAMPLE]-
>EXAMPLE

> [!TODO]

> [!cite] "A robotic evolution that uses artificial muscles combined with an articulated skeleton."

> [!MATH] Optimization Function
> $$\min_{u} \int_{0}^{T} L(x, u) dt$$

> [!FIG] Figure 1: MSK Architecture
> ![[your-robot-image.jpg]]

> [!QUOTE]

> [!IMPORTANT] 
>- the `+` and `-` are powerful. If you have a document with 10 technical specs, put each one in a `> [!INFO]-` box. This creates an **Accordion** effect where the reader only opens the parts they want to read
>- You aren't stuck with the names above. You can use the **style** of one callout but give it a **custom title**. Ex: [!abstract] Executive Summary: The MSK Evolution
>- https://obsidian.md/help/callouts

>[!miscellaneous]-
>Create Your Own (No Plugins): If you want a callout that doesn't exist (like one specifically for **"Robot Spec"**), you can create it with a tiny bit of CSS.
>1. Go to **Settings > Appearance > CSS Snippets**.
>2. Click the folder icon and create a file called `custom-callouts.css`.
>3. Paste this in: 
>   .callout[data-callout="robot"] {    --callout-color: 0, 191, 255; /* Deep Sky Blue */--callout-icon: lucide-bot;   /* Uses a robot icon */    }
>4. - Enable the snippet in Obsidian.
>5. **Usage:** `> [!robot] New Actuator Spec`

> [!ABSTRACT] Goal: Human-level Agility
> Replicating the MSK system requires two things:
> > [!INFO] 1. Hardware
> > Electrofluidic Actuators (EFAs)
> 
> > [!INFO] 2. Software
> > Neuromechanically-inspired controllers


|                                                            |                                                                         |
| :--------------------------------------------------------- | :---------------------------------------------------------------------- |
| > [!SUCCESS] Advantages <br> - High DoF <br> - Lightweight | > [!FAILURE] Current Limits <br> - Power density <br> - Heat management |





> [!TODO] **Next Milestones**
> - [x] Define EFA parameters
> - [/] Prototype musculoskeletal leg    
> - [ ] Test force-control algorithm

<details>
  <summary><strong>XYZ</strong></summary>
kcnk
</details>


# Mermaid : WOW🤯!!!!!
- **TD/TB:** Top Down
- **LR:** Left to Right
```mermaid
graph LR
    A[Neural Signal] --> B(EFA Actuators)
    B --> C{Hybrid Robot}
    C --> D[Agility]
```

```mermaid
sequenceDiagram
    Alice->>+John: Hello John, how are you?
    Alice->>+John: John, can you hear me?
    John-->>-Alice: Hi Alice, I can hear you!
    John-->>-Alice: I feel great!
```

```mermaid
graph TD
    A[Neural Signal] -->|Input| B(EFA Controller)
    B --> C{Force Check}
    C -- High --> D[Reduce Voltage]
    C -- Low --> E[Increase Pressure]
    
    style B fill:#f96,stroke:#333
    style C fill:#fff,stroke:#f66,stroke-width:3px
```

```mermaid
sequenceDiagram
    participant S as Sensor
    participant C as Controller
    participant A as EFA Actuator
    
    S->>C: Send Proprioception Data
    Note over C: Calculate Inverse Kinematics
    C->>A: Pulse Width Modulation (PWM)
    A-->>C: Acknowledge Force Applied
    C->>S: Request Next Frame
```
```mermaid
gantt
    title MSK Robot Development 2026
    dateFormat  YYYY-MM-DD
    section Design
    Skeleton CAD          :a1, 2026-01-01, 30d
    EFA Integration       :after a1, 20d
    section Testing
    Locomotion Trials     :2026-03-01, 15d
    Force Feedback Tuning :active, 10d
```
```mermaid
stateDiagram-v2
    [*] --> Idle
    Idle --> Walking : Command Received
    Walking --> Falling : Balance Loss
    Falling --> Recovery
    Recovery --> Idle
    Walking --> Idle : Stop
```

```mermaid
---
config:
  look: handDrawn
  theme: neutral
---
graph LR
    Start --> Brain --> Motion
```

```mermaid
graph LR
    A[Motor] --- B[Gear]
    classDef hardware fill:#f9f,stroke:#333,stroke-width:2px;
    class A,B hardware;
```

```mermaid
pie title Robot Weight Distribution
    "Actuators (EFAs)" : 45
    "Skeleton (Carbon Fiber)" : 25
    "Battery/Electronics" : 20
    "Sensors" : 10
```

```mermaid
journey
    title Robot Crossing Uneven Terrain
    section Perception
      Identify Obstacle: 5: Robot
      Calculate Path: 3: Controller
    section Action
      Engage EFAs: 5: Robot
      Maintain Balance: 4: Robot
    section Goal
      Reach Target: 5: Success
```
