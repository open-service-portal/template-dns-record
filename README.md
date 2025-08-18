# DNS Record Template

Advanced Crossplane v2 template for managing DNS records in Kubernetes.

## Overview

This template provides DNS record management using Crossplane v2's advanced features including:
- **Go Templating** for flexible resource creation
- **Environment Configs** for shared DNS zone configuration  
- **Direct Kubernetes resource creation** without provider-kubernetes
- **Status field updates** to return computed FQDN

## Contents

- `xrd.yaml` - Composite Resource Definition (XRD) that defines the DNS record API
- `composition.yaml` - Composition using Pipeline mode with Go templating
- `environmentconfig.yaml` - DNS zone configuration shared across all records
- `rbac.yaml` - RBAC permissions for Crossplane to create ConfigMaps
- `examples/` - Example claims showing how to use this template

## Requirements

### Core Requirements
- Crossplane v2.0+
- Kubernetes cluster
- Flux (for GitOps sync)

### Composition Functions
Crossplane v2 requires composition functions for Pipeline mode (installed by setup-cluster.sh):
- function-go-templating
- function-environment-configs
- function-auto-ready
- function-patch-and-transform

## Setup Instructions

### 1. Apply RBAC Permissions
```bash
# Grant Crossplane permission to create ConfigMaps
kubectl apply -f rbac.yaml
```

### 3. Create Environment Config
```bash
# Configure the DNS zone for your platform
kubectl apply -f environmentconfig.yaml
```

### 4. Install the XRD
```bash
kubectl apply -f xrd.yaml
```

### 5. Install the Composition
```bash
kubectl apply -f composition.yaml
```

### 6. Create a DNS Record
```bash
kubectl apply -f examples/claim.yaml

# Check the status
kubectl get dnsrecords
kubectl describe dnsrecord my-app-dns
```

## Usage

### Creating a DNS Record

```yaml
apiVersion: platform.io/v1alpha1
kind: DNSRecord
metadata:
  name: my-app
  namespace: default
spec:
  type: A
  name: my-app
  value: "192.168.1.100"
  ttl: 3600
```

### Supported Record Types
- **A** - IPv4 address
- **AAAA** - IPv6 address  
- **CNAME** - Canonical name (alias)
- **TXT** - Text record

### Getting the FQDN

The composition automatically computes and returns the FQDN in the status:

```bash
kubectl get dnsrecord my-app -o jsonpath='{.status.fqdn}'
# Output: my-app.portal.example.com
```

## How It Works

1. **Claim Creation**: Developer creates a DNSRecord claim
2. **Environment Loading**: Composition loads DNS zone from EnvironmentConfig
3. **Resource Creation**: Go template creates a ConfigMap with DNS data
4. **Status Update**: FQDN is computed and returned in status
5. **Ready State**: Auto-ready function marks the resource as ready

## Crossplane v2 Features Used

### Advanced Features
1. **Pipeline Mode Composition** - Sequential processing with functions
2. **Go Templating** - Flexible resource generation with logic
3. **Environment Configs** - Shared configuration across resources
4. **Direct Resource Creation** - No provider needed for Kubernetes resources
5. **Status Patching** - Return computed values to users

### Standard Features
1. **Default Composition Reference** - XRD specifies default composition
2. **Composition Selector** - Claims can choose different compositions
3. **Validation** - Schema validation with patterns and constraints
4. **Version Annotations** - Clear version tracking

## Restaurant Analogy

Using our restaurant analogy from the documentation:
- **XRD (xrd.yaml)** = The Menu - Shows what DNS records you can order
- **Composition** = The Recipe - How to create the DNS record
- **Environment Config** = The Kitchen Settings - Shared configuration (DNS zone)
- **Claim** = The Order - What the developer requests
- **Functions** = The Kitchen Equipment - Tools for complex preparation
- **RBAC** = Kitchen Access - Who can use what equipment

## Customization

### Changing the DNS Zone
Edit `environmentconfig.yaml`:
```yaml
data:
  zone: your-domain.com  # Change this to your DNS zone
```

### Adding More Record Types
Update the XRD to include additional DNS record types in the enum.

### Using Different Storage
Instead of ConfigMap, you could modify the Go template to create:
- Secrets (for sensitive DNS data)
- Custom Resources (if you have a DNS operator)
- External API calls (using webhook functions)

## Troubleshooting

### Functions Not Working
```bash
# Check function status
kubectl get functions
kubectl describe function function-go-templating

# Check composition events
kubectl describe composition dnsrecord
```

### RBAC Issues
```bash
# Verify RBAC is applied
kubectl get clusterrole | grep crossplane

# Check Crossplane service account permissions
kubectl auth can-i create configmaps --as=system:serviceaccount:crossplane-system:crossplane
```

### Environment Config Not Loading
```bash
# Check environment config exists
kubectl get environmentconfig dns-config

# Check composition pipeline logs
kubectl logs -n crossplane-system deployment/crossplane -f | grep environment
```

### DNS Record Not Creating
```bash
# Check XRD status
kubectl describe xdnsrecord

# Check created ConfigMap
kubectl get configmap -A | grep dns

# View claim events
kubectl describe dnsrecord my-app-dns
```

## Links and Resources

- [Crossplane v2 Documentation](https://docs.crossplane.io/latest/)
- [Go Templating Function](https://github.com/crossplane-contrib/function-go-templating)
- [Environment Configs](https://docs.crossplane.io/latest/concepts/environment-configs/)
- [Composition Functions](https://docs.crossplane.io/latest/concepts/composition-functions/)