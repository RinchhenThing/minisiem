<div align="center">

# MiniSIEM

### *Security Information & Event Management — Simplified*

[![FastAPI](https://img.shields.io/badge/FastAPI-0.100+-009688?style=for-the-badge&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![Next.js](https://img.shields.io/badge/Next.js-16.x-000000?style=for-the-badge&logo=nextdotjs&logoColor=white)](https://nextjs.org/)
[![React](https://img.shields.io/badge/React-19-61DAFB?style=for-the-badge&logo=react&logoColor=black)](https://react.dev/)
[![SQLite](https://img.shields.io/badge/SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white)](https://www.sqlite.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.x-3178C6?style=for-the-badge&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![License](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)](LICENSE)

---

**MiniSIEM** is a lightweight, full-stack Security Information and Event Management platform built for analysts, researchers, and developers who need real-time threat detection, log ingestion, alert management, and attack simulation — all within a clean, modern admin interface.

[Features](#-features) · [Architecture](#-architecture) · [Getting Started](#-getting-started) · [API Reference](#-api-reference) · [Simulation Engine](#-attack-simulation-engine) · [Contributing](#-contributing)

</div>

---

## Overview

Modern security operations demand tools that are fast, transparent, and extensible. **MiniSIEM** bridges the gap between heavyweight enterprise SIEM platforms and bare-bones log viewers by delivering:

- **Real-time log ingestion** from multiple source types (syslog, Apache, Linux auth, firewall, network)
- **Automated correlation and alert generation** through a built-in rules engine
- **Attack simulation** to test your detection capabilities across five distinct threat scenarios
- **AI-powered chat assistant** to help analysts query and interpret security events
- **JWT-authenticated admin dashboard** built with Next.js 16 and React 19

Whether you're a student learning cybersecurity, a researcher prototyping detection rules, or a small team needing a self-hosted SIEM without the enterprise price tag — MiniSIEM is built for you.

---

## Features

###  Authentication & Authorization
- Secure **JWT-based authentication** with token storage and protected route guards
- User **registration with password policy enforcement** (length, complexity, special characters)
- Password visibility toggle and graceful error feedback on the login interface
- All sensitive API endpoints are protected via dependency injection on the backend

###  Log Management
- **Ingest logs** from diverse source types: `linux_auth`, `apache_access`, `firewall`, `network`, and more
- Automatic **log normalization** — raw content is parsed and stored as structured JSON alongside the original
- Log levels supported: `INFO`, `WARNING`, `ERROR`, `CRITICAL`
- Paginated retrieval with timestamp-descending ordering for efficient browsing

###  Alert System
- **Automated alert generation** triggered by the correlation engine upon log ingestion
- Severity tiers: `LOW`, `MEDIUM`, `HIGH`, `CRITICAL`
- Alert lifecycle management with status transitions: `NEW` → `INVESTIGATING` → `RESOLVED`
- Source IP tracking on every alert for rapid attribution

###  Statistics & Dashboard
- Dedicated `/api/stats` endpoint providing aggregated security metrics
- At-a-glance visibility into log volumes, alert counts, severity distributions, and system health

###  AI Chat Assistant
- Integrated `/api/chat` endpoint powering an intelligent analyst assistant
- Ask natural language questions about your logs, alerts, and security posture
- Responses rendered in the frontend using `react-markdown` with GFM support

###  Attack Simulation Engine
- Five configurable threat scenarios to stress-test your detection pipeline:
  - **Brute Force** — SSH credential stuffing with repeated failed auth attempts
  - **SQL Injection** — Malicious query patterns embedded in Apache access logs
  - **Port Scan** — Rapid sequential connection attempts across common service ports
  - **Malware C2 Callback** — Outbound connections to known malicious IP addresses
  - **Mixed** — Probabilistic blend of all scenarios for realistic traffic simulation
- Configurable **interval**, **auto-stop duration**, and **brute force attempt count**
- Real-time simulation status, statistics tracking, and graceful stop/start controls

---

##  Architecture

MiniSIEM follows a clean **decoupled full-stack architecture** with a Python backend and a TypeScript frontend communicating over a REST API.

```
┌─────────────────────────────────────────────────────────┐
│                    Browser / Client                     │
│              Next.js 16 + React 19 + TanStack Query     │
└───────────────────────┬─────────────────────────────────┘
                        │ HTTP / REST (localhost:3000)
                        ▼
┌─────────────────────────────────────────────────────────┐
│                  FastAPI Backend                         │
│                  (localhost:8000)                        │
│                                                         │
│  ┌──────────┐ ┌────────┐ ┌───────┐ ┌────────────────┐  │
│  │   Auth   │ │  Logs  │ │Alerts │ │   Simulation   │  │
│  │  Router  │ │ Router │ │Router │ │     Router     │  │
│  └──────────┘ └────────┘ └───────┘ └────────────────┘  │
│                                                         │
│  ┌──────────────────┐    ┌──────────────────────────┐   │
│  │  Normalizer      │    │   Correlation Engine     │   │
│  │  Service         │    │   (Background Tasks)     │   │
│  └──────────────────┘    └──────────────────────────┘   │
│                                                         │
│  ┌─────────────────────────────────────────────────┐    │
│  │          SQLAlchemy ORM + SQLite                │    │
│  │          (minisiem.db)                          │    │
│  └─────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────┘
```

### Backend Stack

| Component | Technology |
|---|---|
| Web Framework | FastAPI |
| ORM | SQLAlchemy |
| Database | SQLite |
| Authentication | JWT (PyJWT) + bcrypt |
| Background Tasks | FastAPI `BackgroundTasks` |
| Password Hashing | bcrypt |

### Frontend Stack

| Component | Technology |
|---|---|
| Framework | Next.js 16 (App Router) |
| UI Library | React 19 |
| Styling | Tailwind CSS v4 |
| Component Library | shadcn/ui + Radix UI |
| Data Fetching | TanStack Query v5 |
| Icons | Lucide React |
| Markdown Rendering | react-markdown + remark-gfm |

---

##  Project Structure

```
minisiem/
│
├── backend/
│   ├── app/
│   │   ├── main.py                 # FastAPI app entry point, CORS, router mounts
│   │   ├── database.py             # SQLAlchemy engine, session factory
│   │   ├── models.py               # Log and Alert ORM models
│   │   ├── schemas/
│   │   │   └── user.py             # Pydantic schemas for auth
│   │   ├── model/
│   │   │   └── user.py             # User ORM model
│   │   ├── routers/
│   │   │   ├── auth.py             # /api/auth — register, login
│   │   │   ├── logs.py             # /api/logs — ingest, retrieve
│   │   │   ├── alerts.py           # /api/alerts — list, update status
│   │   │   ├── stats.py            # /api/stats — aggregated metrics
│   │   │   ├── chat.py             # /api/chat — AI assistant
│   │   │   └── simulation.py       # /api/simulation — attack scenarios
│   │   ├── services/
│   │   │   ├── normalizer.py       # Raw log → structured JSON
│   │   │   └── engine.py           # Correlation rules engine
│   │   └── utils/
│   │       └── security.py         # JWT creation, password hashing/verification
│   ├── static/                     # Served static files
│   └── requirements.txt
│
└── frontend/   (minisiem_admin/)
    ├── app/
    │   ├── page.tsx                # Root redirect (token → dashboard or login)
    │   ├── login/page.tsx          # Login UI
    │   ├── signup/page.tsx         # Registration UI
    │   ├── dashboard/              # Protected admin dashboard
    │   └── api/
    │       └── auth.ts             # API service layer
    ├── hooks/
    │   └── useAuth.ts              # useLogin, useRegister mutations
    ├── components/
    │   └── Logo.tsx                # Shared Logo component
    ├── types/                      # TypeScript type definitions
    ├── next.config.ts
    └── package.json
```

---

##  Getting Started

### Prerequisites

Ensure the following are installed on your system:

- **Python** ≥ 3.10
- **Node.js** ≥ 18.x
- **npm** or **yarn**
- **Git**

---

### 1. Clone the Repository

```bash
git clone https://github.com/your-username/minisiem.git
cd minisiem
```

---

### 2. Backend Setup

```bash
# Navigate to the backend directory
cd backend

# Create and activate a virtual environment
python -m venv venv
source venv/bin/activate        # On Windows: venv\Scripts\activate

# Install core dependencies
pip install fastapi uvicorn sqlalchemy pyjwt bcrypt python-multipart

# Or install from requirements (note: requirements.txt includes system packages —
# install only what's needed for the app if running in a clean venv)
pip install fastapi uvicorn sqlalchemy pyjwt bcrypt

# Start the development server
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

The API will be available at `http://localhost:8000`.  
Interactive API documentation (Swagger UI) is available at `http://localhost:8000/docs`.

---

### 3. Frontend Setup

```bash
# Navigate to the frontend directory
cd ../frontend   # or minisiem_admin/

# Install dependencies
npm install

# Start the development server
npm run dev
```

The admin dashboard will be available at `http://localhost:3000`.

---

### 4. First Login

1. Navigate to `http://localhost:3000/signup` and create your first admin account.
2. Passwords must be **4–16 characters** and include at least one letter, one number, and one special character (`@$!%*?&`).
3. After registration, log in at `/login` — you'll be redirected to the dashboard automatically.

---

##  API Reference

All API routes are prefixed with `/api`. Protected routes require a valid `Authorization: Bearer <token>` header.

### Authentication

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| `POST` | `/api/auth/register` |  Public | Register a new user account |
| `POST` | `/api/auth/login` |  Public | Authenticate and receive a JWT token |

**Login Request Body:**
```json
{
  "email": "admin@minisiem.com",
  "password": "Secure@123"
}
```

**Login Response:**
```json
{
  "success": true,
  "data": {
    "user": { "id": 1, "fullname": "Admin", "email": "admin@minisiem.com" },
    "token": "<jwt_token>"
  },
  "message": "Login successful"
}
```

---

### Logs

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| `POST` | `/api/logs/` |  Required | Ingest a new log entry |
| `GET` | `/api/logs/` |  Required | Retrieve logs (paginated, newest first) |

**Ingest Log Request Body:**
```json
{
  "source_ip": "192.168.1.10",
  "log_type": "linux_auth",
  "raw_content": "Accepted password for user admin from 192.168.1.10 port 22 ssh2",
  "level": "INFO"
}
```

Supported `log_type` values: `linux_auth`, `apache_access`, `firewall`, `network`, `syslog`, `eventlog`

---

### Alerts

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| `GET` | `/api/alerts/` |  Required | Retrieve all alerts |
| `PATCH` | `/api/alerts/{id}` |  Required | Update alert status |

**Alert Severity Levels:** `LOW` · `MEDIUM` · `HIGH` · `CRITICAL`  
**Alert Statuses:** `NEW` · `INVESTIGATING` · `RESOLVED`

---

### Statistics

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| `GET` | `/api/stats/` |  Required | Retrieve aggregated security statistics |

---

### Simulation

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| `POST` | `/api/simulation/start` |  Required | Start an attack simulation |
| `POST` | `/api/simulation/stop` |  Required | Stop the running simulation |
| `GET` | `/api/simulation/status` |  Required | Get current simulation state and stats |

**Simulation Config:**
```json
{
  "scenario": "mixed",
  "interval": 2.0,
  "brute_force_attempts": 6,
  "auto_stop": 60
}
```

**Available scenarios:** `normal` · `bruteforce` · `sql` · `portscan` · `malware` · `mixed`

---

### Chat

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| `POST` | `/api/chat/` |  Required | Send a message to the AI security assistant |

---

##  Attack Simulation Engine

The simulation engine is one of MiniSIEM's most powerful features for testing and learning. It runs in a **background thread** and continuously generates realistic log events based on your chosen scenario.

### Scenario Descriptions

####  Brute Force (`bruteforce`)
Simulates repeated SSH login failures from a single attacker IP (`10.0.0.50`) against common usernames (`root`, `admin`, `ubuntu`, etc.). Occasionally follows up with a successful login to mimic a real compromise.

####  SQL Injection (`sql`)
Generates Apache access log entries containing common SQL injection payloads such as `UNION SELECT`, `' OR '1'='1`, and `'; DROP TABLE users; --`. These are tagged `CRITICAL` level and sourced from rotating attacker IPs.

####  Port Scan (`portscan`)
Produces firewall log entries showing sequential connection attempts across common ports (22, 80, 443, 3306, 5432, 8080, 8443) from a single scanner IP (`10.0.0.100`).

####  Malware C2 Callback (`malware`)
Simulates an infected internal host (`192.168.5.50`) making outbound connections to hardcoded known-malicious IP addresses over port 443, mimicking command-and-control beacon traffic.

####  Mixed (`mixed`)
Probabilistic weighted blend of all scenarios:
- 50% normal traffic
- 25% brute force
- 15% SQL injection
- 5% port scan
- 5% malware callback

This is the most realistic scenario for testing your correlation engine's ability to distinguish signal from noise.

---

##  Security Considerations

> **MiniSIEM is intended for educational, research, and internal use.** Before deploying in any production or internet-facing environment, review the following:

- **JWT Secret Key**: Set a strong, randomly generated secret in your environment. Do not use default or hardcoded values.
- **CORS Origins**: The backend currently allows `http://localhost:3000`. Restrict this to your actual frontend domain in production.
- **SQLite in Production**: SQLite is appropriate for development and single-node deployments. For multi-user or high-throughput environments, migrate to PostgreSQL.
- **Token Storage**: The frontend stores the JWT in `localStorage`. For high-security environments, consider `httpOnly` cookie-based storage.
- **HTTPS**: Always serve both frontend and backend over TLS in any non-local deployment.
- **Rate Limiting**: Add rate limiting middleware (e.g., `slowapi`) to the auth endpoints to protect against abuse.

---

##  Development

### Running in Development Mode

**Backend** (with auto-reload):
```bash
uvicorn app.main:app --reload
```

**Frontend** (with hot module replacement):
```bash
npm run dev
```

### Linting & Type Checking

```bash
# Frontend
npm run lint

# TypeScript check
npx tsc --noEmit
```

### Building for Production

```bash
# Frontend
npm run build
npm run start
```

---

##  Roadmap

- [ ] PostgreSQL support for production deployments
- [ ] Role-based access control (RBAC) with analyst / admin tiers
- [ ] Custom correlation rule builder UI
- [ ] MITRE ATT&CK technique tagging on alerts
- [ ] Email / webhook notifications for `CRITICAL` alerts
- [ ] Log source agent for shipping real system logs
- [ ] Dark mode toggle in the admin dashboard
- [ ] Export alerts and logs to CSV / JSON

---

##  Contributing

Contributions are warmly welcomed. To get started:

1. **Fork** this repository
2. Create a feature branch: `git checkout -b feature/your-feature-name`
3. Commit your changes: `git commit -m 'feat: add your feature'`
4. Push to your branch: `git push origin feature/your-feature-name`
5. Open a **Pull Request** with a clear description of your changes

Please follow conventional commit messages and ensure your code passes linting before submitting.

---

##  License

This project is licensed under the **MIT License**. See the [LICENSE](LICENSE) file for full details.

---

<div align="center">

Built with  for the security community

*MiniSIEM — because every defender deserves good tools.*

</div>
