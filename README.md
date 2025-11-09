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
```

- **Spring Boot** manages:
  - Web APIs, WebSocket feed, security, persistence with MySQL.
- **Python** AI microservice handles:
  - Model loading, inference, ML logic.
- **Flyway** manages schema evolution for `connected_farms_db`.

---

## 🧰 Tech Stack

### Backend (this repo)

- **Java 17**  
- **Spring Boot 3.5.7**
  - Spring Web (REST)
  - Spring Data JPA
  - Spring Security
  - Spring Validation
  - Spring WebFlux (for `WebClient` to call Python AI)
  - Spring WebSocket
  - Spring Integration (HTTP, JPA, WebFlux, WebSocket)
  - Spring Boot Actuator
  - Spring Boot Starter Thymeleaf
- **MySQL** – `connected_farms_db`  
- **Flyway** – database migrations  
- **Lombok** – reduce boilerplate  
- **Gradle** – build system  

### AI Microservice (planned / separate repo)

- Python 3.x  
- FastAPI or Flask  
- ML stack (e.g. Scikit-learn / XGBoost / PyTorch / TensorFlow)  
- Pandas, NumPy  

---

## 📁 Project Structure (planned / indicative)

```text
src/main/java/com/connected
 └── farms
     ├── ConnectedFarmsApplication.java
     ├── config/           # Security, WebSocket, WebClient config
     ├── farm/             # Farm, Field, Crop, Season domain
     ├── farmer/           # Farmer accounts, profiles, roles
     ├── feed/             # Connected Farms feed, posts, comments
     ├── alert/            # Alerts & notifications
     ├── insight/          # AI insights & events from AgriFusion
     ├── integration/      # Clients and flows to Python AI service
     ├── common/           # Shared DTOs, utils, exceptions
     └── security/         # Auth, authorization, filters
```

```text
src/main/resources
 ├── application.properties
 ├── db/migration/         # Flyway migrations (V1__init.sql, V2__...sql)
 └── templates/            # Thymeleaf templates (dashboard, feed, etc.)
```

---

## ⚙️ Getting Started

### 1️⃣ Prerequisites

- JDK 17+  
- Gradle (or use the provided Gradle wrapper)  
- MySQL 8+  
- Git  
- (Optional) Docker  

### 2️⃣ Clone the Repository

```bash
git clone https://github.com/<your-username>/<your-repo-name>.git
cd <your-repo-name>
```

### 3️⃣ Database Setup (MySQL)

Create the database:

```sql
CREATE DATABASE connected_farms_db
    CHARACTER SET utf8mb4
    COLLATE utf8mb4_unicode_ci;
```

Create a dedicated user (optional but recommended):

```sql
CREATE USER 'connected_user'@'%' IDENTIFIED BY 'strong_password';
GRANT ALL PRIVILEGES ON connected_farms_db.* TO 'connected_user'@'%';
FLUSH PRIVILEGES;
```

### 4️⃣ Application Configuration

Edit `src/main/resources/application.properties`:

```properties
# Server
server.port=8080

# Datasource
spring.datasource.url=jdbc:mysql://localhost:3306/connected_farms_db?useSSL=false&serverTimezone=Africa/Harare
spring.datasource.username=connected_user
spring.datasource.password=strong_password
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# JPA / Hibernate
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.open-in-view=false
spring.jpa.database-platform=org.hibernate.dialect.MySQLDialect

# Thymeleaf
spring.thymeleaf.enabled=true
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html
spring.thymeleaf.mode=HTML
spring.thymeleaf.encoding=UTF-8
spring.thymeleaf.cache=false

# Flyway
spring.flyway.enabled=true
spring.flyway.locations=classpath:db/migration
```

> 🔁 In production, prefer **Flyway migrations** over `ddl-auto=update`.

### 5️⃣ Build & Run

Using Gradle:

```bash
./gradlew clean build
./gradlew bootRun
```

Or run the jar:

```bash
./gradlew clean bootJar
java -jar build/libs/connected-farms-0.0.1-SNAPSHOT.jar
```

The application will be available at:  
[`http://localhost:8080`](http://localhost:8080)

---

## 🧬 Gradle Build (Summary)

Key dependencies (configured in `build.gradle`):

- `spring-boot-starter-web`  
- `spring-boot-starter-data-jpa`  
- `spring-boot-starter-security`  
- `spring-boot-starter-validation`  
- `spring-boot-starter-actuator`  
- `spring-boot-starter-webflux`  
- `spring-boot-starter-websocket`  
- `spring-boot-starter-thymeleaf`  
- `spring-boot-starter-integration`  
- `com.mysql:mysql-connector-j`  
- `flyway-core`, `flyway-mysql`  
- `lombok`  
- Spring Boot test starters  

---

## 🔗 AgriFusion AI Integration (Concept)

The Java backend communicates with the Python AI service via REST.

### Example Request (Java → Python AI)

```http
POST /api/v1/analyze/farm
Content-Type: application/json
```

**Payload:**

```json
{
  "farmId": 123,
  "crop": "maize",
  "stage": "vegetative",
  "weather": { "rain_mm": 2.1, "temp_c": 32 },
  "soil": { "moisture": 18.5 },
  "history": {
    "yields": [3.2, 4.1],
    "planting_dates": ["2023-11-10"]
  }
}
```

**Example response:**

```json
{
  "status": "at_risk",
  "cause": "water_stress",
  "riskScore": 0.82,
  "actions": [
    "Irrigate field A within the next 24 hours",
    "Delay nitrogen top dressing until moisture improves"
  ]
}
```

The Java service:

- Saves the result as an **Insight** for that farm  
- Optionally creates an **Alert**  
- May broadcast a **regional warning** into the Connected Farms feed if many farms are affected  

---

## 🌐 Core Functional Modules

### 1. Farmer & Farm Management

- Manage farmers, profiles, and roles  
- Register and manage:
  - Farms  
  - Fields  
  - Crops per season  

### 2. Telemetry & Data Capture

- Capture:
  - Sensor readings (if available)  
  - Manual entries (planting, irrigation, spraying, etc.)  
- Persist data in MySQL using JPA  

### 3. AI Insights (AgriFusion)

- Prepare and send farm/field data to the Python AI service  
- Receive and store AI-generated insights  
- Expose REST endpoints and Thymeleaf pages so farmers can see:
  - Current farm/field status  
  - Risk levels  
  - Recommended actions  

### 4. Connected Farms Feed

- Real-time feed of:
  - AI warnings from AgriFusion  
  - Farmer posts, pictures, and comments  
- WebSocket integration for live updates  
- Feeds can be scoped by:
  - Region (district, ward, village)  
  - Crop type  

### 5. Alerts & Notifications

- Central alert model for:
  - Pest/disease risk  
  - Water stress  
  - Weather extremes  
  - System events  
- Future integration targets:
  - SMS  
  - WhatsApp  
  - Email  

---

## 🧪 Testing

Run the test suite:

```bash
./gradlew test
```

Includes:

- `spring-boot-starter-test`  
- `spring-security-test`  
- `spring-integration-test`  
- `reactor-test`  

---

## 🗺️ Roadmap

- [ ] Define and implement domain model (Farmer, Farm, Field, Season, Insight, Alert, Post, Comment, etc.)  
- [ ] Integrate first working version of Python AgriFusion AI service  
- [ ] Build initial Thymeleaf dashboards:
  - Farm overview  
  - Regional risk view  
  - Connected Farms live feed  
- [ ] Add JWT-based auth / OAuth2 for external clients  
- [ ] Add SMS/WhatsApp/email notifications for critical alerts  
- [ ] Containerization (Docker) and deployment automation  

---

## 🤝 Contributing

Contributions, feature ideas, and issue reports are welcome.

1. Fork the repo  
2. Create a feature branch:
   ```bash
   git checkout -b feature/my-change
   ```
3. Commit your changes:
   ```bash
   git commit -m "Describe my change"
   ```
4. Push to your branch:
   ```bash
   git push origin feature/my-change
   ```
5. Open a Pull Request  

---

## 📜 License

Add your preferred license (e.g., MIT, Apache 2.0) as `LICENSE` in the root of the repo and update this section accordingly.

---

## 📬 Contact

**Project:** Connected Farms – AgriFusion360AI  
**Author:** Talent Chigwagwa (ScriptMaster)  
**GitHub:** `https://github.com/<your-username>`  

> _“Turning real farm data into real farmer power.”_ 🌾
