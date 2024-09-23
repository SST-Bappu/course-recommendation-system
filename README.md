# Course Recommendation Microservice Project (ML Powered)





## Introduction

Welcome to the ML-Powered Course Recommendation Microservice Project! This project emulates the core functionalities of an online learning platform, focusing on user authentication, course management, email notifications, and personalized course recommendations powered by machine learning.

Designed with a **microservice architecture**, the system ensures scalability, maintainability, and efficient handling of user interactions and background tasks.

---

## Architecture Overview

The project comprises three main microservices:

1. **Auth Service**
2. **Course Service**
3. **Email Service**

These services interact through a **Kafka message broker** and utilize dedicated databases to manage their respective data. Authentication is handled via **JWT tokens**, ensuring secure and stateless user sessions.

### Graphical Representation

Below is a **Mermaid diagram** illustrating the relationships between the services, databases, and the message broker:

```mermaid
graph TD
    subgraph Auth Service
        A[User Registration & Authentication]
        A -->|Generates JWT Tokens| B(JWT Tokens)
        A -->|Publishes to Kafka| K(Kafka Topic)
    end

    subgraph Course Service
        C[Course Management APIs]
        C -->|Validates JWT Tokens| B
        C -->|Reads/Writes| D[(PostgreSQL - Course DB)]
        C -->|Publishes to Kafka| K
    end

    subgraph Email Service
        E[Email Task Consumer]
        E -->|Consumes from Kafka| K
        E -->|Reads/Writes| F[(MongoDB - Email DB)]
    end

    subgraph Databases
        D[(PostgreSQL - Auth & Course DB)]
        F[(MongoDB - Email DB)]
    end

    B --> C
