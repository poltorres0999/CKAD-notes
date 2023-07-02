# Configuration

## ConfigMaps
- Summary: ConfigMaps are used to store configuration data in key-value pairs or as plain text files. They provide a way to separate configuration from application code, making it easier to manage and modify configuration settings without redeploying the application.
- Use Cases: ConfigMaps can be used to store environment variables, command-line arguments, or configuration files for applications running in pods. They allow you to decouple configuration data from application images and provide flexibility in configuration management.
- Most Used Commands:
  - Create in declarative way: `kubectl apply -f configmap.yaml`
  - Create in imperative way: `kubectl create configmap configmap-name --from-literal=key=value`
  - Get information: `kubectl get configmaps`
  - Get detailed information: `kubectl describe configmap configmap-name`
- Example YAML:
    ```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: example-configmap
    data:
      key1: value1
      key2: value2
    ```
  - Container definition example with envFrom:
    ```yaml
    spec:
    containers:
      - name: example-container
        image: nginx:latest
        ports:
          - containerPort: 80
        envFrom:
          - configMapRef:
              name: example-configmap
    ```
  - Container definition example with env:
    ```yaml
    spec:
      containers:
        - name: example-container
          image: nginx:latest
          ports:
            - containerPort: 80
          env:
            - name: MY_CONFIG_KEY
              valueFrom:
                configMapKeyRef:
                  name: example-configmap
                  key: my-key
      ```
## Secrets
- Summary: Secrets in Kubernetes are designed to securely store and distribute sensitive information to pods and containers. They are base64 encoded by default and can be mounted as files or exposed as environment variables within the application.
- Use Cases
  - Storing database passwords or credentials required by applications.
  - Managing API keys, access tokens, or other sensitive authentication information.
  - Distributing TLS certificates for securing communication between services.

- Most Used Commands
  - Create in declarative way: `kubectl apply -f secret.yaml`
  - Create in imperative way: `kubectl create secret generic secret-name --from-literal=key=value`
  - Get information: `kubectl apply -f secret.yaml`
  - Get detailed information: `kubectl describe secret secret-name`
- Example YAML:
  ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: example-secret
    type: Opaque
    data:
      username: dXNlcm5hbWU=  # Base64-encoded value
      password: cGFzc3dvcmQ=  # Base64-encoded value
  ```
  - POD example with secrets using envFrom:
    ```yaml
      apiVersion: v1
      kind: Pod
      metadata:
        name: example-pod
      spec:
        containers:
          - name: example-container
            image: nginx:latest
            envFrom:
              - secretRef:
                  name: example-secret
    ```
  - POD example with secrets using env:
    ```yaml
      apiVersion: v1
      kind: Pod
      metadata:
        name: example-pod
      spec:
        containers:
          - name: example-container
            image: nginx:latest
            env:
              - name: DB_USERNAME
                valueFrom:
                  secretKeyRef:
                    name: example-secret
                    key: username
              - name: DB_PASSWORD
                valueFrom:
                  secretKeyRef:
                    name: example-secret
                    key: password
    ```
  - POD example using mounted volumes within the container:
    ```yaml
      apiVersion: v1
      kind: Pod
      metadata:
        name: example-pod
      spec:
        containers:
          - name: example-container
            image: nginx:latest
            volumeMounts:
              - name: secret-volume
                mountPath: /etc/secrets
                readOnly: true
        volumes:
          - name: secret-volume
            secret:
              secretName: example-secret
    ```
