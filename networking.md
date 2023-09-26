# Networking
## Network Policies
Network policies are a Kubernetes resource used to control and manage network traffic within a cluster by defining rules that specify which pods can communicate with each other and on what network ports and protocols. Network Policies help enforce security and access control for microservices within the cluster
### Overview
- Network Policies are Kubernetes resources used to control pod communication.
- They define rules for how pods can communicate with each other.
- Work at the pod level for fine-grained control.

### Pod Selector
- Select pods based on labels using the `podSelector` field.
- Rules can be applied to pods with specific labels.

### Ingress and Egress Rules
- Two rule types: `ingress` and `egress`.
- `Ingress` controls incoming traffic to pods.
- `Egress` controls outgoing traffic from pods.

### Pod Communication
- By default, pods can communicate within a namespace.
- Network Policies restrict communication based on rules.

### Rule Matching
- Rules are matched in order, top to bottom.
- First matching rule is applied; subsequent rules are ignored.

### Policy Types
- Two types: `ClusterNetworkPolicy` and `NetworkPolicy`.
- CKAD focuses on `NetworkPolicy`.

### Example Network Policy YAML
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: database-allow-api-policy
spec:
  podSelector:
    matchLabels:
      app: database
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: api
```
  - It applies to pods labeled with app: database.
  - It specifies an Ingress rule that allows incoming traffic only from pods labeled with app: api.
