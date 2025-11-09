# Connected Farms – AgriFusion360AI 🌱🤖

> **Connected Farms** is a digital agriculture platform that combines an **AI-powered farm monitoring engine (AgriFusion)** with a **real-time farmer network layer**.  
> The goal is simple: help farmers **see risks early, learn from each other, and make better decisions** using data and AI.

---

## 🚀 Overview

The platform is built around two tightly-integrated layers:

1. **AgriFusion (AI Engine – “Farm Brain”)**
   - AI-powered farm monitoring and decision support.
   - Detects water stress, pests, diseases, yield risks, and anomalies.
   - Generates actionable recommendations (irrigation, fertilizer timing, pest control, etc.).
   - Exposes a clean interface so the Java backend can call the Python AI service.

2. **Connected Farms (Network Layer – “Farmer Village”)**
   - Social + data layer where farmers **learn from each other**.
   - Real-time feed of:
     - AI alerts coming from AgriFusion.
     - Farmer posts, pictures, and local experiences.
   - Regional warnings based on **what is actually happening on nearby farms**.

This repository contains the **Spring Boot backend** for the platform (APIs, web layer, persistence, integration, and security).  
The **Python AI service** for AgriFusion can live in a separate repository and is integrated via HTTP or messaging.

---

## ✨ Key Features

### 🌍 Connected Farms (Java / Spring)

- Farmer & farm management
- Field and crop management (by season)
- Real-time regional feed (WebSocket-ready)
- Farmer posts, comments, and reactions
- Alerts and notifications (AI-generated + farmer-reported)
- Basic access control and security

### 🤖 AgriFusion AI Integration (Python)

> Implemented as an external microservice (planned/ongoing).

- Accepts farm + environment + history data from the Java backend
- Returns:
  - Farm/field **status**: `normal`, `stressed`, `at_risk`, `high_risk`
  - **Cause**: water stress, pest, disease, nutrient, unknown
  - **Risk score** (0–1)
  - **Recommended actions** (what the farmer should do next)
- Sends events back to the Java backend for:
  - Insights per farm
  - Regional warnings for the Connected Farms feed

---

## 🏗️ High-Level Architecture

```text
+-----------------------------+          +-----------------------------+
|         Mobile/Web UI       |  <-----> |    Connected Farms API      |
|  (Thymeleaf / REST client)  |          |   (Spring Boot + Security)  |
+-----------------------------+          +-----------------------------+
                                                   |
                                                   | JPA/Hibernate
                                                   v
                                         +----------------------+
                                         |   MySQL Database     |
                                         | connected_farms_db   |
                                         +----------------------+
                                                   ^
                                                   |
                                           REST / HTTP (JSON)
                                                   |
+-----------------------------+          +-----------------------------+
|  AgriFusion AI Service      |  <-----> |   Integration Layer         |
| (Python – FastAPI/Flask)    |          | (WebClient / Integration)   |
+-----------------------------+          +-----------------------------+
