# Observability
## Liveness Probes
- Liveness probes are used to determine if a container in a pod is alive and functioning properly.
- They periodically execute a specified command inside the container or make an HTTP request to a specified endpoint.
- If the probe fails (command returns an error or HTTP request returns a non-success status code), Kubernetes restarts the container.
- Liveness probes help in automatic recovery and maintaining the desired state of the application.
- Common types of liveness probes include:
  - ExecProbe: Executes a command inside the container.
  - TCPSocketProbe: Checks if a specific port is open and accepting connections.
  - HTTPGetProbe: Makes an HTTP GET request to a specified endpoint.
```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: liveness-pod
  spec:
    containers:
      - name: my-container
        image: my-container-image:latest
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 15
          periodSeconds: 10
```
In this example, the livenessProbe field is added to the container specification within the pod definition. It configures an HTTP GET probe to the /health endpoint on port 8080. The probe will start after an initial delay of 15 seconds and will be repeated every 10 seconds. If the probe fails, Kubernetes will restart the container.

## Readiness Probes
- Readiness probes are used to determine if a container in a pod is ready to serve traffic.
- They are similar to liveness probes but specifically focused on indicating when a container is ready to receive requests.
- Readiness probes can help prevent traffic from being directed to a container that is still starting up or not fully prepared to handle requests.
- If a container fails its readiness probe, it is temporarily removed from service until it passes the probe.
- Common types of readiness probes include:
  - ExecProbe: Executes a command inside the container.
  - TCPSocketProbe: Checks if a specific port is open and accepting connections.
  - HTTPGetProbe: Makes an HTTP GET request to a specified endpoint.
**Readiness Probes Example**
```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: readiness-pod
    spec:
      containers:
        - name: my-container
          image: my-container-image:latest
          readinessProbe:
            httpGet:
              path: /ready
              port: 8080
            initialDelaySeconds: 10
            periodSeconds: 5
```
In this example, the readinessProbe field is added to the container specification within the pod definition. It configures an HTTP GET probe to the /ready endpoint on port 8080. The probe will start after an initial delay of 10 seconds and will be repeated every 5 seconds. If the probe fails, the container is considered not ready to receive traffic.

## Logging and Troubleshooting:
### Accessing Container Logs:
- Use the kubectl logs command to access the logs of a specific container within a pod.
- Syntax: kubectl logs <pod-name> [-c <container-name>]
- Example: kubectl logs my-pod -c my-container

### Troubleshooting Application or Pod Failures

- Inspect the container logs to identify any error messages or unexpected behavior that may indicate the cause of the failure.
- Troubleshoot common issues such as network connectivity problems, resource constraints, or configuration errors.
  
  Use the following commands to assist with troubleshooting:

  **Describing Pods**
  
    - Use the kubectl describe pod command to get detailed information about a pod, including its current state, events, and related objects.
    - Syntax: kubectl describe pod <pod-name>
    - Example: kubectl describe pod my-pod
    
  **Inspecting Pod Events**
  
  - Use the kubectl get events command to view events related to pods in the cluster.
  - To filter events specific to a pod, use the --field-selector flag with the involvedObject.name field.
  - Syntax: kubectl get events --field-selector involvedObject.name=<pod-name>
  - Example: kubectl get events --field-selector involvedObject.name=my-pod
  
  **Executing Commands in Containers**
  - Use the kubectl exec command to execute commands directly within a running container for debugging purposes.
  - Syntax: kubectl exec -it <pod-name> [-c <container-name>] -- <command>
  - Example: kubectl exec -it my-pod -c my-container -- /bin/bash
