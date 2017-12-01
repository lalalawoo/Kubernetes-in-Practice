### Pod
#### using secret as environment variable
..java
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
#### using secret in volumn
..java
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
#### with (non-persistent)volume
..java
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

### Deployment
#### with ingress access
..java
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
