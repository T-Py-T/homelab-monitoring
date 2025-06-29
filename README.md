# 📊 Homelab Monitoring - Observability Stack

> **Complete observability platform** - Deploy enterprise-grade monitoring, logging, and alerting through GitOps with Prometheus, Grafana, Loki, and comprehensive dashboards.

---

## 📖 Overview

This repository provides the **observability and monitoring layer** for the homelab platform. Automatically deployed by ArgoCD after the platform services are ready, it delivers comprehensive monitoring, logging, and alerting capabilities with production-ready dashboards and integrations.

### 🎯 Repository Purpose

- Deploy Prometheus stack for metrics collection and alerting
- Provide Grafana dashboards for visualization
- Implement centralized logging with Loki and Promtail
- Monitor platform services and applications
- Enable distributed tracing and performance monitoring

---

## 🏗️ Repository Structure

```
homelab-monitoring/
├── environments/                    # Environment-specific configurations
│   ├── dev/
│   │   ├── istio-gateways.yaml     # Istio ingress for monitoring services
│   │   ├── namespaces.yaml         # Monitoring namespaces
│   │   ├── servicemonitors.yaml    # Prometheus service discovery
│   │   ├── kustomization.yaml      # Environment kustomization
│   │   └── values/                 # Helm values for monitoring stack
│   │       ├── values-prometheus-stack.yaml
│   │       ├── values-loki.yaml
│   │       ├── values-promtail.yaml
│   │       ├── values-blackbox-exporter.yaml
│   │       └── values-node-exporter.yaml
│   ├── staging/                    # Staging environment configs
│   └── prod/                      # Production environment configs
└── docs/                          # Documentation
```

---

## 📊 Monitoring Stack Components

### 🔍 Metrics Collection

| Component | Purpose | Configuration |
|:----------|:--------|:--------------|
| **Prometheus** | Time-series metrics database | `values-prometheus-stack.yaml` |
| **Node Exporter** | Host-level metrics (CPU, memory, disk) | `values-node-exporter.yaml` |
| **Blackbox Exporter** | External service monitoring | `values-blackbox-exporter.yaml` |
| **ServiceMonitors** | Auto-discovery of services to monitor | `servicemonitors.yaml` |

### 📈 Visualization & Alerting

| Component | Purpose | Access |
|:----------|:--------|:-------|
| **Grafana** | Dashboards and visualization | `https://grafana.your-domain.com` |
| **Alertmanager** | Alert routing and notifications | Integrated with Prometheus |
| **Pre-built Dashboards** | Kubernetes and application metrics | Automatically imported |

### 📝 Logging Stack

| Component | Purpose | Configuration |
|:----------|:--------|:--------------|
| **Loki** | Log aggregation and storage | `values-loki.yaml` |
| **Promtail** | Log collection agent | `values-promtail.yaml` |
| **Grafana Integration** | Log visualization in Grafana | Automatic datasource setup |

---

## 🚀 Deployment Process

### Automatic Deployment

This repository is **automatically deployed by ArgoCD** after platform services are healthy:

```
Deployment Order:
1. 🏗️ Platform Services  ← Istio, Vault (homelab-platform)
2. 📊 Monitoring Stack   ← YOU ARE HERE  
3. 🚀 Applications       ← Apps with monitoring enabled
```

### What Gets Deployed

- **Prometheus Stack**: Complete monitoring with alerting
- **Grafana**: Pre-configured with dashboards and datasources
- **Loki Stack**: Centralized logging infrastructure
- **Service Discovery**: Automatic monitoring of platform services
- **Istio Integration**: Service mesh observability

---

## 📊 Pre-Configured Dashboards

### Kubernetes Dashboards

- **Cluster Overview**: Resource usage, node health, pod status
- **Node Metrics**: CPU, memory, disk, network per node
- **Namespace Resources**: Resource consumption by namespace
- **Workload Performance**: Deployment and pod metrics
- **Persistent Volume Usage**: Storage monitoring

### Platform Services Dashboards

- **Istio Service Mesh**: Traffic flow, latency, error rates
- **ArgoCD**: GitOps deployment status and health
- **Vault**: Secret engine usage and performance
- **External Secrets**: Secret sync status and errors

### Infrastructure Dashboards

- **Node Exporter**: Detailed host-level metrics
- **Blackbox Monitoring**: External service availability
- **Alert Overview**: Current alerts and firing status

---

## 🔧 Monitoring Configuration

### Service Discovery

ServiceMonitors automatically discover services to monitor:

```yaml
# Example: Monitor platform services
spec:
  selector:
    matchLabels:
      monitoring: "true"
  endpoints:
  - port: metrics
    path: /metrics
```

### Custom Metrics

Platform services expose metrics automatically:

- **Istio**: Service mesh telemetry
- **Vault**: Secret operations and performance  
- **Keycloak**: Authentication and user metrics
- **ArgoCD**: GitOps operations and sync status

---

## 📝 Logging Configuration

### Log Collection

Promtail collects logs from:

- **All Kubernetes pods**: Application and system logs
- **Platform services**: Istio, Vault, Keycloak logs
- **System components**: kubelet, containerd logs
- **Custom applications**: Application-specific logging

### Log Retention

- **Development**: 7 days retention
- **Staging**: 30 days retention  
- **Production**: 90 days retention

### Log Parsing

- **JSON logs**: Automatic field extraction
- **Structured logs**: Label-based filtering
- **Error detection**: Automatic error log highlighting

---

## 🚨 Alerting Rules

### Platform Alerts

- **Node Down**: Kubernetes node unreachable
- **High CPU/Memory**: Resource exhaustion warnings
- **Pod Crash Loops**: Application restart failures
- **Disk Space**: Storage capacity warnings

### Service-Specific Alerts

- **Vault Sealed**: Critical vault availability
- **ArgoCD Sync Failed**: GitOps deployment issues
- **Istio Errors**: Service mesh connectivity problems
- **Certificate Expiry**: TLS certificate warnings

### Alert Routing

Alerts are routed based on severity:

- **Critical**: Immediate notification
- **Warning**: Regular notification schedule
- **Info**: Logged but not notified

---

## 🔍 Accessing Monitoring Services

### Service URLs

Access monitoring services through Istio service mesh:

- **Grafana**: `grafana.dev.cluster.local`
- **Prometheus**: `prometheus.dev.cluster.local`
- **Alertmanager**: `alertmanager.dev.cluster.local`

### Health Checks

```bash
# Check monitoring stack status
kubectl get pods -n monitoring

# Verify Istio virtual services
kubectl get virtualservices -n monitoring
```

---

## 🔧 Environment Configuration

### Development Environment

- **Reduced retention**: Lower storage requirements
- **Debug logging**: Verbose log collection
- **Relaxed alerts**: Fewer noise alerts for testing

### Production Environment

- **Extended retention**: Long-term metric and log storage
- **Optimized resources**: Right-sized for production load
- **Strict alerting**: Comprehensive alert coverage

### Custom Dashboards

Add custom dashboards by:

1. Creating JSON files in environment-specific directories
2. Adding to `kustomization.yaml`
3. ArgoCD automatically deploys changes

---

## 🔍 Troubleshooting

### Common Issues

| Issue | Check | Solution |
|:------|:------|:---------|
| Grafana not accessible | Istio gateway config | Verify `istio-gateways.yaml` |
| Missing metrics | ServiceMonitor config | Check `servicemonitors.yaml` |
| Logs not appearing | Promtail status | Verify Promtail daemonset pods |
| Alerts not firing | Prometheus rules | Check alert rule configuration |

### Diagnostic Commands

```bash
# Check monitoring stack health
kubectl get pods -n monitoring

# Verify Prometheus targets
kubectl port-forward svc/prometheus-server -n monitoring 9090:9090
# Visit http://localhost:9090/targets

# Check Loki logs
kubectl logs -n monitoring -l app=loki

# View Grafana pod logs
kubectl logs -n monitoring -l app.kubernetes.io/name=grafana
```

---

## 📊 Performance Monitoring

### Resource Usage

Monitor platform resource consumption:

- **CPU utilization** across all nodes
- **Memory usage** by namespace and pod
- **Storage consumption** for persistent volumes
- **Network traffic** between services

### Application Performance

- **Response times** for all HTTP services
- **Error rates** and status code distribution  
- **Throughput** requests per second
- **Database performance** query times and connections

---

## 🔗 Integration Points

### Platform Services Integration

- **Istio**: Service mesh telemetry and distributed tracing
- **Vault**: Secret operations monitoring
- **Keycloak**: Authentication metrics and user activity
- **ArgoCD**: GitOps deployment status and health

### Application Monitoring

Once applications are deployed, monitoring automatically includes:

- **Homepage**: Portal availability and response times
- **n8n**: Workflow execution metrics
- **Commafeed**: RSS processing performance
- **Database services**: Connection pools and query performance

---

## 🌟 Key Features

### 📊 **Comprehensive Visibility**

- Full-stack monitoring from infrastructure to applications
- Pre-built dashboards for immediate insights
- Custom alerting for proactive issue detection

### 🔄 **GitOps Integration**

- Configuration as code with version control
- Automatic deployment and updates
- Environment-specific monitoring configurations

### 🎯 **Production Ready**

- High availability monitoring stack
- Long-term storage and retention policies
- Enterprise-grade alerting and notification

---

## 📚 Next Steps

After monitoring deployment:

1. **Access Grafana**: Explore pre-built dashboards
2. **Configure Alerts**: Set up notification channels
3. **Custom Dashboards**: Create application-specific views
4. **Log Analysis**: Set up log-based alerts and analysis
5. **Performance Tuning**: Optimize based on monitoring insights

---

*📊 Complete observability for your homelab platform. Monitor everything from infrastructure to applications with enterprise-grade tools and automation.*