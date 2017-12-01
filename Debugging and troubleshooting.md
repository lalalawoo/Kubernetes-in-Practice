https://kubernetes.io/docs/getting-started-guides/coreos/bare_metal_offline/#helping-commands-for-debugging

##### List all keys in etcd:
```sh
etcdctl ls --recursive
```
##### Check system status of services on master:
```sh
systemctl status kube-apiserver
systemctl status kube-controller-manager
systemctl status kube-scheduler
systemctl status kube-register
```
##### Check system status of services on a node:
```sh
systemctl status kube-kubelet
systemctl status docker.service
```
##### List Kubernetes
```sh
kubectl get pods
kubectl get nodes
```
##### Kill all pods:
```sh
for i in `kubectl get pods | awk '{print $1}'`; do kubectl delete pod $i; done
```
##### if kubelet not working, try:
```sh
systemctl start kubelet.service
```
##### check cluster working status
```sh
kubectl get componentstatuses
```
##### then SSH to master and enable / restart the service that is not running. 

https://kubernetes.io/docs/tasks/debug-application-cluster/debug-cluster/
