---
name: performance
description: "Optimisation des performances web, Core Web Vitals, bundle analysis, caching, et monitoring. Use when measuring or improving Core Web Vitals, LCP CLS INP, bundle size, caching, lazy loading or render performance, or when the user says lent, perf, performance, charger plus vite ou optimisation."
version: 1.0.0
tags: [performance, optimization, core-web-vitals, caching, monitoring]
---

# Performance Optimization

Guide pour optimiser les performances des applications web.

## Core Web Vitals

| Métrique | Bon | À améliorer | Mauvais |
|----------|-----|-------------|---------|
| **LCP** (Largest Contentful Paint) | < 2.5s | 2.5-4s | > 4s |
| **FID** (First Input Delay) | < 100ms | 100-300ms | > 300ms |
| **CLS** (Cumulative Layout Shift) | < 0.1 | 0.1-0.25 | > 0.25 |
| **INP** (Interaction to Next Paint) | < 200ms | 200-500ms | > 500ms |

## Bundle Optimization

### Code Splitting

```typescript
// React - Lazy loading
const Dashboard = React.lazy(() => import('./Dashboard'));
const Settings = React.lazy(() => import('./Settings'));

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <Routes>
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/settings" element={<Settings />} />
      </Routes>
    </Suspense>
  );
}
```

### Tree Shaking

```typescript
// ❌ Import complet
import _ from 'lodash';
const result = _.chunk([1, 2, 3, 4], 2);

// ✅ Import sélectif
import chunk from 'lodash/chunk';
const result = chunk([1, 2, 3, 4], 2);

// ✅ Alternative légère
import { chunk } from 'lodash-es';
```

### Dynamic Imports

```typescript
// Charger une bibliothèque lourdement
async function loadChart(data: Data[]) {
  const { Chart } = await import('chart.js');
  return new Chart(canvas, { data });
}

// Charger conditionnellement
if (process.env.NODE_ENV === 'development') {
  const { whyDidYouUpdate } = await import('why-did-you-update');
  whyDidYouUpdate(React);
}
```

## Image Optimization

### Next.js Image

```tsx
import Image from 'next/image';

// ✅ Optimisé automatiquement
<Image
  src="/hero.jpg"
  alt="Hero"
  width={1200}
  height={600}
  priority  // Above the fold
  placeholder="blur"
  blurDataURL="data:image/jpeg;base64,..."
/>

// ✅ Lazy loading
<Image
  src="/below-fold.jpg"
  alt="Below fold"
  width={800}
  height={400}
  loading="lazy"
/>
```

### Formats Modernes

```html
<picture>
  <source srcset="image.avif" type="image/avif">
  <source srcset="image.webp" type="image/webp">
  <img src="image.jpg" alt="Fallback" loading="lazy">
</picture>
```

## Caching

### HTTP Cache Headers

```typescript
// Express
app.use('/static', express.static('public', {
  maxAge: '1y',  // Cache 1 an
  immutable: true
}));

app.use('/api', (req, res, next) => {
  res.set('Cache-Control', 'no-store');  // Pas de cache pour les API
  next();
});
```

### Service Worker (Offline)

```typescript
// sw.js
const CACHE_NAME = 'v1';
const urlsToCache = [
  '/',
  '/styles.css',
  '/script.js'
];

self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then((cache) => cache.addAll(urlsToCache))
  );
});

self.addEventListener('fetch', (event) => {
  event.respondWith(
    caches.match(event.request)
      .then((response) => response || fetch(event.request))
  );
});
```

### Redis Caching

```typescript
import Redis from 'ioredis';

const redis = new Redis();

async function getUser(id: string): Promise<User> {
  // Vérifier le cache
  const cached = await redis.get(`user:${id}`);
  if (cached) {
    return JSON.parse(cached);
  }
  
  // Sinon, charger depuis la BDD
  const user = await db.user.findById(id);
  
  // Mettre en cache pour 1 heure
  await redis.setex(`user:${id}`, 3600, JSON.stringify(user));
  
  return user;
}
```

## Performance Rendering

### React Optimization

```typescript
// Memoization
const MemoizedComponent = React.memo(MyComponent, (prevProps, nextProps) => {
  return prevProps.id === nextProps.id;
});

// useMemo pour les calculs coûteux
const sortedItems = useMemo(() => {
  return items.sort((a, b) => a.name.localeCompare(b.name));
}, [items]);

// useCallback pour les callbacks
const handleClick = useCallback((id: string) => {
  setSelected(id);
}, []);

// Virtual scrolling pour les grandes listes
import { FixedSizeList } from 'react-window';

function VirtualList({ items }: { items: Item[] }) {
  return (
    <FixedSizeList
      height={600}
      itemCount={items.length}
      itemSize={50}
      width="100%"
    >
      {({ index, style }) => (
        <div style={style}>{items[index].name}</div>
      )}
    </FixedSizeList>
  );
}
```

## API Performance

### Pagination

```typescript
// Cursor-based pagination (recommandé)
async function getUsers(cursor?: string, limit: number = 20) {
  const query = db.user
    .orderBy('id', 'asc')
    .limit(limit + 1);  // +1 pour détecter s'il y a plus
  
  if (cursor) {
    query.where('id', '>', cursor);
  }
  
  const users = await query;
  const hasMore = users.length > limit;
  
  return {
    data: users.slice(0, limit),
    nextCursor: hasMore ? users[limit - 1].id : null
  };
}
```

### Compression

```typescript
import compression from 'compression';

app.use(compression({
  filter: (req, res) => {
    if (req.headers['x-no-compression']) {
      return false;
    }
    return compression.filter(req, res);
  },
  threshold: 1024  // Compresser si > 1KB
}));
```

## Monitoring

### Lighthouse CI

```yaml
# .github/workflows/lighthouse.yml
name: Lighthouse CI

on:
  pull_request:
    branches: [main]

jobs:
  lighthouse:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Run Lighthouse
        uses: treosh/lighthouse-ci-action@v10
        with:
          urls: |
            https://staging.example.com
          configPath: ./lighthouserc.json
```

### Web Vitals Monitoring

```typescript
// analytics.ts
import { onCLS, onFID, onLCP } from 'web-vitals';

function sendToAnalytics(metric: Metric) {
  const body = JSON.stringify({
    name: metric.name,
    value: metric.value,
    rating: metric.rating,
    delta: metric.delta,
    id: metric.id
  });
  
  // Utiliser Beacon API pour ne pas bloquer
  if (navigator.sendBeacon) {
    navigator.sendBeacon('/analytics', body);
  } else {
    fetch('/analytics', { body, method: 'POST', keepalive: true });
  }
}

onCLS(sendToAnalytics);
onFID(sendToAnalytics);
onLCP(sendToAnalytics);
```

## Checklist Performance

### Bundle
- [ ] Le bundle est-il code-split ?
- [ ] Les imports sont-ils optimisés ?
- [ ] Les images sont-elles en format moderne ?
- [ ] Les fonts sont-elles optimisées ?

### Runtime
- [ ] Le lazy loading est-il utilisé ?
- [ ] La memoization est-elle appliquée ?
- [ ] Le virtual scrolling est-il utilisé pour les grandes listes ?
- [ ] Les calculs coûteux sont-ils mis en cache ?

### Network
- [ ] Le caching est-il configuré ?
- [ ] La compression est-elle activée ?
- [ ] HTTP/2 est-il utilisé ?
- [ ] Le CDN est-il configuré ?

### Monitoring
- [ ] Lighthouse est-il exécuté en CI ?
- [ ] Les Core Web Vitals sont-ils monitorés ?
- [ ] Les erreurs sont-elles trackées ?
- [ ] Les performances sont-elles alertées ?

## Outils

| Catégorie | Outil | Usage |
|-----------|-------|-------|
| Bundle Analysis | Webpack Bundle Analyzer | Visualiser le bundle |
| Performance | Lighthouse | Audit complet |
| Monitoring | Sentry | Erreurs et performances |
| Analytics | Web Vitals | Métriques utilisateur |
| Testing | Lighthouse CI | Performance en CI |
| Caching | Redis | Cache serveur |
| CDN | Cloudflare / AWS CloudFront | Distribution |
