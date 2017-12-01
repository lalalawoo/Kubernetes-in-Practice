https://kubernetes.io/docs/getting-started-guides/scratch/

### Start a Kubernetes head node with etcd , the API server, and the controller manager
#### install etcd
```sh
$ docker run -d --name=k8s -p 8080:8080
gcr.io/google_containers/etcd:3.1.10 etcd --data-dir /var/lib/data
```
#### install apiServer
```sh
$ docker run -d --net=container:k8s
gcr.io/google_containers/hyperkube:v1.7.6 /apiserver
--etcd-servers=http://127.0.0.1:2379 \
--service-cluster-ip-range=10.0.0.1/24 \
--insecure-bind-address=0.0.0.0 \
--insecure-port=8080 \
--admission-control=AlwaysAdmit
```
#### install controller-manager
```sh
$ docker run -d --net=container:k8s
gcr.io/google_containers/hyperkube:v1.7.6 /controller-manager
--master=127.0.0.1:8080
```
##### Note: etcd , the API server, and the controller manager share the same network 
namespace, they can reach each other on 127.0.0.1, even though they are running in different
containers.
#### test
```sh
$ docker exec -ti k8s etcdctl /bin/sh
$ export ETCDCTL_API=3
$ etcdctl get “/registry/api” --prefix=true
$ curl http://127.0.0.1:8080/api/v1
```

### Install kubeadm
https://kubernetes.io/docs/setup/independent/install-kubeadm/
*Do not forget to install Docker*
#### install kubectl
```sh
$ apt-get install -y docker.io kubectl
```
#### bootstrap the cluster
```sh
$kubeadm init
$kubeadm join
```

```sh
$systemctl status kubelet
```
