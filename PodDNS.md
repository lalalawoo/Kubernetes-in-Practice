#### how Pod and service communicate with each other
```sh
kubectl run nginx-deployment --image=nginx --port=80 
kubectl expose deployment nginx-deployment --port=80 --name=nginx-random --target-port=80

kubectl run busy --image=busybox -- sleep 3600

kubectl get pod  to find the busybox pod name
kubectl exec -it busy-b888bd849-gjnv6 nslookup nginx-random
Server:    10.0.0.10
Address 1: 10.0.0.10 kube-dns.kube-system.svc.cluster.local
Name:      nginx-random
Address 1: 10.0.0.138 nginx-random.default.svc.cluster.local

Pod DNS. Using the pod ip I got the output:
kubectl exec -it busy3-b888bd849-gjnv6 nslookup 172-17-0-28.default.cluster.pod.local
Server:    10.0.0.10
Address 1: 10.0.0.10 kube-dns.kube-system.svc.cluster.local

Name:      172.17.0.28
Address 1: 172.17.0.28 busy3-b888bd849-gjnv6
```
