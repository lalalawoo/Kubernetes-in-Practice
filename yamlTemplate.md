### Pod
#### using secret as environment variable
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-q15-volume
spec:
  containers:
  - name: pod-volume
    image: nginx
    volumeMounts:
    - name: vol
      mountPath: /path
  volumes:
  - name: vol
    secret:
      secretName: q15secret
```
#### using secret in volumn
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-q15-env
spec:
  containers:
  - name: pod-volume
    image: nginx
    env:
      - name: confidential
        valueFrom:
          secretKeyRef:
            name: q15secret 
            key: password
```
#### with (non-persistent)volume
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: question16
  namespace: default
spec:
  containers:
  - name: question16
    Image: nginx
    volumeMounts:
    - mountPath: /path
      name: q16vol
  volumes:
  - name: q16vol
    emptyDir: {}
```

### Deployment
#### with ingress access
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-demo
spec:
  rules:
  - host: ingress.demo.com
    http:
      paths:
      - backend:
          serviceName: ghost
          servicePort: 80
```
### PersistentVolume
```yaml
kind: PersistentVolume
apiVersion: v1
metadata:
  name: pv-volume
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /path
```
### DaemonSet
```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: special
spec:
  template:
    metadata:
      labels:
        name: special
    spec:
      containers:
      - name: special
        image: nginx
```
##### From version 1.8.0, the apiVersion of DaemonSet becomes apps/v1beta2, and a pod selector ( .spec.selector ) must be specified, which matches the labels of the .spec.template. 
```yaml
apiVersion: apps/v1beta2
kind: DaemonSet
metadata:
  name: special
spec:
  selector:
    matchLabels:
      name: special
  template:
    metadata:
      labels:
        name: special
    spec:
      containers:
      - name: special
        image: nginx
```
### Job
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: myjob
spec:
  completions: 4
  parallelism: 6
  template:
    metadata:
      name: myjob
    spec:
      containers:
      - name: myjob
        image: perl
        command: ["perl", "-wle", "print \'Beijing\'"]
      restartPolicy: Never
  backoffLimit: 4
```
