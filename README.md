# CyberShield — ZEPHORIA 2K26 PS-09

A safe enterprise cyber-defense simulator. It never sends real phishing, never collects real credentials, and records only simulated interaction telemetry.

## Architecture

React + Vite → REST API → Spring Boot → Service Layer → JPA Repository → MySQL.

This follows the challenge architecture and keeps security-sensitive logic on the backend.

## Folder structure

```text
CyberShield_FullStack/
├── backend/
│   ├── pom.xml
│   └── src/main/
│       ├── java/com/cybershield/
│       │   ├── CyberShieldApplication.java
│       │   ├── config/SecurityConfig.java
│       │   ├── controller/
│       │   ├── dto/
│       │   ├── model/
│       │   ├── repository/
│       │   └── service/
│       └── resources/application.properties
├── frontend/
│   ├── package.json
│   ├── index.html
│   └── src/
│       ├── App.jsx
│       ├── main.jsx
│       └── style.css
└── docker-compose.yml
```

## Database

Entities:
- users
- employees
- campaigns
- interaction_events
- vulnerability_scores
- training_modules
- training_assignments

The relationships are intentionally simple for a hackathon and all important actions are persisted in MySQL.

## Risk algorithm

- Open message: +10
- Click simulated link: +40
- Report: -30
- Complete training: -20
- Repeated failure: +20
- Score is clamped to 0–100.
- 0–29 LOW, 30–59 MEDIUM, 60–79 HIGH, 80–100 CRITICAL.
- Organization HVI is the average employee vulnerability score.

## Safe simulation

The employee portal uses internal `/simulation/...` routes only. It does not collect credentials and does not send external email. A click creates a simulated breach event, updates the employee risk, changes status to REMEDIATION_REQUIRED, and creates a training assignment.

## Run

1. Start MySQL:
   `docker compose up -d`
2. Start backend:
   `cd backend && mvn spring-boot:run`
3. Start frontend:
   `cd frontend && npm install && npm run dev`
4. Open:
   `http://localhost:5173`

Demo admin login:
- email: `admin@cybershield.local`
- password: `Admin@123`

## APIs

POST `/api/auth/login`
GET `/api/dashboard`
POST `/api/campaigns`
GET `/api/campaigns`
POST `/api/campaigns/{id}/launch`
GET `/api/employees`
POST `/api/interactions/open`
POST `/api/interactions/{id}/click`
POST `/api/interactions/{id}/report`
GET `/api/analytics`
GET `/api/training/assignments`
POST `/api/training/{id}/complete`

For a production deployment, replace the demo authentication with a full JWT flow and move credentials/secrets to environment variables.
