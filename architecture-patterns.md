# 🏗️ Architecture Patterns & System Design

> Based on karanpratapsingh/system-design (30.5k ⭐) + enterprise patterns
> This is how $1M/year architects design systems that scale.

## 📐 Core Architecture Patterns

### 1. Layered Architecture
```
Presentation Layer (UI/API)
    ↓
Business Logic Layer (Services)
    ↓
Data Access Layer (Repositories/ORM)
    ↓
Database Layer (Storage)
```

**When to use**: Most web applications, enterprise systems
**Pros**: Clear separation, easy to test, maintainable
**Cons**: Can become rigid, performance overhead

### 2. Microservices Architecture
```
API Gateway
    ↓
Service A → DB A    Service B → DB B    Service C → DB C
```

**When to use**: Large teams, independent scaling, different tech stacks
**Pros**: Independent deployment, scaling, fault isolation
**Cons**: Complexity, distributed transactions, network latency

### 3. Event-Driven Architecture
```
Producer → Event Bus → Consumers
         (Kafka/RabbitMQ)
```

**When to use**: Real-time systems, decoupled services, async processing
**Pros**: Loose coupling, scalability, real-time
**Cons**: Complexity, debugging difficulty, eventual consistency

### 4. Serverless Architecture
```
Client → API Gateway → Lambda/Functions → DB/Services
```

**When to use**: Variable traffic, event-driven, pay-per-use
**Pros**: No server management, auto-scaling, cost-efficient
**Cons**: Cold starts, vendor lock-in, execution limits

## 🗄️ Database Patterns

### 1. CQRS (Command Query Responsibility Segregation)
```
Commands (Write) → Write DB → Events → Read DB → Queries (Read)
```

**When to use**: High read/write disparity, complex queries
**Pros**: Optimized reads/writes, scalability
**Cons**: Complexity, eventual consistency

### 2. Event Sourcing
```
State = Apply(Events[0...n])
```

**When to use**: Audit trails, temporal queries, replayability
**Pros**: Complete history, debugging, temporal queries
**Cons**: Complexity, storage, learning curve

### 3. Database Sharding
```
Users A-M → Shard 1    Users N-Z → Shard 2
```

**When to use**: Large datasets, high write throughput
**Pros**: Horizontal scaling, performance
**Cons**: Complexity, cross-shard queries, rebalancing

## 🌐 API Design Patterns

### 1. REST
- **Resources**: Nouns, not verbs (`/users`, not `/getUsers`)
- **HTTP Methods**: GET, POST, PUT, PATCH, DELETE
- **Stateless**: Each request contains all needed info
- **HATEOAS**: Links to related resources

### 2. GraphQL
- **Single endpoint**: `/graphql`
- **Type system**: Schema defines available data
- **Queries**: Read data
- **Mutations**: Write data
- **Subscriptions**: Real-time updates

### 3. gRPC
- **Protocol buffers**: Efficient binary format
- **HTTP/2**: Multiplexed connections
- **Strong typing**: Contract-first development
- **Streaming**: Bidirectional communication

## ⚡ Performance Patterns

### 1. Caching Strategies
- **Cache-Aside**: App checks cache first, then DB
- **Write-Through**: Write to cache and DB simultaneously
- **Write-Behind**: Write to cache, async write to DB
- **Refresh-Ahead**: Proactively refresh cache before expiry

### 2. Load Balancing
- **Round Robin**: Equal distribution
- **Least Connections**: Send to least busy server
- **IP Hash**: Same client → same server
- **Consistent Hashing**: Minimal redistribution on changes

### 3. Database Optimization
- **Indexing**: B-trees for range queries, hash for exact match
- **Query Optimization**: EXPLAIN plans, avoid N+1
- **Connection Pooling**: Reuse connections, limit concurrent
- **Read Replicas**: Offload reads from primary

## 🔒 Security Patterns

### 1. Authentication
- **JWT**: Stateless, self-contained tokens
- **OAuth 2.0**: Delegated authorization
- **OIDC**: Identity layer on OAuth
- **Session-based**: Server-side state

### 2. Authorization
- **RBAC**: Role-Based Access Control
- **ABAC**: Attribute-Based Access Control
- **PBAC**: Policy-Based Access Control
- **ReBAC**: Relationship-Based Access Control

### 3. Data Protection
- **Encryption at rest**: AES-256 for stored data
- **Encryption in transit**: TLS 1.3 for network
- **Hashing**: bcrypt, Argon2 for passwords
- **Tokenization**: Replace sensitive data with tokens

## 📊 Monitoring & Observability

### 1. The Three Pillars
- **Metrics**: CPU, memory, latency, error rates
- **Logs**: Structured logging with correlation IDs
- **Traces**: Distributed request tracing

### 2. Alerting Strategy
- **Threshold-based**: CPU > 80% for 5 min
- **Anomaly detection**: Unusual patterns
- **SLI/SLO**: Service level indicators/objectives
- **Error budget**: Allowable failure rate

### 3. Health Checks
- **Liveness**: Is the service running?
- **Readiness**: Can it handle requests?
- **Startup**: Is it still initializing?

## 🎯 Decision Framework

### When to Choose What:

| Requirement | Pattern | Why |
|-------------|---------|-----|
| Simple web app | Layered | Easy to build, maintain |
| Large team, independent deploys | Microservices | Team autonomy |
| Real-time events | Event-driven | Decoupled, scalable |
| Variable traffic | Serverless | Pay-per-use, auto-scale |
| High read volume | CQRS + Read Replicas | Optimized reads |
| Audit trail needed | Event Sourcing | Complete history |
| Mobile + Web APIs | GraphQL | Flexible queries |
| High performance | gRPC | Binary, HTTP/2 |
| Global users | CDN + Edge Caching | Low latency |

### Scaling Decision Tree:

```
Is it slow?
├── Yes → Profile first (don't guess)
│   ├── CPU bottleneck → Optimize algorithms, cache
│   ├── Memory bottleneck → Fix leaks, limit caches
│   ├── I/O bottleneck → Async, batch, cache
│   └── Network bottleneck → CDN, compression, HTTP/2
└── No → Don't optimize yet
```

---

*Last updated: 2026-05-16*
*Source: karanpratapsingh/system-design (30.5k ⭐) + enterprise patterns*
