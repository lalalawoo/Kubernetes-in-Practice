https://coreos.com/etcd/docs/latest/op-guide/recovery.html

###### (HINT: use “etcdctl snapshot save <file>” - but need to set etcd environment variables
```sh
export ETCDCTL_CA_FILE='/srv/etcd/etcd-ca.crt'
export ETCDCTL_CERT_FILE='/srv/etcd/etcd-client.crt'
export ETCDCTL_KEY_FILE='/srv/etcd/etcd-client.key'
export ETCDCTL_ENDPOINTS='https://127.0.0.1:2379'
export ETCDCTL_API=3”
```
