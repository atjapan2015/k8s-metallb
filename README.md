
If you’re using kube-proxy in IPVS mode, since Kubernetes v1.14.2 you have to enable strict ARP mode.

You can achieve this by editing kube-proxy config in current cluster:

```
kubectl edit configmap -n kube-system kube-proxy
```

and set:

```
apiVersion: kubeproxy.config.k8s.io/v1alpha1
kind: KubeProxyConfiguration
mode: "ipvs"
ipvs:
  strictARP: true
```

To install MetalLB, apply the manifest:

```
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.3/manifests/namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.3/manifests/metallb.yaml
# On first install only
kubectl create secret generic -n metallb-system memberlist --from-literal=secretkey="$(openssl rand -base64 128)"
```

```
kubectl apply -f metallb-config.yaml
```

高级功能:多个的Service共用一个IP
在创建Service的时候加上一个annotation： metallb.universe.tf/allow-shared-ip: <some_key>，那么使用相同key的service会共用同一个IP。

当然共享IP的前提是这些Service都使用不同的Port。

