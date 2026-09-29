# System Architecture Overview

This document provides interactive visual overviews of multiple system architectures using Mermaid diagrams.

## 1. Monolithic Architecture

```mermaid
graph TB
    subgraph Client["Client Layer"]
        Web["🌐 Web Browser"]
        Mobile["📱 Mobile App"]
    end

    subgraph Monolith["Monolithic Application"]
        UI["UI Layer"]
        BL["Business Logic"]
        Auth["Authentication"]
        User["User Management"]
        Product["Product Management"]
        Order["Order Processing"]
        Payment["Payment Processing"]
        Notification["Notifications"]
    end

    subgraph Data["Data Layer"]
        Cache["🔄 Cache<br/>Redis"]
        Database["🗄️ Database<br/>PostgreSQL"]
    end

    Web -->|HTTP| Monolith
    Mobile -->|HTTP| Monolith
    
    Monolith -->|Read/Write| Cache
    Monolith -->|Persist| Database
    
    style Monolith fill:#ff9999
    style Client fill:#99ccff
    style Data fill:#99ff99
```

---

## 2. Microservices Architecture

```mermaid
graph TB
    subgraph Client["Client Layer"]
        Web["🌐 Web"]
        Mobile["📱 Mobile"]
        Desktop["🖥️ Desktop"]
    end

    subgraph Gateway["API Layer"]
        APIGateway["⚙️ API Gateway<br/>Load Balancer"]
    end

    subgraph Services["Microservices"]
        AuthSvc["🔐 Auth Service"]
        UserSvc["👤 User Service"]
        ProductSvc["📦 Product Service"]
        OrderSvc["📋 Order Service"]
        PaymentSvc["💳 Payment Service"]
        NotifSvc["📧 Notification Service"]
    end

    subgraph Data["Data Storage"]
        AuthDB["Auth DB"]
        UserDB["User DB"]
        ProductDB["Product DB"]
        OrderDB["Order DB"]
    end

    subgraph MessageQueue["Async Communication"]
        MQ["📬 Message Broker<br/>RabbitMQ/Kafka"]
    end

    subgraph Workers["Background Workers"]
        Worker1["⚡ Worker 1"]
        Worker2["⚡ Worker 2"]
    end

    Web -->|Request| APIGateway
    Mobile -->|Request| APIGateway
    Desktop -->|Request| APIGateway
    
    APIGateway -->|Route| AuthSvc
    APIGateway -->|Route| UserSvc
    APIGateway -->|Route| ProductSvc
    APIGateway -->|Route| OrderSvc
    
    AuthSvc -->|Read/Write| AuthDB
    UserSvc -->|Read/Write| UserDB
    ProductSvc -->|Read/Write| ProductDB
    OrderSvc -->|Read/Write| OrderDB
    
    OrderSvc -->|Publish| MQ
    PaymentSvc -->|Publish| MQ
    
    MQ -->|Consume| Worker1
    MQ -->|Consume| Worker2
    Worker1 -->|Trigger| NotifSvc
    
    style Services fill:#99ff99
    style Client fill:#99ccff
    style Gateway fill:#ffcc99
    style Data fill:#ff99cc
```

---

## 3. Serverless Architecture

```mermaid
graph TB
    subgraph Client["Client Layer"]
        Web["🌐 Web App"]
        Mobile["📱 Mobile App"]
    end

    subgraph CDN["Content Delivery"]
        CDNNode["🌍 CDN<br/>CloudFront"]
    end

    subgraph APILayer["API Layer"]
        APIGateway["⚙️ API Gateway"]
    end

    subgraph Serverless["Serverless Functions"]
        AuthFunc["🔐 Auth Lambda"]
        UserFunc["👤 User Lambda"]
        ProductFunc["📦 Product Lambda"]
        OrderFunc["📋 Order Lambda"]
        PaymentFunc["💳 Payment Lambda"]
    end

    subgraph Storage["Storage Services"]
        S3["🗂️ S3<br/>Object Storage"]
        DynamoDB["🗄️ DynamoDB<br/>NoSQL"]
        RDS["📊 RDS Aurora"]
    end

    subgraph Events["Event-Driven"]
        EventBridge["📡 EventBridge"]
    end

    subgraph Monitoring["Monitoring"]
        CloudWatch["📊 CloudWatch<br/>Logs & Metrics"]
    end

    Web -->|Request| CDNNode
    Mobile -->|Request| APIGateway
    CDNNode -->|Request| APIGateway
    
    APIGateway -->|Invoke| AuthFunc
    APIGateway -->|Invoke| UserFunc
    APIGateway -->|Invoke| ProductFunc
    APIGateway -->|Invoke| OrderFunc
    
    AuthFunc -->|Read/Write| RDS
    UserFunc -->|Read/Write| RDS
    ProductFunc -->|Read/Write| DynamoDB
    OrderFunc -->|Read/Write| DynamoDB
    
    OrderFunc -->|Publish| EventBridge
    PaymentFunc -->|Publish| EventBridge
    
    AuthFunc -->|Log| CloudWatch
    UserFunc -->|Log| CloudWatch
    ProductFunc -->|Log| CloudWatch
    
    style Serverless fill:#ffffcc
    style Client fill:#99ccff
    style CDN fill:#ccffcc
    style Storage fill:#ff99cc
```

---

## 4. Hybrid Architecture (On-Premise + Cloud)

```mermaid
graph TB
    subgraph Client["Client Layer"]
        Web["🌐 Web Browser"]
        Mobile["📱 Mobile App"]
    end

    subgraph OnPrem["On-Premise Data Center"]
        LB["⚖️ Load Balancer"]
        WebServer["🖥️ Web Servers"]
        AppServer["⚙️ App Servers"]
        LegacyDB["🗄️ Legacy Database<br/>Oracle/SQL Server"]
    end

    subgraph VPN["Secure Tunnel"]
        VPNGateway["🔒 VPN Gateway"]
    end

    subgraph Cloud["Cloud Services - AWS"]
        APIGateway["⚙️ API Gateway"]
        Microservices["📦 Microservices"]
        Cache["🔄 ElastiCache"]
        CloudDB["🗄️ Aurora DB"]
    end

    subgraph External["External Services"]
        ThirdParty["🌐 Third-party APIs"]
        SaaS["☁️ SaaS Services"]
    end

    Web -->|Request| LB
    Mobile -->|Request| APIGateway
    
    LB -->|Route| WebServer
    WebServer -->|Connect| AppServer
    AppServer -->|Query| LegacyDB
    
    AppServer -->|Sync| VPNGateway
    VPNGateway -->|Connect| APIGateway
    
    APIGateway -->|Route| Microservices
    Microservices -->|Cache| Cache
    Microservices -->|Persist| CloudDB
    
    Microservices -->|Call| ThirdParty
    Microservices -->|Integrate| SaaS
    
    style OnPrem fill:#ffcccc
    style Cloud fill:#ccffcc
    style Client fill:#99ccff
    style External fill:#ffffcc
```

---

## 5. Event-Driven Architecture

```mermaid
graph TB
    subgraph Sources["Event Sources"]
        UserAction["👤 User Actions"]
        SystemEvent["⚙️ System Events"]
        ExternalEvent["🌐 External Events"]
    end

    subgraph EventBus["Event Bus/Broker"]
        Kafka["📬 Apache Kafka<br/>or<br/>AWS EventBridge"]
    end

    subgraph Consumers["Event Consumers"]
        UserService["👤 User Service"]
        OrderService["📋 Order Service"]
        NotificationService["📧 Notification Service"]
        AnalyticsService["📊 Analytics Service"]
        ReportingService["📝 Reporting Service"]
    end

    subgraph Storage["Data Storage"]
        EventStore["📝 Event Store<br/>Immutable Log"]
        Cache["🔄 Cache"]
        Database["🗄️ Database"]
    end

    subgraph Downstream["Downstream Systems"]
        Dashboard["📊 Dashboard"]
        Reports["📈 Reports"]
        Alerts["🚨 Alerts"]
    end

    UserAction -->|Publish| Kafka
    SystemEvent -->|Publish| Kafka
    ExternalEvent -->|Publish| Kafka
    
    Kafka -->|Subscribe| UserService
    Kafka -->|Subscribe| OrderService
    Kafka -->|Subscribe| NotificationService
    Kafka -->|Subscribe| AnalyticsService
    Kafka -->|Subscribe| ReportingService
    
    UserService -->|Persist| EventStore
    OrderService -->|Persist| EventStore
    NotificationService -->|Persist| EventStore
    AnalyticsService -->|Read| EventStore
    
    UserService -->|Update| Cache
    OrderService -->|Update| Cache
    UserService -->|Write| Database
    OrderService -->|Write| Database
    
    AnalyticsService -->|Feed| Dashboard
    ReportingService -->|Feed| Reports
    NotificationService -->|Trigger| Alerts
    
    style Sources fill:#ffcccc
    style EventBus fill:#ffff99
    style Consumers fill:#99ff99
    style Storage fill:#99ccff
    style Downstream fill:#ff99ff
```

---

## 6. CQRS (Command Query Responsibility Segregation) Architecture

```mermaid
graph TB
    subgraph Client["Client Layer"]
        Web["🌐 Web Browser"]
        Mobile["📱 Mobile App"]
        Admin["👨‍💼 Admin Portal"]
    end

    subgraph CommandSide["Command Side (Write)"]
        CommandGateway["⚙️ Command Gateway"]
        CreateCmd["✍️ Create Command"]
        UpdateCmd["✏️ Update Command"]
        DeleteCmd["🗑️ Delete Command"]
        CommandBus["📬 Command Bus"]
    end

    subgraph QuerySide["Query Side (Read)"]
        QueryGateway["⚙️ Query Gateway"]
        GetQuery["🔍 Get Query"]
        SearchQuery["🔎 Search Query"]
        ReportQuery["📊 Report Query"]
        QueryBus["📬 Query Bus"]
    end

    subgraph EventHandlers["Event Handlers"]
        ProjectionUpdater["🔄 Projection Updater"]
        CacheUpdater["💾 Cache Updater"]
        SearchIndexer["🔍 Search Indexer"]
    end

    subgraph Storage["Storage Layer"]
        WriteDB["✍️ Write Database<br/>PostgreSQL"]
        ReadDB["🔍 Read Database<br/>MongoDB"]
        Cache["🔄 Cache<br/>Redis"]
        SearchIndex["🔎 Search Index<br/>Elasticsearch"]
        EventStore["📝 Event Store"]
    end

    Web -->|Write| CommandGateway
    Admin -->|Write| CommandGateway
    
    Web -->|Read| QueryGateway
    Mobile -->|Read| QueryGateway
    Admin -->|Read| QueryGateway
    
    CommandGateway -->|Route| CreateCmd
    CommandGateway -->|Route| UpdateCmd
    CommandGateway -->|Route| DeleteCmd
    
    CreateCmd -->|Dispatch| CommandBus
    UpdateCmd -->|Dispatch| CommandBus
    DeleteCmd -->|Dispatch| CommandBus
    
    CommandBus -->|Publish Event| EventStore
    CommandBus -->|Persist| WriteDB
    
    GetQuery -->|Dispatch| QueryBus
    SearchQuery -->|Dispatch| QueryBus
    ReportQuery -->|Dispatch| QueryBus
    
    QueryBus -->|Query| ReadDB
    QueryBus -->|Query| Cache
    QueryBus -->|Query| SearchIndex
    
    EventStore -->|Update| ProjectionUpdater
    ProjectionUpdater -->|Sync| ReadDB
    
    EventStore -->|Update| CacheUpdater
    CacheUpdater -->|Sync| Cache
    
    EventStore -->|Update| SearchIndexer
    SearchIndexer -->|Sync| SearchIndex
    
    style CommandSide fill:#ffcccc
    style QuerySide fill:#99ff99
    style EventHandlers fill:#ffff99
    style Storage fill:#99ccff
    style Client fill:#ff99ff
```

---

## Architecture Comparison Table

| Architecture | Scalability | Complexity | Maintainability | Best For |
|---|---|---|---|---|
| **Monolithic** | Low | Low | High | Small teams, MVP |
| **Microservices** | High | High | Medium | Large systems, teams |
| **Serverless** | Very High | Medium | Medium | Sporadic workloads |
| **Hybrid** | High | Very High | Low | Legacy + New systems |
| **Event-Driven** | Very High | High | Medium | Real-time systems |
| **CQRS** | High | Very High | Medium | Complex domains |

---

## Interactive Guide

### Choose Your Architecture:

**📌 Monolithic** - Single deployable unit
- ✅ Simpler to understand
- ✅ Easier to test end-to-end
- ❌ Difficult to scale individual components
- ❌ Tight coupling between features

**📌 Microservices** - Independent deployable services
- ✅ Independent scaling
- ✅ Technology diversity
- ✅ Fault isolation
- ❌ Distributed system complexity
- ❌ Network latency

**📌 Serverless** - Function-as-a-Service
- ✅ No infrastructure management
- ✅ Auto-scaling
- ✅ Pay-per-use pricing
- ❌ Vendor lock-in
- ❌ Cold start latency

**📌 Hybrid** - Mix on-premise and cloud
- ✅ Gradual migration
- ✅ Legacy system support
- ✅ Data sovereignty
- ❌ Operational complexity
- ❌ Network latency between systems

**📌 Event-Driven** - Asynchronous event processing
- ✅ Loose coupling
- ✅ Scalability
- ✅ Real-time capabilities
- ❌ Debugging complexity
- ❌ Eventual consistency challenges

**📌 CQRS** - Separate read and write models
- ✅ Optimized read/write paths
- ✅ Independent scaling
- ✅ Flexibility
- ❌ Eventual consistency
- ❌ Operational complexity

---

## Key Design Patterns by Architecture

### Monolithic
- Layered architecture
- MVC pattern
- Service locator

### Microservices
- API Gateway
- Circuit Breaker
- Saga pattern
- Database per service

### Serverless
- Choreography
- Function composition
- Strangler pattern

### Hybrid
- Adapter pattern
- Facade pattern
- Proxy pattern

### Event-Driven
- Event sourcing
- CQRS
- Saga pattern
- Choreography vs Orchestration

### CQRS
- Event sourcing
- Eventual consistency
- Read model projection
- Snapshotting

---

*Last updated: 2026-09-29*
