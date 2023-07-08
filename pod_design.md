# POD Design
## Labels, Selectors and Annotations
### Labels
- Labels are key-value pairs attached to Kubernetes resources such as pods, services, or deployments. They are used to identify and organize resources based on specific characteristics or attributes.
- Labels enable grouping, filtering, and selecting resources for various purposes such as grouping related resources, applying configuration or policies, and defining relationships between resources.
- Labels example:
  ```yaml
  metadata:
  labels:
    app: my-app
    tier: frontend
  ```
### Selectors
- Selectors are used to query and filter resources based on their labels. They allow you to select and operate on specific resources that match certain label criteria.
- Selectors enable the grouping and selection of resources based on their shared labels, facilitating actions like scaling, updating, or deleting multiple resources at once.
- Selectors example:
  ```yaml
   spec:
    selector:
      matchLabels:
        app: my-app
        tier: frontend

  ```
### Annotations
- Annotations are key-value pairs similar to labels, but they provide additional information about a resource that is not used for identification or grouping purposes.
- Annotations allow you to attach arbitrary metadata or notes to a resource. They are often used to store descriptive or operational details, such as version numbers, release notes, or monitoring information.
  ```yaml
    metadata:
      annotations:
        description: "This is my application"
        version: "1.0"
    ```
**Usage example of Labels, Selectors and Annotations**

The following example demonstrates the use of labels, selectors and annotations by deplying two applications: an API built in Fast APi and a Front-End based in React.
- FastAPI Deployment
  ```yaml
    apiVersion: v1
    kind: Deployment
    metadata:
    name: fastapi-app
    spec:
    template:
      metadata:
        labels:
          app: fastapi-app
          tier: backend
      spec:
        containers:
          - name: fastapi-container
            image: fastapi-app:latest
            ports:
              - containerPort: 8000
            env:
              - name: CONFIG_MAP_KEY
                valueFrom:
                  configMapKeyRef:
                    name: fastapi-configmap
                    key: config-key
  ```
- Fast API Service
  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: fastapi-service
  spec:
    selector:
      app: fastapi-app
      tier: backend
    ports:
      - protocol: TCP
        port: 80
        targetPort: 8000
  ```
- React Deployment
  ```yaml
    apiVersion: v1
    kind: Deployment
    metadata:
      name: react-frontend
      annotations:
        description: "React frontend deployment"
        version: "1.0"
    spec:
      template:
        metadata:
          labels:
            app: react-frontend
            tier: frontend
        spec:
          containers:
            - name: react-container
              image: react-frontend:latest
              ports:
                - containerPort: 3000
              env:
                - name: CONFIG_MAP_KEY
                  valueFrom:
                    configMapKeyRef:
                      name: react-configmap
                      key: config-key
    ```
## Rolling Updates and Rollbacks
**Rolling Updates:**
- **Definition**: Rolling updates in Kubernetes allow you to update your application or deployment to a new version without downtime. It follows a gradual and controlled approach, ensuring availability while transitioning to the updated version.
- **Process**: Rolling updates involve creating new instances of the updated version, gradually scaling up while scaling down the instances of the previous version. This allows for a smooth transition without interrupting the running workload.
- **Benefits**: Rolling updates provide the ability to update applications seamlessly, with features like zero-downtime deployments, automated rollbacks, and the ability to monitor the update progress.

**Example**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  annotations:
    kubernetes.io/change-cause: "Updating to version v2"
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
      - name: my-app
        image: my-app:v2
        ports:
        - containerPort: 80
  ```
  **Explanation**
  
  - In this example, a deployment named my-app is defined with three replicas.
  - The deployment specifies the updated version (my-app:v2) in the container's image field.
  - The annotation kubernetes.io/change-cause is added to provide a description or reason for the update.
  - During a rolling update, Kubernetes will create new pods with the updated version while scaling down the pods with the previous version gradually.
    
  **Rollout History**

  - Kubernetes keeps a history of the deployment updates, including revision numbers and details.
  - The ``kubectl rollout history`` command allows you to view the revision history of a deployment, providing information about when each revision was deployed and any associated annotations.
    
## Rollbacks

- **Definition**: Rollbacks in Kubernetes refer to reverting to a previous version of an application or deployment when an update or new version encounters issues or failures.
- **Process**: Kubernetes keeps track of the deployment history and allows you to rollback to a specific revision, which effectively scales down the new version and scales up the previous version.
- **Benefits**: Rollbacks provide a safety net to quickly recover from failed updates or unexpected issues, ensuring the stability and availability of the application.

**Example:**

```shell
kubectl rollout history deployment my-app
kubectl rollout undo deployment my-app --to-revision=2
```
