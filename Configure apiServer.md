https://kubernetes.io/docs/tasks/tls/managing-tls-in-a-cluster/

#### 1. Copy all certs to a directory like “/var/lib/kubernetes”
#### 2. Edit “/etc/systemd/system/kube-apiserver.service”
#### 3. Check if server certificates are configured:
```sh
  --tls-ca-file=/var/lib/kubernetes/ca.pem \
  --tls-cert-file=/var/lib/kubernetes/master.pem \
  --tls-private-key-file=/var/lib/kubernetes/master-key.pem \
```
#### 4. Check if the client certificates are configured and kubelet uses HTTPS:
```sh
  --kubelet-certificate-authority=/var/lib/kubernetes/ca.pem \
  --kubelet-client-certificate=/var/lib/kubernetes/kubelet.pem \
  --kubelet-client-key=/var/lib/kubernetes/kubelet-key.pem \
  --kubelet-https=true \
```
#### 5. Check if a HTTPS port is configured. If not it defaults to 6443.
#### 6. Check which addresses are allowed:
```sh
  --bind-address=0.0.0.0 \
  --insecure-bind-address=127.0.0.1 \
```
#### Once all of the above is setup correctly, save and restart:
```sh
root@jim-sandbox-vm-2:~# sudo systemctl daemon-reload
root@jim-sandbox-vm-2:~# sudo systemctl restart kube-apiserver
root@jim-sandbox-vm-2:~# sudo systemctl status kube-apiserver
  kube-apiserver.service - Kubernetes API Server
   Loaded: loaded (/etc/systemd/system/kube-apiserver.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2017-11-23 19:18:24 UTC; 5s ago
     Docs: https://github.com/GoogleCloudPlatform/kubernetes
 Main PID: 89722 (kube-apiserver)
    Tasks: 6
   Memory: 95.0M
      CPU: 951ms
   CGroup: /system.slice/kube-apiserver.service
           └─89722 /usr/local/bin/kube-apiserver --admission-control=NamespaceLifecycle,LimitRanger,Serv
...
```
#### To test whether the configuration worked use curl as follows:
```sh
nirmata@jim-sandbox-vm-2:~$ curl --cacert /etc/etcd/ca.pem https://10.20.0.8:6443/version
{
  "major": "1",
  "minor": "6",
  "gitVersion": "v1.6.2",
  "gitCommit": "477efc3cbe6a7effca06bd1452fa356e2201e1ee",
  "gitTreeState": "clean",
  "buildDate": "2017-04-19T20:22:08Z",
  "goVersion": "go1.7.5",
  "compiler": "gc",
  "platform": "linux/amd64"
}
```
#### *Alternate solution*

#### Copy all certs to a directory like “/etc/kubernetes/ssl”

#### Run the following script from the tar distro:
```sh
sudo ./kubernetes/cluster/centos/master/scripts/apiserver.sh
```
#### If it gives errors, modify the paths or create the dirs
#### You can also provide following arguments to the script
```sh
MASTER_ADDRESS=${1:-"8.8.8.18"}
ETCD_SERVERS=${2:-"http://8.8.8.18:2379"}
SERVICE_CLUSTER_IP_RANGE=${3:-"10.10.10.0/24"}
ADMISSION_CONTROL=${4:-""}
```
#### This script generates:
```sh
/etc/kubernetes/kube-apiserver    //Environment vars
/etc/systemd/system/kube-apiserver.service  //Systemd unit
```
#### Update the Environment vars and start apiserver using systemctl
#### For etcd v2, make sure to add --storage-backend=etcd2 in the environment variable file
#### Increase log level for debugging --v=6
#### Update path to your certificates
