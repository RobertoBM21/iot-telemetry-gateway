# IoT Telemetry Ingestion & Monitoring Platform

> **Note:** This platform was designed and developed during my Software Engineering internship at Odin Solutions S.L. It successfully centralized IoT message streams, **improving technical incident response efficiency by 60%**.

## 🎯 Project Overview

This platform serves as a centralized gateway to ingest, process, and monitor MQTT telemetry data sent by distributed IoT devices. In industrial and enterprise IoT environments, handling continuous data streams without data loss or security breaches is critical.

This system resolves those challenges by decoupling data ingestion through a message broker architecture, securing device authentication via an enterprise Identity Provider (IdP), and offering a real-time monitoring interface for engineers.

## 💻 Tech Stack

**Backend & Ingestion Engine:**

- JavaScript / Node.js (Express)
- MQTT Protocol & Python (Device automation and stream management)

**Infrastructure & Messaging:**

- **RabbitMQ:** Message broker for advanced queuing, high-throughput decoupling, and asynchronous data distribution.
- **Keycloak:** Enterprise Identity and Access Management (IAM) securing API endpoints and user sessions via OpenID Connect/OAuth 2.0.

**Frontend & Database:**

- Vue.js & Tailwind CSS
- MySQL (Relational persistence for device metadata and logs)

**DevOps & Environment:**

- Docker & Docker Compose

## 🧠 Architecture & Engineering Impact

As the developer responsible for designing and implementing this centralization stack, my work focused on system reliability, decoupling, and zero-trust security:

- **Asynchronous Message Queuing:** Integrated **RabbitMQ** to decouple the ingestion gateways from the core application logic. This pattern safeguards the backend against traffic spikes from thousands of concurrent IoT devices, guaranteeing zero message loss through transactional safety queues.
- **Enterprise IAM Integration:** Deployed and configured **Keycloak** to govern platform security. Implemented Role-Based Access Control (RBAC) to ensure that only authenticated field technicians and automated systems could interact with specific device telemetry pipelines.
- **High-Throughput Stream Parsing:** Engineered ingestion utilities in Python and Node.js capable of capturing raw MQTT data streams, validating JSON payloads against device schemas, and efficiently batch-routing them to persistence layers.
- **Data-Driven Monitoring Interface:** Designed a highly responsive single-page application (SPA) in Vue.js that visualizes live sensor statuses, latency matrices, and automated alert logs, transforming raw data into actionable maintenance insights.

## 🚀 Quick Start

See the [detailed setup guide](SETUP.md) for complete instructions.

---

_If you are a technical lead or recruiter, feel free to explore `backend/src/` to review the architectural integration of RabbitMQ and Keycloak, or `script/` for the automated MQTT stream processors._
