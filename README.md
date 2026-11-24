# 🛒 NodeJS Microservices: Order & Inventory System

![Repository Size](https://img.shields.io/github/repo-size/JawherKl/nodejs-microservices)
![Last Commit](https://img.shields.io/github/last-commit/JawherKl/nodejs-microservices)
![Issues](https://img.shields.io/github/issues-raw/JawherKl/nodejs-microservices)
![Forks](https://img.shields.io/github/forks/JawherKl/nodejs-microservices)
![Stars](https://img.shields.io/github/stars/JawherKl/nodejs-microservices)

Scalable and modular microservices architecture built with Node.js (NestJS & ExpressJS), PostgreSQL, MongoDB and Docker. It leverages RabbitMQ for event-driven communication, ideal for modern e-commerce and real-time systems.

![NodeJS](https://img.shields.io/badge/node.js-6DA55F?style=for-the-badge&logo=node.js&logoColor=white)
![NestJS](https://img.shields.io/badge/nestjs-E0234E?style=for-the-badge&logo=nestjs&logoColor=white)
![ExpressJS](https://img.shields.io/badge/express.js-000000?style=for-the-badge&logo=express&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/postgres-%23316192.svg?style=for-the-badge&logo=postgresql&logoColor=white)
![MongoDB](https://img.shields.io/badge/mongodb-4EA94B?style=for-the-badge&logo=mongodb&logoColor=white)
![RabbitMQ](https://img.shields.io/badge/rabbitmq-FF6600?style=for-the-badge&logo=rabbitmq&logoColor=white)
![Docker](https://img.shields.io/badge/docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)

---

<img width="520" alt="nodejs-microservice" src="https://github.com/user-attachments/assets/8ae30270-d510-495d-b69e-5911c6142ec1" />

## ✨ Overview

This project demonstrates a modern microservices architecture for an Order & Inventory System, suitable for real-world companies. Each service is designed with a single responsibility, clear separation, and scalable technologies.

- **Tech Focus:** TypeScript-first, RESTful APIs, event-driven messaging, containerised infrastructure
- **Monorepo:** All services managed together for easy orchestration and development
- **Best Practices:** Security, scalability, and maintainability in mind

---

## 🏗️ Architecture

```mermaid
graph TB
    %% Application Layer
    subgraph "Application Layer"
        agw[API Gateway]
        us[User Service<br/>NestJS]
        ps[Product Service<br/>ExpressJS]
        os[Order Service<br/>NestJS]
        isvc[Inventory Service<br/>ExpressJS]
        ns[Notification Service<br/>ExpressJS]
    end

    %% Database Layer
    subgraph "Database Layer"
        pg[(PostgreSQL)]
        mongo[(MongoDB)]
    end

    %% Messaging Layer
    subgraph "Messaging"
        rmq[[RabbitMQ<br/>Message Broker]]
    end

    %% Dependencies and Connections
    agw -->|REST| us
    agw -->|REST| ps
    agw -->|REST| os
    agw -->|REST| isvc
    agw -->|REST| ns

    us -- "DATABASE_URL" --> pg
    os -- "DATABASE_URL" --> pg
    ps -- "mongodb://..." --> mongo
    isvc -- "mongodb://..." --> mongo
    ns -- "mongodb://..." --> mongo

    us -- "RABBITMQ_URL" --> rmq
    os -- "RABBITMQ_URL" --> rmq
    isvc -- "RABBITMQ_URL" --> rmq
    ns -- "RABBITMQ_URL" --> rmq

    %% Messaging Events
    rmq -- "Events" --> os
    rmq -- "Events" --> isvc
    rmq -- "Events" --> ns

    %% External Ports Exposure
    agw -->|":3000"| extgw[External Access]
    us -->|":3001"| extus[External Access]
    ps -->|":3002"| extps[External Access]
    os -->|":3003"| extos[External Access]
    isvc -->|":3004"| extis[External Access]
    ns -->|":3005"| extns[External Access]
    pg -->|":9091"| extpg[External Access]
    mongo -->|":9092"| extmongo[External Access]
    rmq -->|":9093/9094"| extrmq[External Access]

    %% Class Styling
    classDef app fill:#2ecc71,stroke:#27ae60,color:white
    classDef db fill:#3498db,stroke:#2980b9,color:white
    classDef msg fill:#e67e22,stroke:#d35400,color:white
    classDef ext fill:#95a5a6,stroke:#7f8c8d,color:white

    class agw,us,ps,os,isvc,ns app
    class pg,mongo db
    class rmq msg
    class extgw,extus,extps,extos,extis,extns,extpg,extmongo,extrmq ext
```

| Microservice             | Tech Stack       | DB         | Responsibility                                  |
| ------------------------ | ---------------- | ---------- | ----------------------------------------------- |
| **User Service**         | NestJS           | PostgreSQL | User registration, login, authentication, roles |
| **Product Service**      | ExpressJS        | MongoDB    | Product CRUD (create, update, list, delete)     |
| **Order Service**        | NestJS           | PostgreSQL | Order creation, validation, and processing      |
| **Inventory Service**    | ExpressJS        | MongoDB    | Stock tracking, update on orders                |
| **Notification Service** | ExpressJS        | MongoDB    | Email/log notifications on events               |
| **API Gateway**          | ExpressJS/NestJS | -          | Unified API entry, routing, token verification  |
| **Message Broker**       | RabbitMQ    | -          | Inter-service event bus                         |

---

## 📁 Project Structure

```bash
nodejs-microservices/
├── api-gateway/              # API Gateway (routing, auth)
├── user-service/             # User microservice
├── product-service/          # Product microservice
├── order-service/            # Order microservice
├── inventory-service/        # Inventory microservice
├── notification-service/     # Notification microservice
├── docker-compose.yml        # Infrastructure orchestration
├── docs/                     # Architecture diagrams, docs
├── README.md
```

---

## 🔗 Communication

* **API Gateway ⇄ Services:** REST (HTTP)
* **Between Services:** Messaging (RabbitMQ)

  * `OrderService` emits `order_created`
  * `InventoryService` listens and adjusts stock
  * `NotificationService` listens and sends notification

---

## 🚀 Features

### 👤 User Service

* Secure sign up & login
* JWT authentication
* Role-based access (admin/user)

### 🛍️ Product Service

* Create/list/delete products
* Fetch by product ID
* Simple MongoDB schema

### 🛒 Order Service

* Place new orders
* Validate users & product stock
* Emits `order_created` event

### 🏬 Inventory Service

* Listens to `order_created`
* Decreases stock or rejects if insufficient

### 📢 Notification Service

* Listens to events (`order_created`, etc)
* Sends notification (email/log)

### 🌍 API Gateway

* One entry point for frontend
* Reverse proxy, JWT verification
* Routes `/products`, `/orders`, `/users` to services

---

## 🐳 Dockerized Infrastructure

Powered by `docker-compose.yml`:

* All microservices (with build contexts)
* MongoDB, PostgreSQL databases
* RabbitMQ for messaging
* Optional admin UIs: pgAdmin, Mongo Express, RabbitMQ dashboard

### 🧪 Docker Network & Setup

Before starting the app, **create the Docker network**:

```bash
docker network create \
  --driver=bridge \
  --subnet=172.21.0.0/24 \
  nodejs_network
```

Then run the entire system:

```bash
sudo docker-compose up --build -d
```

All services will be available on their respective internal IP addresses and ports, or via the API Gateway.

---

## 🛣️ Roadmap

* [ ] Add OpenAPI/Swagger docs for each service
* [ ] Service discovery and dynamic routing
* [ ] Production-ready monitoring & logging
* [ ] Integration with frontend (React/Angular)
* [ ] Advanced notification channels (SMS, Push)
* [ ] Create CI/CD Pipeline with GitHub Actions

---

## 🤝 Contributing

We welcome contributions, ideas, and feedback.
Feel free to open issues or submit pull requests.

---

## 📜 License

MIT License. See [LICENSE](LICENSE) for details.

---

## 👨‍💻 Author

**Jawher Kallel**
[GitHub @JawherKl](https://github.com/JawherKl)

---

Made with ❤️ for scalable, real-world systems.

## 🌟 Star History

<a href="https://www.star-history.com/?repos=jawherkl%2Fnodejs-microservices&type=date&legend=top-left">
 <picture>
   <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/chart?repos=jawherkl/nodejs-microservices&type=date&theme=dark&legend=top-left" />
   <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/chart?repos=jawherkl/nodejs-microservices&type=date&legend=top-left" />
   <img alt="Star History Chart" src="https://api.star-history.com/chart?repos=jawherkl/nodejs-microservices&type=date&legend=top-left" />
 </picture>
</a>

