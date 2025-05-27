# Web Hosting

### choosing a hosting provider
when choosing a provider some features to look for are: 
- actual geographic location. if you're based in the Netherlands then you naturally tend to choose Netherlands based hosting providers. with assumption that majority of your traffic will be from the same region.
- SFTP
- server resources
- CDN
- uptime
- SSL certificate
- DDoS protection

## Shared Hosting
Shared hosting is a type of web hosting where multiple websites reside on a single physical server and share its resources such as CPU, RAM, disk space, and bandwidth. It's one of the most common and affordable hosting options available, especially for beginners and small websites.
### Key Features

- Multiple websites share the same server environment
- Hosting provider manages server maintenance and updates
- Typically includes a control panel (e.g., cPanel) for ease of use
- One-click installations for popular applications like WordPress
### Advantages
- Low cost
- Easy to set up and manage
- No technical knowledge required
- Good for small to medium traffic websites
### Disadvantages
- Limited resources per site
- Performance can be affected by other websites on the same server
- Less control over server configurations
- Shared IP address
- Higher risk of security vulnerabilities due to shared environment
### Ideal Use Cases
- Personal blogs
- Portfolio sites
- Small business websites
- Test or staging environments

---

## Alternatives to Shared Hosting

### 1. VPS Hosting (Virtual Private Server)
A VPS splits a physical server into multiple virtual servers. Each virtual server has dedicated resources and more control than shared hosting.

**Pros:**
- More reliable performance
- Root access and full control
- Scalable resources

**Cons:**
- More expensive than shared hosting
- Requires basic technical knowledge

### 2. Cloud Hosting
Cloud hosting uses a network of virtual servers (clouds) to host websites. It distributes resources dynamically based on demand.

**Pros:**
- High scalability
- Excellent uptime and reliability
- Pay-as-you-use pricing

**Cons:**
- Can become expensive with high traffic
- More complex than shared hosting

### 3. Dedicated Hosting
In dedicated hosting, you rent an entire physical server solely for your website.

**Pros:**
- Full control and customization
- Maximum performance and security
- No resource sharing

**Cons:**
- Expensive
- Requires advanced technical expertise

### 4. Managed Hosting
This is a service (available for shared, VPS, or cloud hosting) where the hosting provider manages everything—updates, security, backups, etc.

**Pros:**
- Hassle-free maintenance
- Ideal for non-technical users

**Cons:**
- More expensive than unmanaged hosting
- Less flexibility

### 5. Static Site Hosting (e.g., Netlify, Vercel, GitHub Pages)
Great for websites that don’t need a backend (like blogs, portfolios, or documentation).
**Pros:**
- Very fast
- Free or low cost
- Easy to deploy from Git

**Cons:**
- No server-side functionality
- Requires Jamstack or static site generators

---

# Scaling
## Vertical scaling
- add more resources
- upgrade to a better faster server
- reach technology limits
- better ram, processor

# Horizontal Scaling: 
## Part 1

### 1. Problem: One server can’t handle all the traffic

If thousands of users hit your site, a single server can get overloaded.

**How do you fix it?**  
You add **more servers**. This is called **horizontal scaling**.

### 2. Problem: If you have multiple servers, who decides where requests go?

Now that you have `server1`, `server2`, `server3`, etc., you need a system to **distribute requests**.

**How do you fix it?**  
Use a **load balancer**. It’s a middleman that receives all incoming traffic and forwards it to one of your servers.

### 3. Problem: How does the load balancer choose which server to send the request to?

**How do you fix it?**  
Use simple algorithms like:

- **Round-robin**: Send requests to servers one by one in a loop.
- **Least connections**: Send to the server with the fewest users currently connected.

These strategies keep the load balanced.

### 4. Problem: What if a user logs in on `server1` but next request goes to `server2`?

When a user logs in, the server stores their session — something like “this user is authenticated.”  
If that session is stored **only on `server1`**, and next time the load balancer sends the user to `server2`, then `server2` will say:

> "Who are you? I don’t have any record of you being logged in."

This breaks the user experience.

### 5. How do you fix that?

**Fix:** Use a **shared session store**. Instead of storing sessions only on each server, you use something like **Redis** — a fast, centralized storage that all servers can talk to.

Now, when `server1` stores the session, it puts it in Redis.  
When `server2` gets the next request, it checks Redis and says,

> "Ah, okay. You’re already logged in."

This keeps the experience smooth, no matter which server handles the request.

### 6. Problem: Redis is now a single point of failure

If Redis goes down, none of your servers can access session info.  
Also, every request now depends on a network call to Redis, which can slow things down under heavy load.

## Part 2
### 7. How do you fix that?

**Fix:**  
Make Redis **highly available**:

- Use **Redis clustering** or **replication**
- Add a **Redis Sentinel** for monitoring and automatic failover
- Use **managed Redis services** (like AWS ElastiCache or Redis Cloud)

This makes Redis much more reliable.

### 8. Problem: You still have to make a Redis call on every request

Every time a user makes a request, the server has to:

- Read the session ID from a cookie
- Ask Redis for the session data

This adds **latency** and **network overhead**, especially if your app gets really big.


### 9. How do you fix that?

**Fix:**  
Use **JWT (JSON Web Tokens)** — a stateless authentication method.

Instead of storing the session on the server (or in Redis), you **send the session data to the client inside a signed token**.

Here’s how it works:

- When the user logs in, the server creates a JWT like:

```json
{
  "userId": 123,
  "role": "admin",
  "exp": 1716929123
}
```

- This token is signed and sent to the browser.
- On future requests, the browser sends the token with each request.
- The server **verifies the signature** and trusts the data inside the token.

No Redis call needed. No session stored on the server.

### 10. Problem: What if you want to **log the user out early** or **revoke their access**?

With JWTs, the token is **self-contained** — if it's valid and not expired, it's trusted.

But what if the user logs out or gets banned? You can’t take the token back — it’ll still work until it expires.

### 11. How do you fix that?

There are two common fixes:

**Fix A: Use short-lived access tokens**

- Make JWTs expire quickly (e.g., 15 minutes)
- Give the client a **refresh token** (stored securely in a cookie)
- The client uses the refresh token to get a new access token before the old one expires
- You can revoke refresh tokens if needed

**Fix B: Use a token blacklist (stored in Redis)**

- Keep a list of "revoked" JWT IDs in Redis
- On each request, check if the token is blacklisted

But this adds a bit of state again — it's a tradeoff.

## Part 3: Scaling Databases

### 12. Problem: One database can’t handle all the read/write traffic

As your app grows, the database becomes the next bottleneck.

### 13. How do you fix that?

**Fix A: Add read replicas**

- Direct all **writes** to the primary DB
- Send **reads** (like getting user data) to replicas

This helps reduce the load on your main DB.

### 14. Drawback: Replication isn’t instant

There’s a **replication delay** — users might not immediately see their recent changes when the app hits a replica.

### 15. How do you fix that?

**Fix:**
- For things that just changed (e.g. after a user updates their profile), **read from the primary** temporarily
- Or store recent writes in memory (cache) and merge that with DB reads

### 16. Problem: One DB server isn’t enough for writes either

Reads scale well with replicas, but **writes still go to one machine**.

### 17. How do you fix that?

**Fix:** Use **sharding** — split your data across multiple databases.

Example:

- Users A–M go to DB1
- Users N–Z go to DB2

Now you’ve divided the write load.

### 18. Drawback: Sharding adds complexity

- Harder to run cross-user queries
- Rebalancing shards is tricky if one grows faster

### 19. How do you fix that?

**Fix:**

- Use **shard keys** that evenly spread the load (e.g., user ID modulo number of shards)
- Use tools like **Vitess**, **Citus**, or databases built for sharding (e.g. **CockroachDB**)

## Part 4: Scaling File Storage

### 20. Problem: App servers can’t store user uploads (e.g., profile pictures)

When using multiple servers, one server might not have access to a file another server saved.

### 21. How do you fix that?

**Fix:** Store files in **object storage** like:

- Amazon S3
- Google Cloud Storage
- DigitalOcean Spaces

Now every server can access the files.

### 22. Drawback: Higher latency

Reading/writing from cloud storage is slower than reading from a local disk.

### 23. How do you fix that?

**Fix:**

- Use a **CDN** (Content Delivery Network) to cache and deliver files faster
- Use **presigned URLs** so clients can upload/download directly from storage

## Part 5: API Rate Limiting

### 24. Problem: With multiple servers, how do you track API rate limits?

If each server tracks requests per user, one user could hit all servers and bypass the limit.

### 25. How do you fix that?

**Fix:** Use a **centralized rate limiter** using Redis.

Each server:

- Increments a Redis key like `ratelimit:user123`
- Checks if the user has gone over the allowed number

### 26. Drawback: Centralized Redis for rate limiting can be a bottleneck

All API traffic needs to hit Redis, which might get overwhelmed.

### 27. How do you fix that?

**Fix:**

- Use **sliding window log or token bucket** algorithms with Redis pipelining
- Or use **edge services** like Cloudflare or API gateways (like Kong, Envoy) that do rate-limiting before requests hit your servers


## When to use what
|Use Case|Vertical Scaling|Horizontal Scaling|
|---|---|---|
|MVPs, startups, side projects|✅|❌ (overkill)|
|Big traffic spikes or growing user base|❌|✅|
|Simple deployments and architecture|✅|❌|
|High availability & resilience|❌|✅|
|Cost-effective at small scale|✅|❌|
|Fault-tolerant, modular services|❌|✅|