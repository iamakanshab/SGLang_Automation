# Grafana Onboarding Guide

Welcome to our Grafana monitoring platform! This guide will help you get started with creating and managing your dashboards.

## Quick Start
- **Dev Cluster URL**: http://abbd9a1ab0f904666978153cc034f473-729179930.us-west-2.elb.amazonaws.com (TO REPLACE WITH DOMAIN URL)
- **Prod Cluster URL**: http://k8s-monitori-grafana-810b5f6f62-535518310.us-west-2.elb.amazonaws.com (TO REPLACE WITH DOMAIN URL)

## Access Requirements
1. AWS Account access
2. Kubernetes cluster access
3. Basic understanding of metrics and visualization

## Getting Started

### 1. Request Access
Contact your team administrator to:
- Create a Grafana user account
- Get added to appropriate teams/organizations
- Receive necessary dashboard permissions

### 2. Authentication
We support two authentication methods:
- Direct login with username/password
- SSO authentication (recommended)

### 3. Creating API Keys
For automated dashboard updates:
1. Navigate to Configuration → API Keys
2. Click "Add API key"
3. Set appropriate permissions:
   - Viewer: Read-only access
   - Editor: Can edit dashboards
   - Admin: Full access (use cautiously)

## Dashboard Management

### Creating New Dashboards
1. Click "+" in the side menu
2. Select "Create New Dashboard"
3. Choose visualization type:
   - Graphs
   - Tables
   - Stats
   - Heatmaps

### Best Practices
1. **Naming Convention**
   ```
   [Team]_[Service]_[MetricType]
   Example: Team1_Authentication_Latency
   ```

2. **Dashboard Organization**
   - Use folders for different teams/services
   - Include description and documentation
   - Tag dashboards appropriately

3. **Panel Design**
   - Clear titles and descriptions
   - Consistent units
   - Appropriate time ranges
   - Helpful tooltips

### Data Sources
Currently supported data sources:
- Prometheus
- AWS CloudWatch
- Custom JSON/CSV uploads

## Automation

### Using Our Dashboard Updater
We provide a custom dashboard updater for automated metrics:

```bash
# Create required secret
kubectl create secret generic grafana-api-key \
  --from-literal=api-key=YOUR_API_KEY \
  -n monitoring
```

## Examples

### Sample Dashboard Structure
```json
{
  "dashboard": {
    "title": "Service Metrics",
    "tags": ["team1", "auth"],
    "timezone": "browser",
    "panels": [
      {
        "title": "Request Latency",
        "type": "graph",
        ...
      }
    ]
  }
}
```

### Common Metrics
- Request Rate
- Error Rate
- Latency (p50, p95, p99)
- Resource Usage
- Custom Business Metrics

### Common Issues

#### Cannot Access Dashboard
- Verify network connectivity
- Check permissions
- Ensure correct authentication

#### Data Not Showing
- Verify data source connection
- Check metric names
- Validate time range selection

## Security Guidelines

### Best Practices
1. Never share API keys
2. Use appropriate permission levels
3. Regularly rotate credentials
4. Audit dashboard access

### Sensitive Data
- No PII in dashboards
- No credentials in queries
- No sensitive business metrics without proper access control

## Contributing

### Adding Features
1. Fork the dashboard repository
2. Make changes
3. Submit PR for review

### Reporting Issues
Create a ticket including:
- Dashboard URL
- Expected behavior
- Actual behavior
- Screenshots if applicable

## Version Control

### Dashboard Versions
- All dashboards are version controlled
- Use the "Save As" feature for major changes
- Document significant updates

## Maintenance

### Regular Tasks
- Review unused dashboards
- Update API keys before expiration
- Validate data source connections
- Clean up old alerts

### Backup and Recovery
- Dashboards are automatically backed up daily
- Recovery process documented in [Guide](guide-link WIP)

## Future Updates
- Upcoming features
- Planned integrations
- Migration schedules
