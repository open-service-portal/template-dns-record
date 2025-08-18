# DNS Record Template

Crossplane template for managing DNS records in Kubernetes.

## Contents

- `xrd.yaml` - Composite Resource Definition (XRD) that defines the DNS record API
- `composition.yaml` - Composition that implements the DNS record using Kubernetes resources
- `examples/` - Example claims showing how to use this template

## Usage

This template will be automatically installed when added to the catalog repository.

### Creating a DNS Record

```yaml
apiVersion: platform.io/v1alpha1
kind: DNSRecord
metadata:
  name: my-dns-record
spec:
  name: example.com
  type: A
  value: 192.168.1.1
```

## Restaurant Analogy

Using our restaurant analogy from the documentation:
- **XRD (xrd.yaml)** = The Menu - Shows what DNS records you can order
- **Composition (composition.yaml)** = The Recipe - How to create the DNS record
- **Claim (examples/claim.yaml)** = The Order - What the developer requests

## Requirements

- Crossplane v2.0+
- Kubernetes cluster
- Flux (for GitOps sync)