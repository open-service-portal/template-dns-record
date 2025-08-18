# DNS Record Template

Advanced Crossplane v2 template for managing DNS records using namespaced XRs.

## Overview

This template provides DNS record management using Crossplane v2's latest features including:
- **Namespaced XRs** - Developers create XDNSRecord directly (no claims needed!)
- **Go Templating** for flexible resource creation
- **Environment Configs** for shared DNS zone configuration  
- **Direct Kubernetes resource creation** without provider-kubernetes
- **Status field updates** to return computed FQDN

## Contents

- `xrd.yaml` - Composite Resource Definition (XRD) with namespaced scope
- `composition.yaml` - Composition using Pipeline mode with Go templating
- `rbac.yaml` - RBAC permissions for Crossplane to create ConfigMaps
- `examples/xr.yaml` - Example XDNSRecord resources (direct creation, no claims)

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

### 2. Verify Environment Config
```bash
# dns-config should be installed by setup-cluster.sh
kubectl get environmentconfig dns-config

# If missing, run the setup script or create manually
```

### 3. Install the XRD
```bash
kubectl apply -f xrd.yaml
```

### 4. Install the Composition
```bash
kubectl apply -f composition.yaml
```

### 5. Create a DNS Record
```bash
# Apply XDNSRecord
kubectl apply -f examples/xr.yaml

# Check the status
kubectl get xdnsrecords -A
kubectl describe xdnsrecord my-app-dns -n default
```

## Usage

### Creating a DNS Record (Namespaced XR)

With Crossplane v2, developers create XDNSRecord resources directly in their namespaces:

```yaml
apiVersion: platform.io/v1alpha1
kind: XDNSRecord
metadata:
  name: my-app
  namespace: default 
spec:
  type: A
  name: my-app
  value: "192.168.1.100"
  ttl: 3600
```

**Key Difference**: No separate claim resource needed! The XDNSRecord is created directly.

### Supported Record Types
- **A** - IPv4 address
- **AAAA** - IPv6 address  
- **CNAME** - Canonical name (alias)
- **TXT** - Text record

### Getting the FQDN

The composition automatically computes and returns the FQDN in the status:

```bash
kubectl get xdnsrecord my-app -n default -o jsonpath='{.status.fqdn}'
# Output: my-app.portal.example.com
```

## How It Works

1. **XR Creation**: Developer creates XDNSRecord directly in their namespace
2. **Environment Loading**: Composition loads DNS zone from EnvironmentConfig
3. **Resource Creation**: Go template creates a ConfigMap in the same namespace
4. **Status Update**: FQDN is computed and returned in status
5. **Ready State**: Auto-ready function marks the resource as ready

## Restaurant Analogy

Using our restaurant analogy from the documentation:
- **XRD (xrd.yaml)** = The Menu - Shows what DNS records you can order
- **Composition** = The Recipe - How to create the DNS record
- **Environment Config** = The Kitchen Settings - Shared configuration (DNS zone)
- **XDNSRecord** = The Direct Order - Developers order directly (v2 style, no waiter/claim needed!)
- **Functions** = The Kitchen Equipment - Tools for complex preparation
- **RBAC** = Kitchen Access - Who can use what equipment
- **Namespace** = The Table - Where your order is delivered

## Customization

### Changing the DNS Zone
Edit the platform environment config:
```bash
# Edit the dns-config EnvironmentConfig
kubectl edit environmentconfig dns-config

# Or update via the platform setup:
# scripts/cluster-manifests/environment-configs.yaml
```

### Adding More Record Types
Update the XRD to include additional DNS record types in the enum.

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
# Check environment config exists (installed by setup-cluster.sh)
kubectl get environmentconfig dns-config

# View the config
kubectl describe environmentconfig dns-config

# Check composition pipeline logs
kubectl logs -n crossplane-system deployment/crossplane -f | grep environment
```

### DNS Record Not Creating
```bash
# Check XRD status
kubectl get xrd xdnsrecords.platform.io

# Check XDNSRecord resources
kubectl get xdnsrecord -A

# Check created ConfigMap
kubectl get configmap -A | grep dns

# View XR events
kubectl describe xdnsrecord my-app-dns -n default
```

## Links and Resources

- [Crossplane v2 Documentation](https://docs.crossplane.io/latest/)
- [Go Templating Function](https://github.com/crossplane-contrib/function-go-templating)
- [Environment Configs](https://docs.crossplane.io/latest/concepts/environment-configs/)
- [Composition Functions](https://docs.crossplane.io/latest/concepts/composition-functions/)