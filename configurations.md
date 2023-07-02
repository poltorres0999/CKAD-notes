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

## Resource Requirements

In Kubernetes, you can specify the resource requirements (such as CPU and memory) for your Pods and Containers. These requirements help the Kubernetes scheduler make decisions about how to allocate resources across the cluster.

- Summary

  Resource requirements allow you to specify the amount of CPU and memory that each Pod or Container needs to run effectively. By setting resource limits and requests, you can ensure that Pods are scheduled appropriately and prevent resource contention issues within the cluster.

- Use Cases

  - Ensuring that Pods have sufficient resources to operate without performance degradation.
  - Preventing individual Pods from consuming excessive resources and impacting the stability of other Pods.
  - Facilitating efficient resource allocation across the cluster by allowing the scheduler to make informed placement decisions.
  - 
- Most Used Commands

  - Set CPU resource limits and requests for a Pod or Container:
    ```yaml
    spec:
      containers:
      - name: my-container
        resources:
          limits:
            cpu: "1"
          requests:
            cpu: "0.5"
    ```
  - Set CPU resource limits and requests for a Pod or Container:
  ```yaml
  spec:
    containers:
    - name: my-container
      resources:
        limits:
          memory: "2Gi"
        requests:
          memory: "1Gi"
  ```
  - View resource allocation and utilization of Pods: `kubectl top pods`
  - View resource allocation and utilization of Nodes:: `kubectl top nodes`
### Limit Ranges and Resource Quotas
#### Limit Ranges

- Limit Ranges allow you to define default and maximum resource limits for a Kubernetes namespace.
- They ensure that Pods created within the namespace adhere to the specified resource constraints.
- By setting default limits and requests, Limit Ranges ensure that Pods have resource allocations even if not explicitly defined.
- Limit Ranges promote fairness and stability by preventing individual Pods from consuming excessive resources.
  
- Limit Range Example:
  ```yaml
  apiVersion: v1
  kind: LimitRange
  metadata:
    name: my-limit-range
  spec:
    limits:
      - default:
          cpu: "500m"
          memory: "512Mi"
        defaultRequest:
          cpu: "100m"
          memory: "256Mi"
        type: Container
  ```

    This LimitRange configuration sets the default and maximum resource limits for Containers within a Kubernetes namespace. The default CPU limit is 500 milliCPU (0.5 CPU), the default memory limit is 512 megabytes (MiB), the default CPU request is 100 milliCPU (0.1 CPU), and the default memory request is 256 megabytes (MiB).

#### Resource Quotas

- Resource Quotas enable you to limit resource consumption within a Kubernetes namespace.
- They set maximum limits for CPU, memory, storage, and other resources to prevent excessive usage.
- Resource Quotas help manage resource allocation and prevent resource contention within a cluster.
- They provide control and isolation at the namespace level, allowing for fair resource distribution among different projects or teams.

- Resource Quota example:
  ```yaml
    apiVersion: v1
    kind: ResourceQuota
    metadata:
      name: my-resource-quota
    spec:
      hard:
        cpu: "2"
        memory: 2Gi
        pods: "10"
  ```
  In this example, the Resource Quota restricts the namespace to a maximum of 2 CPUs, 2Gi of memory, and 10 Pods.
  
## Taints and Tolerations

Taints and Tolerations are used to control which pods can be scheduled on which nodes in a Kubernetes cluster.
A taint is applied to a node, and it repels pods unless they have a matching toleration.
Tolerations are specified in the pod specification and allow pods to tolerate specific taints on nodes.
Taints and tolerations provide a way to dedicate certain nodes for specific pods or enforce specific node constraints.

- Commands:
  - To taint a node: `kubectl taint nodes <node_name> <taint_key>=<taint_value>:<taint_effect>`
  - To view taints on nodes: `kubectl describe node <node_name>`
  
  -  To add a toleration to a pod specification:
      ```yaml
      Copy code
      spec:
        tolerations:
          - key: <taint_key>
            operator: <taint_operator>
            value: <taint_value>
            effect: <taint_effect>
      ```
## Node Selectors
- Node Selectors allow you to schedule pods on nodes that match a set of predefined labels.
- With node selectors, you can specify the label requirements in a pod's specification, ensuring it runs on the desired nodes.
- Node selectors are useful when you have specific nodes in your cluster with certain capabilities or resources.

- Commands:
  - Specify a node selector in the pod specification using labels:
    ```yaml
    spec:
    nodeSelector:
      key: value
    ```
  - Add a label to a node: `kubectl label nodes <node_name> key=value`
  - List nodes with a specific label: `kubectl get nodes -l key=value`
  - List pods and the corresponding nodes that match specific labels:: `kubectl get pods -o wide --selector key=value`
  - Add a node selector to an existing pod: `kubectl patch pod <pod_name> -p '{"spec": {"nodeSelector": {"key": "value"}}}'`
  - Remove a node selector from an existing pod: `kubectl patch pod <pod_name> -p '{"spec": {"nodeSelector": null}}'`
  - To specify a node selector in a pod specification:
    ```yaml
      spec:
        nodeSelector:
          <label_key>: <label_value>
    ```
## Node Affinity
Node affinity is a feature in Kubernetes that allows you to influence the scheduling of pods onto specific nodes based on node properties or labels. It provides fine-grained control over pod placement and enables you to define rules to ensure pods are scheduled on nodes that meet certain criteria.

- Types of affinity:
  - **RequiredDuringSchedulingIgnoredDuringExecution:** Ensures that pods are scheduled only on nodes that satisfy the specified rules.
  - **PreferredDuringSchedulingIgnoredDuringExecution:** Allows the scheduler to prioritize scheduling pods on nodes that satisfy the specified rules, but it's not a hard requirement.
  - **RequiredDuringSchedulingRequiredDuringExecution:** Ensures that pods are scheduled only on nodes that satisfy the specified rules and remains on those nodes during their entire lifecycle.
  - **PreferredDuringSchedulingRequiredDuringExecution:** Allows the scheduler to prioritize scheduling pods on nodes that satisfy the specified rules and ensures that the pods remain on those nodes during their entire lifecycle, but it's not a hard requirement.

- Commands:
  - To specify node affinity in a pod specification:
    ```yaml
      spec:
        affinity:
          nodeAffinity:
            requiredDuringSchedulingIgnoredDuringExecution:
              nodeSelectorTerms:
              - matchExpressions:
                - key: <label_key>
                  operator: <label_operator>
                  values:
                  - <label_value>
      ```
