# Prometheus Windows Exporter for Kubernetes

This repository contains Kubernetes configurations necessary for setting up the Prometheus Windows Exporter in a Kubernetes cluster. The exporter allows for the collection of metrics from Windows nodes and is designed to work alongside the Prometheus Operator and kube-prometheus-stack.

## Prerequisites

Before you begin, ensure that you have the following prerequisites installed and configured:

- A Kubernetes cluster with both Windows and Linux nodes.
- `kubectl` command-line tool configured to communicate with your cluster.
- Prometheus Operator or kube-prometheus-stack installed on the cluster.

## Installation Steps

### Step 1: Installing kube-prometheus-stack

If you haven't already installed the kube-prometheus-stack, you can do so by using Helm:

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus prometheus-community/kube-prometheus-stack
```

### Step 2: Apply Namespace

First, ensure you're working in the correct namespace (if default is not what you're using, adjust accordingly).

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: default
  labels:
    name: default
```

Apply the namespace configuration:

```bash
kubectl apply -f namespace.yaml
```

### Step 3: Deploy Windows Exporter DaemonSet

The DaemonSet configuration ensures that the Windows Exporter runs on each Windows node in your cluster.

```bash
kubectl apply -f windows-exporter-daemonset.yaml
```

Ensure that your Linux-based pods have the correct node selector set to avoid scheduling on Windows nodes.

### Step 4: Expose Windows Exporter with a Service

Create a service to expose the Windows Exporter.

```bash
kubectl apply -f windows-exporter-service.yaml
```

### Step 5: Set up ServiceMonitor

The ServiceMonitor is used by the Prometheus Operator to discover and scrape the metrics endpoint.

```bash
kubectl apply -f windows-exporter-servicemonitor.yaml
```

### Verifying Installation

After applying the configurations, you can verify that the Windows Exporter is correctly set up by:

1. Checking the pods are running:
```bash
kubectl get pods -n default -l app=windows-exporter
```
2. Confirming that the service endpoints are available:
```bash
kubectl get endpoints windows-exporter-service -n default
```
3. Looking at the targets in your Prometheus instance to see if the Windows Exporter targets are up and being scraped for metrics.
```bash
kubectl port-forward prometheus-prometheus-kube-prometheus-prometheus-0 9090
```

### Metrics Collection

The Windows Exporter will collect various system metrics, including CPU, memory, disk, and network metrics, which can then be visualized using Grafana or any other visualization tool that supports Prometheus as a data source.

### Troubleshooting

The Windows Exporter will collect various system metrics, including CPU, memory, disk, and network metrics, which can then be visualized using Grafana or any other visualization tool that supports Prometheus as a data source.

If you run into any issues with the setup, ensure that:

* You have applied the correct node selectors to your pods.
* The Windows Exporter DaemonSet is running on Windows nodes only.
* The Prometheus ServiceMonitor has been set up correctly and is in the same namespace as the exporter service.

For more detailed logs, you can check the logs of the Windows Exporter pods:

```bash
kubectl logs -n default -l app=windows-exporter
```

### Conclusion

These steps shuold help you successfully deploy the Prometheus Windows Exporter on your Kubernetes cluster. You can now monitor your Windows nodes alongside your Linux nodes in your Prometheus dashboard.