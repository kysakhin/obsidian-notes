
# Chatgpt and Claude links
https://claude.ai/share/11f6089e-38de-450c-88f4-c3bf02cc50da
https://chatgpt.com/share/681a04d1-04d8-800a-9c48-52c5ea0e4048

# Youtube
- OS
	https://youtu.be/yK1uBHPdp30?si=Pg3KwDsoGUXCzsvq

# Textbooks
- OS
	- Operating System Concepts by Galvin
	- Modern Operating systems by Tanenbaum



# System Design + Distributed Systems Roadmap

## ✅ Phase 1: Foundations (1–2 weeks)
**Goal: Understand core system design concepts**

- [ ] Read basic concepts from the [System Design Primer](https://github.com/donnemartin/system-design-primer)
- [ ] Watch “How to design WhatsApp” by Gaurav Sen or Hussein Nasser
- [ ] Understand:
  - [ ] Scalability, Latency, Throughput
  - [ ] Availability, Consistency, Partition Tolerance (CAP Theorem)
  - [ ] Load Balancing (L4 vs L7)
  - [ ] Caching (Redis, CDN basics)
  - [ ] SQL vs NoSQL Databases
  - [ ] Monolith vs Microservices

---

## ✅ Phase 2: Build Small Systems (2–3 weeks)
**Goal: Implement basic systems with Go or Python**

- [ ] Choose a language: Go or Python
- [ ] Build small projects:
  - [ ] URL Shortener
  - [ ] Rate Limiter (token bucket or leaky bucket)
  - [ ] Pastebin Clone
  - [ ] Todo API with Redis Caching
  - [ ] JWT-based Auth System

**Concepts covered:**
- [ ] REST APIs
- [ ] HTTP servers
- [ ] Routing
- [ ] Database access
- [ ] Caching

---

## ✅ Phase 3: Intermediate System Design (2–4 weeks)
**Goal: Learn how to scale and harden systems**

- [ ] Learn:
  - [ ] Horizontal vs Vertical Scaling
  - [ ] Load Balancers (Nginx, HAProxy)
  - [ ] Replication and Sharding
  - [ ] Message Queues (RabbitMQ, Redis Streams)
  - [ ] Rate Limiting Patterns
  - [ ] CDN and Caching Layers

- [ ] Build:
  - [ ] Background job processor (e.g., email sender)
  - [ ] Distributed counter with Redis or channels
  - [ ] File Upload Service with DB metadata

---

## ✅ Phase 4: Distributed Systems Basics (3–5 weeks)
**Goal: Understand and build simple distributed systems**

- [ ] Learn:
  - [ ] Concurrency (Goroutines or Python threads/async)
  - [ ] Basics of TCP/HTTP networking
  - [ ] Consensus (Raft, Paxos – at a high level)
  - [ ] Eventual Consistency
  - [ ] Heartbeats & Gossip Protocols

- [ ] Build:
  - [ ] Multi-client chat server
  - [ ] Distributed key-value store
  - [ ] Heartbeat monitoring system

---

## ✅ Phase 5: Combine & Scale (4+ weeks)
**Goal: Design real-world systems using everything you've learned**

- [ ] Learn:
  - [ ] Logging, Tracing, Metrics (Observability)
  - [ ] Docker & Containers
  - [ ] Simple Kubernetes concepts
  - [ ] CI/CD Pipelines
  - [ ] Fault tolerance (Retries, Circuit Breakers)

- [ ] Projects:
  - [ ] Instagram-lite backend
  - [ ] YouTube metadata backend (uploads, views, etc.)
  - [ ] Social feed service with fan-out, ranking

---

## Bonus Resources
- Book: *Designing Data-Intensive Applications* by Martin Kleppmann
- GitHub: [awesome-scalability](https://github.com/binhnguyennus/awesome-scalability)
- YouTube: Gaurav Sen, Hussein Nasser, Tech Dummies

