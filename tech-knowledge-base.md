# 🧠 Technical Knowledge Base - Every Developer Should Know

> Based on mtdvio/every-programmer-should-know (29.7k ⭐) + elite developer patterns
> This is the technical knowledge that separates senior from junior developers.

## 📚 Core Technical Areas

### 1. Algorithmic Complexity & Data Structures
- **Big O notation** - Time/space complexity analysis
- **Common complexities**: O(1), O(log n), O(n), O(n log n), O(n²), O(2ⁿ)
- **Data structures**: Arrays, Linked Lists, Trees, Graphs, Hash Tables, Heaps
- **When to use which**: 
  - Hash Table: O(1) lookups, but no ordering
  - Tree: O(log n) lookups with ordering
  - Graph: Relationships and networks
  - Array: Fast access, slow insert/delete
  - Linked List: Fast insert/delete, slow access

### 2. Memory Management
- **Stack vs Heap**: Stack is fast, limited, LIFO. Heap is larger, slower, dynamic
- **Garbage Collection**: Reference counting, mark-and-sweep, generational GC
- **Memory Leaks**: Common in closures, event listeners, caches without limits
- **Cache Locality**: CPU cache hits are 100x faster than RAM access

### 3. Concurrency & Parallelism
- **Threading models**: Single-threaded, multi-threaded, async/await
- **Race Conditions**: Multiple threads accessing shared data
- **Deadlocks**: Circular wait for resources
- **Synchronization**: Mutexes, semaphores, atomic operations
- **Event Loop**: How Node.js/browser handle async operations

### 4. Networking Fundamentals
- **TCP vs UDP**: Reliable vs fast, connection-oriented vs connectionless
- **HTTP/1.1 vs HTTP/2 vs HTTP/3**: Multiplexing, header compression, QUIC
- **DNS**: Domain resolution, TTL, caching
- **TLS/SSL**: Handshake, certificates, encryption
- **CDNs**: Edge caching, latency reduction

### 5. Database Systems
- **ACID Properties**: Atomicity, Consistency, Isolation, Durability
- **CAP Theorem**: Consistency, Availability, Partition tolerance (pick 2)
- **Indexing**: B-trees, hash indexes, composite indexes
- **Query Optimization**: EXPLAIN plans, query planning, index usage
- **Normalization vs Denormalization**: Trade-offs for read vs write performance
- **Connection Pooling**: Reuse connections, avoid overhead

### 6. System Design Patterns
- **Load Balancing**: Round-robin, least connections, consistent hashing
- **Caching Strategies**: Write-through, write-behind, cache-aside
- **Database Replication**: Master-slave, master-master, read replicas
- **Sharding**: Horizontal partitioning by key
- **Message Queues**: RabbitMQ, Kafka, SQS - async processing
- **API Design**: REST, GraphQL, gRPC - when to use each

### 7. Security Fundamentals
- **OWASP Top 10**: Injection, XSS, CSRF, broken auth, etc.
- **Authentication vs Authorization**: Who you are vs what you can do
- **Encryption**: Symmetric vs asymmetric, hashing vs encryption
- **Input Validation**: Never trust user input
- **Rate Limiting**: Prevent abuse, DDoS protection
- **Security Headers**: CSP, HSTS, X-Frame-Options

### 8. Testing Strategies
- **Testing Pyramid**: Unit (70%), Integration (20%), E2E (10%)
- **Test Types**: Unit, integration, E2E, performance, security
- **Mocking vs Stubbing**: Isolating dependencies
- **TDD vs BDD**: Test-driven vs behavior-driven development
- **Code Coverage**: 80%+ for critical paths, not 100% everywhere

### 9. Performance Optimization
- **Frontend**: Bundle size, lazy loading, code splitting, CDN
- **Backend**: Query optimization, caching, connection pooling
- **Database**: Indexes, query plans, connection limits
- **Network**: Compression, HTTP/2, CDN, caching headers
- **Memory**: Avoid leaks, limit cache sizes, use streams

### 10. Debugging Methodology
- **Reproduce First**: Can't fix what you can't reproduce
- **Isolate Variables**: Change one thing at a time
- **Read Logs**: Stack traces, error messages, timing
- **Binary Search**: Comment out half, see if bug persists
- **Rubber Ducking**: Explain the problem out loud
- **Time Travel Debugging**: Record state, replay

## 🎯 Elite Developer Mindset

### What $1M/year developers know that $20K/month don't:

1. **Systems Thinking** - They understand how everything connects
2. **Trade-off Analysis** - Every decision has pros/cons, they know them
3. **Root Cause Analysis** - They fix the disease, not the symptom
4. **Performance Intuition** - They know what's slow before measuring
5. **Security by Design** - Security isn't an afterthought
6. **Operational Excellence** - They think about monitoring, logging, recovery
7. **Business Alignment** - Technical decisions serve business goals
8. **Mentorship** - They elevate the whole team

### Anti-Patterns That Create Bugs:

- **Premature Optimization** - Optimize after measuring, not before
- **Over-Engineering** - Simple solutions first, complex only when needed
- **Copy-Paste Programming** - Understanding > copying
- **Ignoring Error Cases** - Happy path is 20% of real-world usage
- **Not Reading Documentation** - RTFM saves hours of debugging
- **Assuming Instead of Verifying** - Check, don't guess

## 📖 Quick Reference

### When to Use What:

| Problem | Solution |
|---------|----------|
| Need fast lookups | Hash Table / Dictionary |
| Need ordered data | Tree / Sorted Array |
| Need relationships | Graph |
| Need FIFO processing | Queue |
| Need LIFO processing | Stack |
| Need priority processing | Heap / Priority Queue |
| Need unique items | Set |
| Need key-value pairs | Hash Map |
| Need to cache | Redis / Memcached |
| Need to queue | RabbitMQ / Kafka |
| Need to search | Elasticsearch / Database index |
| Need to store files | S3 / Blob storage |

### Performance Numbers Every Dev Should Know:

- L1 cache reference: 0.5 ns
- L2 cache reference: 7 ns
- RAM reference: 100 ns
- SSD read: 150-200 μs
- HDD read: 1-10 ms
- Network round trip (same datacenter): 0.5 ms
- Network round trip (cross-country): 50-100 ms

### Common Bottlenecks:

1. **Database queries** - N+1 queries, missing indexes, full table scans
2. **Network latency** - Too many round trips, large payloads
3. **Memory leaks** - Growing memory usage over time
4. **CPU bottlenecks** - Inefficient algorithms, blocking operations
5. **I/O bottlenecks** - File reads/writes, network calls
6. **Lock contention** - Threads waiting for locks
7. **Garbage collection** - Frequent GC pauses

---

*Last updated: 2026-05-16*
*Source: mtdvio/every-programmer-should-know (29.7k ⭐) + elite developer patterns*
