---
name: monitoring-observability
description: "Monitoring et observabilite: logs, metriques, traces, alerting, dashboards SRE. Use when setting up observability, logging, metrics, tracing, or alerting."
---

# Monitoring & Observability Skill

## Overview
Expert in implementing comprehensive monitoring, logging, metrics, tracing, and alerting for production applications following the three pillars of observability.

## The Three Pillars

### 1. Logging
```typescript
// Structured logging with context
logger.info("User created", {
  userId: user.id,
  email: user.email,
  timestamp: new Date().toISOString(),
  requestId: req.id,
  service: "auth-service"
});

// Error logging with stack trace
logger.error("Payment failed", {
  error: err.message,
  stack: err.stack,
  orderId: order.id,
  amount: order.total,
  userId: order.userId
});
```

**Log Levels:**
- **ERROR**: Critical failures requiring immediate attention
- **WARN**: Unexpected conditions that don't break functionality
- **INFO**: Important business events and state changes
- **DEBUG**: Detailed diagnostic information (dev only)

**Best Practices:**
- Use structured logging (JSON format)
- Include correlation IDs for request tracing
- Never log sensitive data (passwords, tokens, PII)
- Use appropriate log levels
- Centralize logs (ELK Stack, Loki, CloudWatch)

### 2. Metrics
```typescript
// Prometheus-style metrics
const httpRequestDuration = new Histogram({
  name: 'http_request_duration_seconds',
  help: 'Duration of HTTP requests in seconds',
  labelNames: ['method', 'route', 'status_code'],
  buckets: [0.01, 0.05, 0.1, 0.5, 1, 5]
});

const activeUsers = new Gauge({
  name: 'active_users_total',
  help: 'Number of currently active users'
});

const ordersTotal = new Counter({
  name: 'orders_total',
  help: 'Total number of orders',
  labelNames: ['status']
});
```

**Key Metrics (USE Method):**
- **Utilization**: CPU, memory, disk, network usage
- **Saturation**: Queue length, thread pool usage
- **Errors**: Error rate, failed requests

**Golden Signals (Google SRE):**
- **Latency**: Time to serve requests
- **Traffic**: Requests per second
- **Errors**: Error rate
- **Saturation**: How "full" is the service

### 3. Distributed Tracing
```typescript
// OpenTelemetry tracing
import { trace } from '@opentelemetry/api';

const tracer = trace.getTracer('order-service');

async function createOrder(orderData: OrderData) {
  return tracer.startActiveSpan('createOrder', async (span) => {
    span.setAttribute('order.id', orderData.id);
    span.setAttribute('order.total', orderData.total);

    try {
      // Trace database call
      const order = await tracer.startActiveSpan('db.insert', async (dbSpan) => {
        const result = await db.order.create({ data: orderData });
        dbSpan.end();
        return result;
      });

      // Trace external call
      await tracer.startActiveSpan('payment.charge', async (paySpan) => {
        await paymentService.charge(order.total);
        paySpan.end();
      });

      span.setStatus({ code: SpanStatusCode.OK });
      return order;
    } catch (error) {
      span.setStatus({ code: SpanStatusCode.ERROR, message: error.message });
      throw error;
    } finally {
      span.end();
    }
  });
}
```

### 4. Alerting
```yaml
# Alert rules (Prometheus/Alertmanager)
groups:
  - name: application
    rules:
      - alert: HighErrorRate
        expr: rate(http_requests_total{status_code=~"5.."}[5m]) > 0.05
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "High error rate detected"

      - alert: HighLatency
        expr: histogram_quantile(0.99, rate(http_request_duration_seconds_bucket[5m])) > 2
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: "99th percentile latency > 2s"

      - alert: HighMemoryUsage
        expr: process_resident_memory_bytes / process_memory_limit_bytes > 0.8
        for: 5m
        labels:
          severity: warning
```

**Alert Design:**
- **Actionable**: Every alert requires human action
- **Graded**: Warning â†’ Critical â†’ Emergency
- **Context-rich**: Include what, when, impact
- **Runbook-linked**: Link to resolution steps

### 5. Dashboards
**Key Dashboards:**
- **Service Overview**: Request rate, error rate, latency (p50/p95/p99)
- **Infrastructure**: CPU, memory, disk, network
- **Business Metrics**: Orders/min, revenue, conversion rate
- **Database**: Query performance, connections, cache hit rate

### 6. Health Checks
```typescript
// Health check endpoint
app.get('/health', async (req, res) => {
  const checks = {
    database: await checkDatabase(),
    redis: await checkRedis(),
    externalApi: await checkExternalApi(),
    diskSpace: await checkDiskSpace()
  };

  const healthy = Object.values(checks).every(c => c.status === 'ok');

  res.status(healthy ? 200 : 503).json({
    status: healthy ? 'healthy' : 'degraded',
    checks,
    uptime: process.uptime(),
    timestamp: new Date().toISOString()
  });
});
```

### 7. Tools Stack
- **Logging**: Winston/Pino (Node), ELK Stack, Loki, Datadog
- **Metrics**: Prometheus, Grafana, Datadog, New Relic
- **Tracing**: Jaeger, Zipkin, OpenTelemetry, Datadog APM
- **Alerting**: PagerDuty, Opsgenie, Slack webhooks
- **Uptime**: Pingdom, UptimeRobot, Betterstack

## Implementation Checklist
- [ ] Structured logging with correlation IDs
- [ ] Request tracing with OpenTelemetry
- [ ] Key metrics (latency, traffic, errors, saturation)
- [ ] Health check endpoints
- [ ] Alerting rules for critical conditions
- [ ] Dashboards for service and infrastructure
- [ ] Log aggregation and search
