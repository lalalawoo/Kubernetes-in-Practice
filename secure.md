(deny all traffic by default, allow specific traffic)

```sh
kubectl edit ns secure 
"net.beta.kubernetes.io/network-policy={\"ingress\": {\"isolation\": \"DefaultDeny\"}}" 
```

May have to add labels to the pods to make it work.
```sh
apiVersion: extensions/v1beta1
kind: NetworkPolicy
metadata:
  name: test-network-policy
  namespace: secure
spec:
  podSelector:
    matchLabels:
      <role: to-pod> (replace withthe to-pod label)
  ingress:
  - from:
    - podSelector:
        matchLabels:
          <role: from-pod> (replace with the from pod label)
    ports:
    - protocol: tcp
      port: 6379 (replace with the to-pod port)
```
