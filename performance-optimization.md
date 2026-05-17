# ⚡ Performance Optimization for Web Developers

> Based on Web Performance Checklist + elite developer patterns
> This is how $1M/year developers build fast applications.

## 🎯 Performance Mindset

### What Elite Developers Know:
1. **Measure first** - Don't optimize what isn't slow
2. **User-centric metrics** - Focus on what users experience
3. **Performance is a feature** - Fast apps convert better
4. **Continuous monitoring** - Performance degrades over time
5. **Optimize bottlenecks** - 80/20 rule applies
6. **Frontend + Backend** - Full stack optimization

## 📊 Key Performance Metrics

### Core Web Vitals (Google):
- **LCP (Largest Contentful Paint)**: < 2.5s
  - How fast the main content loads
- **FID (First Input Delay)**: < 100ms
  - How fast the page responds to interaction
- **CLS (Cumulative Layout Shift)**: < 0.1
  - How stable the page is visually

### Additional Metrics:
- **TTFB (Time to First Byte)**: < 800ms
- **FCP (First Contentful Paint)**: < 1.8s
- **TTI (Time to Interactive)**: < 3.8s
- **Speed Index**: < 3.4s

## 🖥️ Frontend Performance

### 1. Reduce Bundle Size
```bash
# Analyze bundle
npm run build -- --analyze
npx webpack-bundle-analyzer dist/

# Common optimizations:
# - Tree shaking (remove unused code)
# - Code splitting (load only what's needed)
# - Lazy loading (load on demand)
# - Compression (gzip, brotli)
```

### 2. Optimize Images
```html
<!-- ✅ Good: Responsive images -->
<img 
  src="image-800.webp"
  srcset="image-400.webp 400w, image-800.webp 800w, image-1200.webp 1200w"
  sizes="(max-width: 600px) 400px, (max-width: 1200px) 800px, 1200px"
  alt="Description"
  loading="lazy"
>

<!-- ✅ Good: Modern formats -->
<picture>
  <source srcset="image.avif" type="image/avif">
  <source srcset="image.webp" type="image/webp">
  <img src="image.jpg" alt="Description">
</picture>
```

### 3. Optimize CSS/JS
```html
<!-- ✅ Good: Critical CSS inline, rest async -->
<style>
  /* Critical CSS for above-the-fold content */
</style>
<link rel="stylesheet" href="styles.css" media="print" onload="this.media='all'">

<!-- ✅ Good: Defer non-critical JS -->
<script src="app.js" defer></script>
<script src="analytics.js" async></script>
```

### 4. Caching Strategies
```javascript
// Service Worker caching
self.addEventListener('fetch', (event) => {
  event.respondWith(
    caches.match(event.request).then((response) => {
      return response || fetch(event.request).then((response) => {
        return caches.open('v1').then((cache) => {
          cache.put(event.request, response.clone());
          return response;
        });
      });
    })
  );
});

// HTTP caching headers
Cache-Control: public, max-age=31536000, immutable
Cache-Control: no-cache, must-revalidate
```

## 🖧 Network Performance

### 1. Reduce HTTP Requests
- Combine CSS/JS files
- Use image sprites
- Inline critical resources
- HTTP/2 multiplexing

### 2. Optimize Network Delivery
```nginx
# Enable compression
gzip on;
gzip_types text/plain text/css application/json application/javascript text/xml application/xml;
gzip_min_length 1000;

# Enable HTTP/2
listen 443 ssl http2;

# Set cache headers
location ~* \.(jpg|jpeg|png|gif|ico|css|js)$ {
  expires 1y;
  add_header Cache-Control "public, immutable";
}
```

### 3. CDN Optimization
- Static assets on CDN
- Edge caching
- Image optimization at edge
- Minimize origin requests

## 🗄️ Backend Performance

### 1. Database Optimization
```sql
-- ✅ Good: Use indexes
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_orders_user_id ON orders(user_id);

-- ✅ Good: Avoid N+1 queries
-- ❌ Bad: SELECT * FROM orders WHERE user_id = ? (for each user)
-- ✅ Good: SELECT * FROM orders WHERE user_id IN (?, ?, ?)

-- ✅ Good: Use EXPLAIN to analyze queries
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'test@example.com';
```

### 2. Caching Strategies
```typescript
// Redis caching
import Redis from 'ioredis';
const redis = new Redis();

// Cache-aside pattern
async function getUser(id: string) {
  const cached = await redis.get(`user:${id}`);
  if (cached) return JSON.parse(cached);
  
  const user = await db.users.find(id);
  if (user) {
    await redis.set(`user:${id}`, JSON.stringify(user), 'EX', 3600);
  }
  return user;
}

// Write-through caching
async function updateUser(id: string, data: any) {
  const user = await db.users.update(id, data);
  await redis.set(`user:${id}`, JSON.stringify(user), 'EX', 3600);
  return user;
}
```

### 3. API Optimization
```typescript
// Pagination
app.get('/api/users', async (req, res) => {
  const page = parseInt(req.query.page) || 1;
  const limit = parseInt(req.query.limit) || 20;
  const offset = (page - 1) * limit;
  
  const users = await db.users.findMany({
    skip: offset,
    take: limit,
    orderBy: { createdAt: 'desc' }
  });
  
  res.json({
    data: users,
    pagination: {
      page,
      limit,
      total: await db.users.count(),
      totalPages: Math.ceil(await db.users.count() / limit)
    }
  });
});

// Field selection
app.get('/api/users/:id', async (req, res) => {
  const fields = req.query.fields?.split(',') || ['id', 'name', 'email'];
  const user = await db.users.findUnique({
    where: { id: req.params.id },
    select: fields.reduce((acc, field) => ({ ...acc, [field]: true }), {})
  });
  res.json(user);
});
```

## 📱 Mobile Performance

### 1. Network Optimization
- Minimize payload sizes
- Use efficient formats (JSON > XML)
- Compress responses
- Use HTTP/2

### 2. Local Caching
- Cache API responses
- Store static assets locally
- Use IndexedDB for structured data
- Implement offline-first where possible

### 3. Rendering Optimization
- Lazy load images and content
- Use virtualized lists for large datasets
- Minimize re-renders
- Optimize animations (use transform/opacity)

## 🛠️ Performance Tools

### Frontend Tools:
- **Lighthouse**: Performance audit
- **WebPageTest**: Detailed performance analysis
- **Chrome DevTools**: Network, Performance, Memory tabs
- **Bundle Analyzer**: Visualize bundle composition
- **GTmetrix**: Performance grading

### Backend Tools:
- **APM**: New Relic, Datadog, AppDynamics
- **Database Profiler**: EXPLAIN, slow query logs
- **Load Testing**: k6, Artillery, JMeter
- **Monitoring**: Prometheus, Grafana

### Commands:
```bash
# Lighthouse CLI
npx lighthouse https://example.com --output html --output-path report.html

# Bundle analysis
npm run build -- --analyze
npx webpack-bundle-analyzer dist/

# Load testing
k6 run script.js
artillery run load-test.yml
```

## 📊 Performance Checklist

### Pre-Deployment:
- [ ] Run Lighthouse audit (target: 90+ performance score)
- [ ] Check Core Web Vitals
- [ ] Optimize images (WebP/AVIF, responsive)
- [ ] Minify CSS/JS/HTML
- [ ] Enable compression (gzip/brotli)
- [ ] Set cache headers
- [ ] Use CDN for static assets
- [ ] Database indexes in place
- [ ] API pagination implemented
- [ ] Error boundaries in place

### Post-Deployment:
- [ ] Monitor performance metrics
- [ ] Set up alerts for performance degradation
- [ ] Regular Lighthouse audits
- [ ] Load testing for traffic spikes
- [ ] Database query optimization
- [ ] Cache hit ratio monitoring
- [ ] CDN performance monitoring
- [ ] Mobile performance testing

## 🎯 Optimization Decision Tree

```
Is it slow?
├── Yes → Measure first (don't guess)
│   ├── Frontend slow?
│   │   ├── Large bundle? → Code split, tree shake
│   │   ├── Slow images? → Optimize, lazy load
│   │   ├── Blocking resources? → Defer, async
│   │   └── Poor caching? → Add cache headers
│   ├── Backend slow?
│   │   ├── Slow queries? → Add indexes, optimize
│   │   ├── No caching? → Add Redis/cache layer
│   │   ├── N+1 queries? → Batch, eager load
│   │   └── Large payloads? → Paginate, field select
│   └── Network slow?
│       ├── Too many requests? → Combine, HTTP/2
│       ├── Large payloads? → Compress, minimize
│       └── No CDN? → Add CDN, edge cache
└── No → Don't optimize yet (measure first)
```

---

*Last updated: 2026-05-16*
*Source: Web Performance Checklist + elite developer patterns*
