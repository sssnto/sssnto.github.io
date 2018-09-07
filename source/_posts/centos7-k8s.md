---
title: 使用kubeadm在centos7上部署Kubernetes集群以及 监控视图
date: 2018-4-2 10:49:38
author: sssnto
tags: [centos7,k8s,docker]
category: [云平台]
comments: true    // 是否开启评论
img:               // 自定义缩略图
---
 
#### 系统要求
* CentOS 7
* 2G以上内存
* 2个以上CPU
* 群集中所有机器（公共或专用网络都可以）之间的完全网络连接正常
* 每个节点的主机名，mac地址，product_uuid唯一
* 确保机器上的端口都是开着的
* 关闭交换分区 swapoff -a

##### 需要开通的端口

Master node(s)

Protocol | Direction | Port Range | Purpose
- | :-: | :-: | :-: |
TCP	| Inbound | 6443* | Kubernetes API server
TCP	| Inbound |	2379-2380 |	etcd server client API
TCP	| Inbound |	10250 |	Kubelet API
TCP	| Inbound |	10251 |	kube-scheduler
TCP	| Inbound |	10252 |	kube-controller-manager
TCP	| Inbound |	10255 |	Read-only Kubelet API


Worker node(s)

Protocol | Direction | Port Range | Purpose
- | :-: | :-: | :-: |
TCP | Inbound |	10250 | Kubelet API
TCP | Inbound |	10255 | Read-only Kubelet API
TCP | Inbound |	30000-32767 | NodePort Services**

#### 安装docker
> 所有机器都需要安装，请用root用户执行一下命名

```
yum install -y docker
systemctl enable docker && systemctl start docker
```
#### 安装kubeadm，kubelet和kubectl
> 您将在您的所有机器上安装这些软件包
* kubeadm：引导群集的命令。
* kubelet：在群集中的所有机器上运行的组件，并执行诸如启动荚和容器之类的组件。
* kubectl：命令行util与您的群集通话。

```
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF

setenforce 0

yum install -y kubelet kubeadm kubectl

systemctl enable kubelet && systemctl start kubelet
```

> 注意：
* 例如，setenforce 0需要通过运行来禁用SELinux 以允许容器访问主机文件系统，这是pod网络所需的。您必须这样做，直到kubelet中的SELinux支持得到改进。
* RHEL / CentOS 7上的某些用户报告了由于iptables被绕过而导致流量被错误路由的问题。您应该确保net.bridge.bridge-nf-call-iptables在您的sysctl配置中设置为1 ，例如
```
cat <<EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sysctl --system
```


#### 在主节点上配置kubelet使用的cgroup驱动程序
确保kubelet使用的cgroup驱动程序与Docker使用的驱动程序相同。验证您的Docker cgroup驱动程序是否与kubelet config相匹配：

```jshelllanguage
docker info | grep -i cgroup
cat /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
```
如果Docker cgroup驱动程序和kubelet配置不匹配，请更改kubelet配置以匹配Docker cgroup驱动程序。你需要改变的标志是--cgroup-driver。如果它已经设置，你可以像这样更新：

```jshelllanguage
sed -i "s/cgroup-driver=systemd/cgroup-driver=cgroupfs/g" /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
```


否则，您将需要打开systemd文件并将该标志添加到现有环境行。

然后重新启动kubelet：

```jshelllanguage
systemctl daemon-reload
systemctl restart kubelet
```


#### 使用kubeadmin创建kubernetes集群

初始化master节点，pod网络采用Fannle
```jshelllanguage
kubeadm init  --pod-network-cidr=10.244.0.0/16
```

```
[root@instance-1 ~]# kubeadm init --pod-network-cidr=10.244.0.0/16
[init] Using Kubernetes version: v1.10.0
[init] Using Authorization modes: [Node RBAC]
[preflight] Running pre-flight checks.
        [WARNING FileExisting-crictl]: crictl not found in system path
Suggestion: go get github.com/kubernetes-incubator/cri-tools/cmd/crictl
[certificates] Generated ca certificate and key.
[certificates] Generated apiserver certificate and key.
[certificates] apiserver serving cert is signed for DNS names [instance-1 kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 10.140.0.2]
[certificates] Generated apiserver-kubelet-client certificate and key.
[certificates] Generated etcd/ca certificate and key.
[certificates] Generated etcd/server certificate and key.
[certificates] etcd/server serving cert is signed for DNS names [localhost] and IPs [127.0.0.1]
[certificates] Generated etcd/peer certificate and key.
[certificates] etcd/peer serving cert is signed for DNS names [instance-1] and IPs [10.140.0.2]
[certificates] Generated etcd/healthcheck-client certificate and key.
[certificates] Generated apiserver-etcd-client certificate and key.
[certificates] Generated sa key and public key.
[certificates] Generated front-proxy-ca certificate and key.
[certificates] Generated front-proxy-client certificate and key.
[certificates] Valid certificates and keys now exist in "/etc/kubernetes/pki"
[kubeconfig] Wrote KubeConfig file to disk: "/etc/kubernetes/admin.conf"
[kubeconfig] Wrote KubeConfig file to disk: "/etc/kubernetes/kubelet.conf"
[kubeconfig] Wrote KubeConfig file to disk: "/etc/kubernetes/controller-manager.conf"
[kubeconfig] Wrote KubeConfig file to disk: "/etc/kubernetes/scheduler.conf"
[controlplane] Wrote Static Pod manifest for component kube-apiserver to "/etc/kubernetes/manifests/kube-apiserver.yaml"
[controlplane] Wrote Static Pod manifest for component kube-controller-manager to "/etc/kubernetes/manifests/kube-controller-manager.yaml"
[controlplane] Wrote Static Pod manifest for component kube-scheduler to "/etc/kubernetes/manifests/kube-scheduler.yaml"
[etcd] Wrote Static Pod manifest for a local etcd instance to "/etc/kubernetes/manifests/etcd.yaml"
[init] Waiting for the kubelet to boot up the control plane as Static Pods from directory "/etc/kubernetes/manifests".
[init] This might take a minute or longer if the control plane images have to be pulled.
[apiclient] All control plane components are healthy after 39.501758 seconds
[uploadconfig] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[markmaster] Will mark node instance-1 as master by adding a label and a taint
[markmaster] Master instance-1 tainted and labelled with key/value: node-role.kubernetes.io/master=""
[bootstraptoken] Using token: xmrkib.6kgpwyb3jdk61o22
[bootstraptoken] Configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstraptoken] Configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstraptoken] Configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstraptoken] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
[addons] Applied essential addon: kube-dns
[addons] Applied essential addon: kube-proxy

Your Kubernetes master has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of machines by running the following on each node
as root:

  kubeadm join --token <token> <master-ip>:<master-port> --discovery-token-ca-cert-hash sha256:<hash>
```

记录输出的kubeadm join命令kubeadm init。你一会儿需要这个。

如果为非root用户执行
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

如果为root用户
```
export KUBECONFIG=/etc/kubernetes/admin.conf
```
将 admin.conf 分发到各个机器上，免密登录清参考centos-ambari那篇文章

```
scp /etc/kubernetes/admin.conf instance-2:/etc/kubernetes/admin.conf
```

将KUBECONFIG写入到全局环境变量
```

vim /etc/profile

HOSTNAME=`/usr/bin/hostname 2>/dev/null`
HISTSIZE=1000
if [ "$HISTCONTROL" = "ignorespace" ] ; then
    export HISTCONTROL=ignoreboth
else
    export HISTCONTROL=ignoredups
fi

export PATH USER LOGNAME MAIL HOSTNAME HISTSIZE HISTCONTROL

# By default, we want umask to get set. This sets it for login shell
# Current threshold for system reserved uid/gids is 200
# You could check uidgid reservation validity in
# /usr/share/doc/setup-*/uidgid file
if [ $UID -gt 199 ] && [ "`/usr/bin/id -gn`" = "`/usr/bin/id -un`" ]; then
    umask 002
else
    umask 022
fi

for i in /etc/profile.d/*.sh ; do
    if [ -r "$i" ]; then
        if [ "${-#*i}" != "$-" ]; then
            . "$i"
        else
            . "$i" >/dev/null
        fi
    fi
done

unset i
unset -f pathmunge

export KUBECONFIG=/etc/kubernetes/admin.conf

source /etc/profile

```

##### 安装pod 网络

```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/v0.9.1/Documentation/kube-flannel.yml

```
主隔离
```
kubectl taint nodes --all node-role.kubernetes.io/master-

```

#### 加入其它节点
在其它机器上执行kubeadm join命令。
在kubeadm 命令结束后记下的那串命令 分别在其它机器上执行
```
 kubeadm join 10.140.0.2:6443 --token xmrkib.6kgpwyb3jdk61o22 --discovery-token-ca-cert-hash sha256:e6d6017868185ec3afea5317b35cd19200f7b80f08cb3283be33d3c0da2f0260
```
执行成功后显示
```
[root@instance-2 kubernetes]# kubeadm join 10.140.0.2:6443 --token xmrkib.6kgpwyb3jdk61o22 --discovery-token-ca-cert-hash sha256:e6d6017868185ec3afea5317b35cd19200f7b80f08cb3283be33d3c0da2f0260
[preflight] Running pre-flight checks.
        [WARNING FileExisting-crictl]: crictl not found in system path
Suggestion: go get github.com/kubernetes-incubator/cri-tools/cmd/crictl
[discovery] Trying to connect to API Server "10.140.0.2:6443"
[discovery] Created cluster-info discovery client, requesting info from "https://10.140.0.2:6443"
[discovery] Requesting info from "https://10.140.0.2:6443" again to validate TLS against the pinned public key
[discovery] Cluster info signature and contents are valid and TLS certificate validates against pinned roots, will use API Server "10.140.0.2:6443"
[discovery] Successfully established connection with API Server "10.140.0.2:6443"

This node has joined the cluster:
* Certificate signing request was sent to master and a response
  was received.
* The Kubelet was informed of the new secure connection details.

Run 'kubectl get nodes' on the master to see this node join the cluster.
```


#### 安装kubernetes-dashboard

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml
```

使用代理
--address= 替换你自己的域名
```
kubectl proxy --address='instance-1.c.sssnto-shadowsoct.internal' --port=8080 --accept-hosts='^*$'
```
通过下面url登录监控面板
```
http://instance-1.c.sssnto-shadowsoct.internal:8080/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
```


创建用户，并绑定admin角色

```
vim admin-role.yaml

apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kube-system
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kube-system

```

```
kubectl create -f admin-role.yaml
```

获取token执行以下命令
```
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')

```


####控制台免登录设置
```
[root@instance-1 .ssh]#  kubectl -n kube-system get secret
NAME                                             TYPE                                  DATA      AGE

deployment-controller-token-fmljr                kubernetes.io/service-account-token   3         4h

[root@instance-1 .ssh]#  kubectl -n kube-system describe secret deployment-controller-token-fmljr
Name:         deployment-controller-token-fmljr
Namespace:    kube-system
Labels:       <none>
Annotations:  kubernetes.io/service-account.name=deployment-controller
              kubernetes.io/service-account.uid=d46cf650-36ec-11e8-b10c-42010a8c0002

Type:  kubernetes.io/service-account-token

Data
====
namespace:  11 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJkZXBsb3ltZW50LWNvbnRyb2xsZXItdG9rZW4tZm1sanIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGVwbG95bWVudC1jb250cm9sbGVyIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiZDQ2Y2Y2NTAtMzZlYy0xMWU4LWIxMGMtNDIwMTBhOGMwMDAyIiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50Omt1YmUtc3lzdGVtOmRlcGxveW1lbnQtY29udHJvbGxlciJ9.zynU8FEB9veLHkiJuRhVWqnQVYgMqgZyXwpzDUYVgiNp4caaFJ1hL4urLOULI1Ydmz0p8PYQ4aSq4P4jj25JBMenxBGrJTkgiTe-e4rfQoJ--bvRnFIsLaFrT2jPyGO9uIa24IxU2F5NzlC9G2cGU4lRZvMuWtKVlQbOcvf4NI2eJuGQdToxOZM1u2xQ_oiGTTI-RWzj_6ZyBDF9Ve7_Vh1Mz_eCo7XqmB3s6o2S-XW9lRQ_qn27CGqn8v4tjV8Zquf6Dn3QVE5DeGSEdX8ICtoAYC7fX74XmbV9qFRlhEERHLth7JaC67I-FAk708E6ubCKGE-B8AaVzCrdflk-3g
ca.crt:     1025 bytes
```

设置cluster-admin用户设置token

```
kubectl config set-credentials cluster-admin --token=eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJkZXBsb3ltZW50LWNvbnRyb2xsZXItdG9rZW4tZm1sanIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGVwbG95bWVudC1jb250cm9sbGVyIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiZDQ2Y2Y2NTAtMzZlYy0xMWU4LWIxMGMtNDIwMTBhOGMwMDAyIiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50Omt1YmUtc3lzdGVtOmRlcGxveW1lbnQtY29udHJvbGxlciJ9.zynU8FEB9veLHkiJuRhVWqnQVYgMqgZyXwpzDUYVgiNp4caaFJ1hL4urLOULI1Ydmz0p8PYQ4aSq4P4jj25JBMenxBGrJTkgiTe-e4rfQoJ--bvRnFIsLaFrT2jPyGO9uIa24IxU2F5NzlC9G2cGU4lRZvMuWtKVlQbOcvf4NI2eJuGQdToxOZM1u2xQ_oiGTTI-RWzj_6ZyBDF9Ve7_Vh1Mz_eCo7XqmB3s6o2S-XW9lRQ_qn27CGqn8v4tjV8Zquf6Dn3QVE5DeGSEdX8ICtoAYC7fX74XmbV9qFRlhEERHLth7JaC67I-FAk708E6ubCKGE-B8AaVzCrdflk-3g
```

将管理员权限授予仪表板的服务帐户

```
cat <<EOF | kubectl create -f -
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: kubernetes-dashboard
  labels:
    k8s-app: kubernetes-dashboard
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: kubernetes-dashboard
  namespace: kube-system
EOF
```

#### 常用命令

查看所有pods
```
kubectl get pods --all-namespaces
```
