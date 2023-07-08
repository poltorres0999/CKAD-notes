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
