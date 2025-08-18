# DNSRecord Examples

This directory contains examples of how to use the DNSRecord Crossplane resource.

## Available Examples

### Basic Records
- `a-record.yaml` - Simple A record pointing to an IP address
- `cname-record.yaml` - CNAME record pointing to another domain
- `txt-record.yaml` - TXT record for domain verification

### Environment-Specific
- `production-example.yaml` - Example for production namespace with lower TTL

### Advanced Usage
- `advanced-composition.yaml` - Using composition selectors and references

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