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
  ```

## ReplicaSets
- Summary: ReplicaSets ensure the desired number of pod replicas are running at all times. They provide high availability and scalability by automatically maintaining a specified number of identical pods.
- Most Used Commands:
  - Create in declarative way: `kubectl apply -f replicaset.yaml`
  - Create in imperative way: `kubectl create replicaset --image=image-name`
  - Get information: `kubectl get replicasets`
  - Get detailed information: `kubectl describe replicaset replicaset-name`
- Example YAML:
  ```yaml
  apiVersion: apps/v1
  kind: ReplicaSet
  metadata:
    name: example-replicaset
  spec:
    replicas: 3
    selector:
      matchLabels:
        app: my-app
    template:
      metadata:
        labels:
          app: my-app
      spec:
        containers:
        - name: my-container
          image: nginx:latest
  ```

## Deployments
- Summary: Deployments are a higher-level abstraction that manages ReplicaSets and provides declarative updates to pods and their replicas. They enable easy scaling, rolling updates, and rollbacks of application deployments.
- Most Used Commands:
  - Create in declarative way: `kubectl apply -f deployment.yaml`
  - Create in imperative way: `kubectl create deployment --image=image-name`
  - Get information: `kubectl get deployments`
  - Get detailed information: `kubectl describe deployment deployment-name`
  - Scale deployment: `kubectl scale deployment deployment-name --replicas=3`
  - Perform rolling update: `kubectl set image deployment/deployment-name container-name=image-name`
  - Perform rollback: `kubectl rollout undo deployment/deployment-name`
- Example YAML:
  ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: example-deployment
  spec:
    replicas: 3
    selector:
      matchLabels:
        app: my-app
    template:
      metadata:
        labels:
          app: my-app
      spec:
        containers:
        - name: my-container
          image: nginx:latest
  ```

## Namespaces
- Summary: Namespaces provide a way to create virtual clusters within a Kubernetes cluster, allowing multiple teams or projects to share the same physical infrastructure while maintaining isolation and resource management.
- Most Used Commands:
  - Create in declarative way: `kubectl apply -f namespace.yaml`
  - Create in imperative way: `kubectl create namespace namespace-name`
  - Get information: `kubectl get namespaces`
  - Get detailed information: `kubectl describe namespace namespace-name`
- Example YAML:
  ```yaml
  apiVersion: v1
  kind: Namespace
  metadata:
    name: example-namespace
  ```
