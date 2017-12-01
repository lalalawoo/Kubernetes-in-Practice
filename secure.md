#### deny all traffic by default, allow specific traffic

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

#### ingress controller deployment
https://medium.com/@gokulc/setting-up-nginx-ingress-on-kubernetes-2b733d8d2f45
may have to change /etc/hosts

Use the following commands to generate a self signed certificate:
```sh
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout nginx.key -out nginx.crt
sudo openssl dhparam -out dhparam.pem 2048
kubectl create secret tls tls-certificate --key nginx.key --cert nginx.crt
kubectl create secret generic tls-dhparam --from-file=dhparam.pem
```
