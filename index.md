# Core Concepts

## Pods
- Summary: Pods are the basic building blocks of Kubernetes and represent a single instance of a running process in a cluster. They encapsulate one or more containers, shared storage, network, and other resources.
- Most Used Commands:
  - Create in declarative way: `kubectl apply -f pod.yaml`
  - Create in imperative way: `kubectl run pod-name --image=image-name`
  - Get information: `kubectl get pods`
  - Get detailed information: `kubectl describe pod pod-name`
- Example YAML:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
  - name: my-container
    image: nginx:latest