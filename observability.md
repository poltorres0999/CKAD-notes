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
