# Storage Persistence

## Volumes in Kubernetes?
- A volume in Kubernetes is a directory that is accessible to containers in a pod.
- Volumes provide a way to persist and share data across containers within the same pod.
- They are used to handle data that needs to survive pod restarts, scaling, and rescheduling.

## Types of Volumes in Kubernetes:
- **EmptyDir:** A temporary volume that is empty when a pod is created and exists as long as the pod is running. Data is erased when the pod is deleted.
- **HostPath:** Mounts a file or directory from the host machine's filesystem into the pod. Useful for sharing files with the host or accessing host-specific resources.
- **ConfigMap:** Mounts configuration data stored in a ConfigMap as a volume. Useful for injecting configuration files into containers.
- **Secret:** Mounts sensitive data stored in a Secret as a volume. Useful for securely storing and accessing credentials and secrets.
- **PersistentVolumeClaim (PVC):** An abstraction for requesting and using persistent storage in a pod. PVCs are used to consume Persistent Volumes (PVs).

## Persistent Volumes (PVs):
- PVs are cluster-wide resources that represent storage volumes provisioned in the cluster, such as network-attached storage, cloud-based storage, or local storage.
- They can be provisioned statically by administrators or dynamically using StorageClasses.
- PVs have attributes like access modes (e.g., ReadWriteOnce, ReadOnlyMany, ReadWriteMany), capacity, and storage type.

## Persistent Volume Claims (PVCs):
- PVCs are requests for storage made by pods or developers.
- They define the desired storage characteristics such as capacity and access mode.
- PVCs can be dynamically bound to available PVs based on their requirements.
- PVCs have a lifecycle tied to the pod; when a pod is deleted, the associated PVC is released and can be reused.

## Examples
### EmptyDir Volume
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: emptydir-pod
spec:
  containers:
    - name: nginx-container
      image: nginx:latest
      volumeMounts:
        - name: emptydir-volume
          mountPath: /data
  volumes:
    - name: emptydir-volume
      emptyDir: {}
```
### HostPath Volume
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hostpath-pod
spec:
  containers:
    - name: my-app-container
      image: my-app-image:latest
      volumeMounts:
        - name: hostpath-volume
          mountPath: /data
  volumes:
    - name: hostpath-volume
      hostPath:
        path: /host-data

```
### ConfigMap Volume
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: configmap-pod
spec:
  containers:
    - name: my-app-container
      image: my-app-image:latest
      volumeMounts:
        - name: configmap-volume
          mountPath: /config
  volumes:
    - name: configmap-volume
      configMap:
        name: my-config-map
```
### Secret Volume
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-pod
spec:
  containers:
    - name: my-app-container
      image: my-app-image:latest
      volumeMounts:
        - name: secret-volume
          mountPath: /secrets
  volumes:
    - name: secret-volume
      secret:
        secretName: my-secret
```
### Persistent Volume Claim
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
apiVersion: v1
kind: Pod
metadata:
  name: pvc-pod
spec:
  containers:
    - name: my-app-container
      image: my-app-image:latest
      volumeMounts:
        - name: pvc-volume
          mountPath: /data
  volumes:
    - name: pvc-volume
      persistentVolumeClaim:
        claimName: my-pvc
```
