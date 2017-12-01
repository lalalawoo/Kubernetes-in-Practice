https://kubernetes.io/docs/tasks/administer-cluster/static-pod/

##### 1. Edit the kubelet systemd configuration and pass in the --pod-manifests-path option:
```sh
vi /etc/systemd/system/kubelet.service
		File: 
Description=Kubernetes Kubelet
Documentation=https://github.com/GoogleCloudPlatform/kubernetes

[Service]
ExecStart=/usr/local/bin/kubelet \
  --allow-privileged=true \
  --anonymous-auth=false \
  --authorization-mode=Webhook \
  --client-ca-file=/var/lib/kubernetes/ca.pem \
  --cluster-dns=10.20.0.8 \
  --cluster-domain=cluster.local \
  --image-pull-progress-deadline=2m \
  --kubeconfig=/var/lib/kubernetes/jim-sandbox-vm-1.kubeconfig \
  --network-plugin=cni \
  --pod-cidr=192.168.1.0/24 \
  --register-node=true \
  --require-kubeconfig \
  --runtime-request-timeout=15m \
  --tls-cert-file=/var/lib/kubernetes/jim-sandbox-vm-1.pem \
  --tls-private-key-file=/var/lib/kubernetes/jim-sandbox-vm-1-key.pem \
  --pod-manifest-path=<question provided path> \                 <add this including back slash!>
  --cgroups-per-qos=false \
  --enforce-node-allocatable="" \
  --v=3
Restart=on-failure
RestartSec=5
```
##### 2. Add the YAML
Then add the Pod YAML in the manifests folder “/etc/kubernetes/manifests

##### 3. Restart the service
```sh
sudo systemctl daemon-reload
sudo systemctl restart kubelet
```
##### 4. Check is the Pod is up
```sh
systemctl status kubelet
```
Check local logs for the POD name (e.g. nginx):
   
        grep "nginx" /var/log/syslog
