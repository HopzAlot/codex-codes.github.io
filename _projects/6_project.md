---
layout: page
title: Seedha Rasta
description: Fuel-Optimized Intelligent Routing Engine.
img:
importance: 1
category: Side Projects
giscus_comments: false
---

## 🚗 Seedha Rasta — Fuel-Optimized Routing Engine

**Seedha Rasta** is a full-stack intelligent routing system that optimizes travel routes based on **fuel consumption** rather than just distance or time. It integrates traffic-aware modeling, vehicle-specific profiles, and cost functions to generate more realistic and economical routes.

The project tackles a real-world inefficiency in traditional navigation systems by accounting for **idle fuel consumption in traffic**, making route recommendations smarter and more practical.

---

## 🧠 System Overview

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/seedharasta.png" title="Seedha Rasta" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Visual display of what the system looks like along with its route recommendation method.
</div>

- **Frontend:** React with an interactive 2D route visualizer
- **Backend:** Django + Django REST Framework
- **Routing Engine:** Custom graph-based system using A* algorithm
- **Graph Source:** OpenStreetMap (OSM) processed via OSMnx
- **Database:** PostgreSQL
- **Caching (planned/extendable):** Redis
- **Containerization:** Docker + Docker Compose
- **CI/CD:** GitHub Actions pipeline
- **Deployment:** AWS ECS (Elastic Container Service)

---

## ⚙️ Core Concept

Unlike traditional navigation systems, Seedha Rasta minimizes:

> **Fuel Cost = Distance Fuel + Idle Fuel (Traffic-Based)**

Where:
- Idle time is derived from traffic intensity
- Fuel consumption varies based on vehicle profile

---

## ✨ Key Features

- ⛽ **Fuel-optimized routing** using a custom cost function  
- 🧠 **A\* pathfinding algorithm** with multi-factor optimization  
- 🚦 **Traffic-aware modeling** (idle time = traffic × travel time)  
- 🚗 **Vehicle profiles** (car, sedan, SUV with configurable mileage)  
- 📍 **Nearest node mapping** using geographic coordinates  
- 🗺️ **Interactive frontend visualization** of routes  
- 🔄 **Comparison of shortest vs fuel-efficient paths**  
- 🧪 **Preprocessed graph with speed, distance, and travel time metadata**  

---

## 🔄 How It Works

1. **User Input**  
   - Start & destination coordinates  
   - Vehicle type (e.g., car, SUV, sedan)

2. **Graph Processing**  
   - Road network loaded from OpenStreetMap  
   - Nodes and edges enriched with:
     - Distance
     - Speed
     - Travel time

3. **Traffic Modeling**  
   - Traffic factor (0–1) assigned to edges  
   - Idle time derived dynamically

4. **Pathfinding Execution**  
   - Shortest path (distance-based)  
   - Fuel-optimized path (custom cost function)

5. **Fuel Calculation**  
   - Distance fuel + idle fuel computed per route  

6. **Visualization Output**  
   - Both routes rendered on frontend  
   - Metrics displayed:
     - Fuel cost
     - Time
     - Distance  

---

## 🐳 DevOps & Deployment

- **Dockerized Architecture**
  - Separate containers for backend and services
  - Optimized multi-stage builds

- **CI/CD Pipeline (GitHub Actions)**
  - Automated build and test workflows
  - Image creation and validation

- **AWS Deployment**
  - Hosted using **Elastic Container Service (ECS)**
  - Scalable container orchestration
  - Production-ready infrastructure setup

---

## ⚠️ Current Limitations

- Traffic modeling is simulated (not real-time yet)  
- Fuel estimation is approximate (based on averages)  
- Limited geographic dataset depending on OSM extraction  

---

## 🔮 Future Improvements

- Real-time traffic integration (e.g., APIs)  
- Machine learning-based traffic prediction  
- More granular vehicle tuning (engine types, fuel types)  
- Mobile-friendly UI and map enhancements  
- Route caching and performance optimizations  

---

## 🔗 Link to the CodeBase:

You can explore the source code, system design, or contribute here:  
👉 **[GitHub: Seedha Rasta](https://github.com/HopzAlot/Seedha-Rasta)**

---

## 🤝 Contribution & Contact

This project is actively evolving.  
If you’re interested in collaborating or discussing system design:

**Email:** rehasaqib2006@gmail.com