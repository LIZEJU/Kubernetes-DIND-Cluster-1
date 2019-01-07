## Kubernetes DIND Cluster
Kubernetes is an open-source system used for automating the deployment, scaling, and management of containerized applications.

I'll be demonstrating how to quickly set it up on Ubuntu 16.04.

#### Installing dependencies
The first piece to be install is apt-transport-https (a package that allows using https as well as http in apt repository sources). This can be installed with the following command:
```bash
sudo apt-get update && apt-get install -y apt-transport-https
```

Our next dependency is Docker. Our Kubernetes installation will depend upon this, so install it with:
```bash
sudo apt install docker.io
```

Once that completes, start and enable the Docker service with the commands
```bash
sudo systemctl start docker
sudo systemctl enable docker
```

#### Installing Kubernetes
First, download and add the key for the Kubernetes install with the following command:
```bash
sudo curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add
```

Next add a repository:
```bash
sudo echo 'deb http://apt.kubernetes.io/ kubernetes-xenial main' > /etc/apt/sources.list.d/kubernetes.list
```

Now run:
```bash
apt-get update
apt-get install -y kubelet kubeadm kubectl kubernetes-cni
```

Disable swap:
```bash
swapoff -a
```

Clone the git repo:
```bash
git clone https://github.com/Mirantis/kubeadm-dind-cluster.git
cd kubeadm-dind-cluster/fixed
```

Start up a Kubernetescluster requesting 3 worker nodes in the cluster (default is 2):
```bash
NUM_NODES=3 ./dind-cluster-v1.13.sh up
```

It will take a few minutes and produce lot of output:
```bash
root@dev-virtual-machine:~/kubeadm-dind-cluster/fixed# NUM_NODES=3 ./dind-cluster-v1.13.sh up
* Making sure DIND image is up to date
Status: Downloaded newer image for mirantis/kubeadm-dind-cluster
/root/.kubeadm-dind-cluster/kubectl-v1.13.0: OK
* Starting DIND container: kube-master
* Running kubeadm: init --config /etc/kubeadm.conf --ignore-preflight-errors=all
Initializing machine ID from random generator.
Created symlink /etc/systemd/system/multi-user.target.wants/docker.service → /lib/systemd/system/docker.service.
Loaded image: mirantis/hypokube:base
...
real    0m9.633s
user    0m0.676s
sys     0m0.576s
Sending build context to Docker daemon
...
Successfully built 2464b557339b
Successfully tagged mirantis/hypokube:final
Created symlink /etc/systemd/system/multi-user.target.wants/kubelet.service → /lib/systemd/system/kubelet.service.
[init] Using Kubernetes version: v1.13.0
[preflight] Running pre-flight checks
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Activating the kubelet service
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [kube-master kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [kube-master localhost] and IPs
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [kube-master localhost] and IPs
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[controlplane] Adding extra host path mount "hyperkube" to "kube-apiserver"
[controlplane] Adding extra host path mount "hyperkube" to "kube-controller-manager"
[controlplane] Adding extra host path mount "hyperkube" to "kube-scheduler"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
[controlplane] Adding extra host path mount "hyperkube" to "kube-apiserver"
[controlplane] Adding extra host path mount "hyperkube" to "kube-controller-manager"
[controlplane] Adding extra host path mount "hyperkube" to "kube-scheduler"
[control-plane] Creating static Pod manifest for "kube-scheduler"
[controlplane] Adding extra host path mount "hyperkube" to "kube-apiserver"
[controlplane] Adding extra host path mount "hyperkube" to "kube-controller-manager"
[controlplane] Adding extra host path mount "hyperkube" to "kube-scheduler"
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[apiclient] All control plane components are healthy after 21.014846 seconds
[uploadconfig] storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config-1.13" in namespace kube-system with the configuration for the kubelets in the cluster
[patchnode] Uploading the CRI Socket information "/var/run/dockershim.sock" to the Node API object "kube-master" as an annotation
[mark-control-plane] Marking the node kube-master as control-plane by adding the label "node-role.kubernetes.io/master=''"
[mark-control-plane] Marking the node kube-master as control-plane by adding the taints [node-role.kubernetes.io/master:NoSchedule]
[bootstrap-token] Using token: kf9nb4.3fjpbtvbb9oxvpwq
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstraptoken] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstraptoken] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstraptoken] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstraptoken] creating the "cluster-info" ConfigMap in the "kube-public" namespace
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes master has initialized successfully!
```

Notice:
```bash
To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of machines by running the following on each node
as root:

  kubeadm join xx.xxx.x.xx:xxxx --token xxxxxxx... --discovery-token-ca-cert-hash sha256:xxxxxx...

* Setting cluster config
Cluster "dind" set.
Context "dind" created.
Switched to context "dind".
* Starting node container: 1
* Starting DIND container: kube-node-1
* Node container started: 1
* Starting node container: 2
* Starting DIND container: kube-node-2
* Node container started: 2
* Starting node container: 3
* Starting DIND container: kube-node-3
* Node container started: 3
* Joining node: 2
* Joining node: 3
...
* Deploying k8s dashboard
deployment.extensions/kubernetes-dashboard created
service/kubernetes-dashboard created
clusterrolebinding.rbac.authorization.k8s.io/add-on-cluster-admin created
* Cluster Info
Network Mode: ipv4
Cluster context: dind
Cluster ID: 0
Management CIDR(s): 10.192.0.0/24
Service CIDR/mode: 10.96.0.0/12/ipv4
Pod CIDR(s): 10.244.0.0/16
* Taking snapshot of the cluster
deployment.extensions/coredns scaled
deployment.extensions/kubernetes-dashboard scaled
pod "kube-proxy-c65pk" deleted
pod "kube-proxy-j9wh8" deleted
pod "kube-proxy-n894s" deleted
pod "kube-proxy-rlcwj" deleted
NAME                         READY   STATUS              RESTARTS   AGE
etcd-kube-master             1/1     Running             0          18s
kube-apiserver-kube-master   1/1     Running             0          20s
kube-proxy-5zsph             1/1     Running             0          12s
kube-proxy-7hbs9             1/1     Running             0          11s
kube-proxy-9jbns             1/1     Running             0          11s
kube-proxy-vbmjs             0/1     ContainerCreating   0          1s
kube-scheduler-kube-master   1/1     Running             0          4s
tar: var/lib/kubelet/device-plugins/kubelet.sock: socket ignored
tar: var/lib/kubelet/device-plugins/kubelet.sock: socket ignored
tar: var/lib/kubelet/device-plugins/kubelet.sock: socket ignored
tar: var/lib/kubelet/device-plugins/kubelet.sock: socket ignored
* Waiting for kube-proxy and the nodes
....[done]
* Bringing up coredns and kubernetes-dashboard
deployment.extensions/coredns scaled
deployment.extensions/kubernetes-dashboard scaled
............[done]
NAME          STATUS   ROLES    AGE     VERSION
kube-master   Ready    master   2m30s   v1.13.0
kube-node-1   Ready    <none>   101s    v1.13.0
kube-node-2   Ready    <none>   98s     v1.13.0
kube-node-3   Ready    <none>   98s     v1.13.0
* Access dashboard at: http://127.0.0.1:32768/api/v1/namespaces/kube-system/services/kubernetes-dashboard:/proxy
```

Verify that you can see the master and worker nodes as ordinary containers:
```bash
docker ps

CONTAINER ID        IMAGE                                                                          COMMAND                  CREATED             STATUS              PORTS                       NAMES
83a8ae1a6914        mirantis/kubeadm-dind-cluster:596f7d093470c1dc3a3e4466bcdfb34438a99b90-v1.13   "/sbin/dind_init sys…"   14 minutes ago      Up 14 minutes       8080/tcp                    kube-node-3
89bde78a1034        mirantis/kubeadm-dind-cluster:596f7d093470c1dc3a3e4466bcdfb34438a99b90-v1.13   "/sbin/dind_init sys…"   14 minutes ago      Up 14 minutes       8080/tcp                    kube-node-2
abaea1f4b94c        mirantis/kubeadm-dind-cluster:596f7d093470c1dc3a3e4466bcdfb34438a99b90-v1.13   "/sbin/dind_init sys…"   14 minutes ago      Up 14 minutes       8080/tcp                    kube-node-1
a413f86dccd7        mirantis/kubeadm-dind-cluster:596f7d093470c1dc3a3e4466bcdfb34438a99b90-v1.13   "/sbin/dind_init sys…"   16 minutes ago      Up 16 minutes       127.0.0.1:32768->8080/tcp   kube-master
```

```bash
 kubectl get nodes
NAME          STATUS   ROLES    AGE   VERSION
kube-master   Ready    master   15m   v1.13.0
kube-node-1   Ready    <none>   15m   v1.13.0
kube-node-2   Ready    <none>   15m   v1.13.0
kube-node-3   Ready    <none>   15m   v1.13.0
```

Now in order to access the kubernetes dashboard, run:
```bash
kubectl proxy --address=0.0.0.0 --accept-hosts='^\[::1\]$' --disable-filter=true
Starting to serve on [::]:8001
```
and you will be able to access your dashboard from any broweser on the same network at the following URL:
```http request
http://<YOUR_VM_IP>:8001/api/v1/namespaces/kube-system/services/http:kubernetes-dashboard:/proxy/#!/workload?namespace=default
```
