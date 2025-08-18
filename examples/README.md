# DNSRecord Examples

This directory contains examples of how to use the DNSRecord Crossplane resource.

## Available Examples

### Basic Records
- `a-record.yaml` - Simple A record for application endpoints (includes local/production values)
- `cname-record.yaml` - CNAME alias records with practical use cases
- `txt-record.yaml` - TXT records for domain verification, SPF, DMARC
- `wildcard-record.yaml` - Wildcard DNS for dynamic subdomains

### Environment-Specific
- `production-example.yaml` - Production best practices with proper TTL, annotations, and namespace isolation

### Advanced Usage
- `advanced-composition.yaml` - Composition selection, provider configuration, and management policies

## Usage

To create a DNS record, apply any of these examples:

```bash
# Create a simple A record
kubectl apply -f examples/a-record.yaml

# Check the status
kubectl get dnsrecord -n default

# View details
kubectl describe dnsrecord my-app-dns -n default
```

## Notes

- In Crossplane v2, XRs (like DNSRecord) are namespaced
- No Claims are needed - you work directly with XRs
- Resources can be created in any namespace
- The actual DNS provider is configured in the Composition

## Cleanup

To remove created resources:

```bash
kubectl delete -f examples/a-record.yaml
```