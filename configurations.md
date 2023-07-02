# Configuration

## ConfigMaps
- Summary
  
  ConfigMaps are used to store configuration data in key-value pairs or as plain text files. They provide a way to separate configuration from application code, making it easier to manage and modify configuration settings without redeploying the application.

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
- Summary
  
  Secrets in Kubernetes are designed to securely store and distribute sensitive information to pods and containers. They are base64 encoded by default and can be mounted as files or exposed as environment variables within the application.
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
## Security Context
- Summary

  The Security Context defines the security attributes for a pod or container. It includes settings like the user and group IDs, file permissions, and Linux capabilities. By configuring the Security Context, you can enhance the security of your applications and isolate their access rights.

- Use Cases

  - Running containers with non-root users to minimize the impact of security vulnerabilities.
  - Restricting the use of privileged access or restricting the capabilities available within containers.
  - Controlling the Linux user and group IDs to manage file access permissions within the container.

- Set a Security Context in a Pod or Container:
  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: example-pod
  spec:
    securityContext:
      runAsUser: 1000
      runAsGroup: 2000
      fsGroup: 3000
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
    containers:
      - name: example-container
        image: nginx:latest
  ```
## Service Account

A Service Account in Kubernetes is an identity that is used by Pods to authenticate and authorize access to other resources within the cluster. It provides an identity for processes running within a Pod and allows them to interact with the Kubernetes API and other cluster resources.

- Summary

  A Service Account represents an identity within the Kubernetes cluster and is associated with one or more Pods. It provides a way to authenticate and authorize requests made by the Pods to various resources, such as other Pods, Services, or Secrets. Service Accounts can be used to control access permissions and implement security policies within the cluster.

- Use Cases

  - Authenticating requests made by Pods to the Kubernetes API server.
  - Authorizing Pods to access other resources within the cluster, such as Secrets, ConfigMaps, or other Services.
  - Enforcing RBAC (Role-Based Access Control) policies by associating Service Accounts with specific Roles or RoleBindings.
    
- Most Used Commands
  - Create a service account `kubectl create serviceaccount <service-account-name>`
  - Get details of a Service Account: `kubectl get serviceaccount <service-account-name>`
  - Describe a Service Account: `kubectl describe serviceaccount <service-account-name>`
  - Associate a Service Account with a Pod:
      ```yaml
        spec:
          serviceAccountName: <service-account-name>
      ```
  - Grant additional RBAC roles to a Service Account: `kubectl create rolebinding <role-binding-name> --role=<role-name> --serviceaccount=<namespace>:<service-account-name>`
