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

## Kubernetes and Jenkins Flow

To ensure seamless deployment, scalability, and continuous integration/continuous deployment (CI/CD), the project leverages **Kubernetes (K8s)** for container orchestration and **Jenkins** for automating the build and deployment pipelines.

### Kubernetes Setup

Each microservice (Auth, Course, and Email) is containerized using **Docker** and deployed as separate **Kubernetes pods** within the cluster. The services are exposed via **Kubernetes services**, and **Ingress controllers** are configured to manage external access. **Kafka** runs as part of the K8s setup, ensuring smooth communication between services. The database instances are connected via persistent volumes for data durability.

- **Auth Service**: Runs in its own pod, exposing APIs for authentication.
- **Course Service**: Also runs in a separate pod, connected to the PostgreSQL instance via persistent volumes.
- **Email Service**: Deployed in its own pod and consumes tasks from Kafka topics.

### Jenkins Pipeline

A **Jenkins pipeline** automates the entire CI/CD process:

1. **Code Changes**: Upon changes pushed to the version control system (e.g., GitHub), Jenkins automatically triggers a build pipeline.
2. **Build & Test**: The code is tested, Docker images are built, and these images are pushed to a Docker registry.
3. **Kubernetes Deployment**: Jenkins communicates with the Kubernetes cluster and deploys the updated images. Each microservice's pod is updated using a **rolling update strategy**, ensuring zero downtime.

The pipeline stages are structured as follows:

- **Build**: Creates a new Docker image for the updated microservice.
- **Test**: Runs unit tests, integration tests, and checks for vulnerabilities.
- **Deploy**: Deploys the service to the Kubernetes cluster.


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





