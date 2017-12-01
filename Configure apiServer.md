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

#### kube-apiserver reference
https://kubernetes.io/docs/reference/generated/kube-apiserver/
```sh
      --bind-address ip                                         The IP address on which to listen for the --secure-port port. The associated interface(s) must be reachable by the rest of the cluster, and by CLI/web clients. If blank, all interfaces will be used (0.0.0.0). (default 0.0.0.0)
      --cert-dir string                                         The directory where the TLS certs are located. If --tls-cert-file and --tls-private-key-file are provided, this flag will be ignored. (default "/var/run/kubernetes")
      --client-ca-file string                                   If set, any request presenting a client certificate signed by one of the authorities in the client-ca-file is authenticated with an identity corresponding to the CommonName of the client certificate.
      --insecure-bind-address ip                                The IP address on which to serve the --insecure-port (set to 0.0.0.0 for all interfaces). (default 127.0.0.1)
      --insecure-port int                                       The port on which to serve unsecured, unauthenticated access. It is assumed that firewall rules are set up such that this port is not reachable from outside of the cluster and that port 443 on the cluster's public address is proxied to this port. This is performed by nginx in the default setup. (default 8080)
      --secure-port int                                         The port on which to serve HTTPS with authentication and authorization. If 0, don't serve HTTPS at all. (default 6443)
      --tls-ca-file string                                      If set, this certificate authority will used for secure access from Admission Controllers. This must be a valid PEM-encoded CA bundle. Alternatively, the certificate authority can be appended to the certificate provided by --tls-cert-file.
      --tls-cert-file string                                    File containing the default x509 Certificate for HTTPS. (CA cert, if any, concatenated after server cert). If HTTPS serving is enabled, and --tls-cert-file and --tls-private-key-file are not provided, a self-signed certificate and key are generated for the public address and saved to /var/run/kubernetes.
      --tls-private-key-file string                             File containing the default x509 private key matching --tls-cert-file.
      --tls-sni-cert-key namedCertKey                           A pair of x509 certificate and private key file paths, optionally suffixed with a list of domain patterns which are fully qualified domain names, possibly with prefixed wildcard segments. If no domain patterns are provided, the names of the certificate are extracted. Non-wildcard matches trump over wildcard matches, explicit domain patterns trump over extracted names. For multiple key/certificate pairs, use the --tls-sni-cert-key multiple times. Examples: "example.crt,example.key" or "foo.crt,foo.key:*.foo.com,foo.com". (default [])
