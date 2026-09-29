# System Architecture Overview

This document provides a visual overview of the system architecture using a Mermaid diagram.

## Architecture Diagram

```mermaid
graph TB
    subgraph Client["Client Layer"]
        Web["Web Browser"]
        Mobile["Mobile App"]
    end

    subgraph API["API Gateway & Services"]
        Gateway["API Gateway"]
        Auth["Authentication Service"]
        User["User Service"]
        Product["Product Service"]
    end

    subgraph Data["Data Layer"]
        Cache["Cache Layer<br/>Redis"]
        Database["Primary Database<br/>PostgreSQL"]
        Backup["Backup Database<br/>PostgreSQL"]
    end

    subgraph Queue["Message Queue"]
        Queue_Srv["Message Broker<br/>RabbitMQ"]
    end

    subgraph Workers["Background Workers"]
        Worker1["Worker 1"]
        Worker2["Worker 2"]
        Worker3["Worker 3"]
    end

    subgraph External["External Services"]
        Email["Email Service"]
        Payment["Payment Gateway"]
        Analytics["Analytics Service"]
    end

    Web -->|HTTP/REST| Gateway
    Mobile -->|HTTP/REST| Gateway
    
    Gateway -->|Route| Auth
    Gateway -->|Route| User
    Gateway -->|Route| Product
    
    Auth -->|Read/Write| Cache
    User -->|Read/Write| Cache
    Product -->|Read/Write| Cache
    
    Cache -->|Fallback| Database
    Auth -->|Read/Write| Database
    User -->|Read/Write| Database
    Product -->|Read/Write| Database
    
    Database -->|Replicate| Backup
    
    Auth -->|Publish| Queue_Srv
    User -->|Publish| Queue_Srv
    Product -->|Publish| Queue_Srv
    
    Queue_Srv -->|Consume| Worker1
    Queue_Srv -->|Consume| Worker2
    Queue_Srv -->|Consume| Worker3
    
    Worker1 -->|Send| Email
    Worker2 -->|Process| Payment
    Worker3 -->|Track| Analytics
```

## Component Descriptions

### Client Layer
- **Web Browser**: User-facing web application
- **Mobile App**: Native mobile application

### API Gateway & Services
- **API Gateway**: Single entry point for all client requests
- **Authentication Service**: Handles user authentication and authorization
- **User Service**: Manages user profiles and accounts
- **Product Service**: Manages product catalog and information

### Data Layer
- **Cache Layer (Redis)**: High-speed caching for frequently accessed data
- **Primary Database (PostgreSQL)**: Main relational database
- **Backup Database (PostgreSQL)**: Replicated database for disaster recovery

### Message Queue
- **Message Broker (RabbitMQ)**: Asynchronous message processing

### Background Workers
- **Worker 1-3**: Process async tasks from the message queue

### External Services
- **Email Service**: Sends transactional emails
- **Payment Gateway**: Processes payments
- **Analytics Service**: Tracks user behavior and metrics

## Data Flow

1. Client requests are routed through the API Gateway
2. Services handle business logic and cache frequently accessed data
3. Persistent data is stored in the primary database with backup replication
4. Asynchronous tasks are published to the message queue
5. Background workers consume messages and trigger external service integrations
