# Grafana Metrics Dashboard Deployment Guide

## Prerequisites
- Kubernetes cluster access with `kubectl` configured
- Grafana instance running in your cluster
- Grafana API key (service account token)
- JSONL metrics files from Shortfin and SGLang benchmarks

## Setup Steps

### 1. Configure Grafana API Key
If you haven't already set up the API key:
```bash
# First ensure you're in the correct namespace
kubectl config set-context --current --namespace=monitoring

# Create the secret with your API key
kubectl create secret generic grafana-api-key \
  --from-literal=api-key=YOUR_GRAFANA_API_KEY
```

### 2. Prepare Metrics Storage
Ensure your JSONL files are accessible to the pod. The files should follow this structure:
```
/data/metrics/files/
├── shortfin_10_1_none.jsonl
├── shortfin_10_2_none.jsonl
├── ...
├── shortfin_10_1_trie.jsonl
├── shortfin_10_2_trie.jsonl
├── ...
├── sglang_10_1.jsonl
├── sglang_10_2.jsonl
└── ...
```

### 3. Deploy the CronJob
1. Save the provided configuration to `grafana-metrics-job.yaml`
2. Update the following in the file:
   ```yaml
   # Update the hostPath to your metrics location
   volumes:
   - name: metrics-volume
     hostPath:
       path: /data/files  # <- Update this(if different)
   
   # Update if your Grafana service name is different
   env:
   - name: GRAFANA_URL
     value: "http://grafana:80"  # <- Verify this
   ```
3. Deploy:
   ```bash
   kubectl apply -f grafana-metrics-job.yaml
   ```

### 4. Verify Installation

Test the job immediately:
```bash
# Create a test job
kubectl create job --from=cronjob/grafana-metrics-updater grafana-metrics-test -n monitoring

# Check the logs
kubectl logs -n monitoring -l job-name=grafana-metrics-test
```

### 5. Check Dashboard
1. Access your Grafana UI
2. Look for "Cluster Details" dashboard
3. Verify the five panels are present:
   - Median E2E Latency
   - Median TTFT
   - Median ITL
   - Request Throughput
   - Benchmark Duration

## Maintenance

### Checking Job Status
```bash
# View all cronjobs
kubectl get cronjobs -n monitoring

# View recent jobs
kubectl get jobs -n monitoring

# View job history
kubectl get events -n monitoring
```

### Modifying the Schedule
To change the schedule (default: midnight UTC):
```bash
kubectl edit cronjob grafana-metrics-updater -n monitoring
```
Update the `schedule` field using cron syntax (e.g., `"0 0 * * *"` for midnight)

### Troubleshooting
1. Check job logs:
   ```bash
   # Get the pod name
   kubectl get pods -n monitoring | grep grafana-metrics
   
   # View logs
   kubectl logs <pod-name> -n monitoring
   ```

2. Common issues:
   - API key permissions: Ensure the service account has dashboard creation rights
   - File access: Check if the hostPath is correctly mounted
   - Python dependencies: Verify pandas and requests install correctly

### Updating the Configuration
To update the script or job configuration:
```bash
# Edit the ConfigMap
kubectl edit configmap dashboard-updater-script -n monitoring

# Or apply updated YAML
kubectl apply -f grafana-metrics-job.yaml
```

## Monitoring Tips
- Set up alerts for job failures
- Monitor disk usage where metrics files are stored
- Regularly check dashboard update timestamps
- Review Grafana logs for any dashboard creation issues
