---
layout: page
title: Autonomous Drone Fleet Simulator
description: A high-fidelity 3D simulation for modeling collective quadrotor drone behavior.
img:
importance: 3
category: Side Projects
---

### Introduction

The **Autonomous Drone Fleet Coordination Simulator** is a high-fidelity 3D simulation platform designed to model the collective behavior of multiple quadrotor drones. This project explores how object-oriented programming concepts—such as encapsulation, modularity, inheritance, and composition—can be used to build complex interacting systems from simpler components. 

The simulator provides a safe and efficient way to test coordination and control algorithms, such as formation maintenance and collision avoidance, before hardware implementation in fields like precision agriculture and environmental research.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/dronesimulator_uml.webp" title="System Architecture UML" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The system architecture follows a modular, object-oriented design where each class represents a distinct physical or computational process.
</div>

### Key Features

* **Autonomous Agent Modeling**: Each drone operates as an independent autonomous agent capable of movement, target tracking, and communication.
* **Physics-Based Dynamics**: Implements translational and rotational motion using discrete-time Newtonian dynamics and Euler’s rotational equations.
* **Decentralized Coordination**: Features consensus-based formation control where drones adjust motion based on local neighbor information.
* **Safety Protocols**: Utilizes repulsive potential fields to prevent drone overlap and ensure collision avoidance.
* **Stochastic Communication**: Models radio-based data exchange, incorporating spatial distance constraints and probabilistic signal loss.
* **Performance Analytics**: Calculates system-level metrics including average spacing, collision counts, and communication success rates.

---

### Core Components

| Class | Description |
| :--- | :--- |
| **Simulator** | The core numerical engine managing time steps, numerical integration, and synchronization. |
| **Drone** | Encapsulates physical parameters (mass, inertia, drag) and bridges the physical model with the software environment. |
| **Controller** | Implements PID/PD control laws to convert positional discrepancies into thrust and torque. |
| **FormationManager** | Maintains spatial coordination and relative positioning within a group. |
| **CollisionAvoidance** | Applies repulsive forces to prevent inter-drone collisions. |

---

### Mathematical Foundations

The simulator relies on several mathematical models to ensure realism. You can see how these equations are integrated into the Java logic for real-time calculation:

$$ma = mg + RT + F_{aero} + F_{rep} + F_{form}$$

$$\dot{\omega} = I^{-1}(\tau - \omega \times (I\omega))$$

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        <div class="card p-3">
            <strong>Technical Specifications:</strong>
            <ul>
                <li><strong>Language:</strong> Java, JSwing(GUI)</li>
                <li><strong>Model:</strong> Discrete-time steps ($\Delta t$)</li>
                <li><strong>Physics:</strong> Newton-Euler equations</li>
                <li><strong>Outputs:</strong> CSV (telemetry) and TXT (summaries)</li>
            </ul>
        </div>
    </div>
</div>
<div class="caption">
    System specifications and mathematical implementation details.
</div>

## 🔗 Link to the CodeBase:

You can explore the source code, view the project structure, or contribute to the development here:  
👉 **[GitHub: Drone_Simulator](https://github.com/HopzAlot/Drone_Simulator)**

---