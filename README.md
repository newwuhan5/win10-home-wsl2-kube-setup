# How to setup Microk8s on Windows 10 home edition
With hours that I spent I have finally setup MicroK8S Kubernetes on Windows 10 Home edition.  If you would like to learn Kubernetes on the single node, this will be a good start point.  In this github project, I would list all the issues that I encountered and shared the solutions.


### Get ready for yor windows 10 home for WSL2
Using this link: https://docs.microsoft.com/en-us/windows/wsl/install-win10

```cmd
 wsl -l -v
  NAME      STATE           VERSION
* Ubuntu    Running         2
```

#### In ubuntu bash:

```bash
~$ uname -a
Linux emily15 4.19.128-microsoft-standard #1 SMP Tue Jun 23 12:58:10 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
```

Then I started to install microk8s, I got the error like following:

```bash
~$ sudo snap install microk8s --classic
```

[sudo] password for jacky:
error: cannot communicate with server: Post http://localhost/v2/snaps/microk8s: dial unix /run/snapd.socket: connect: no such file or directory

```bash
~$ sudo microk8s.status --wait-ready
```

[sudo] password for jacky:
microk8s is running
high-availability: no
  datastore master nodes: 127.0.0.1:19001
  datastore standby nodes: none
addons:
  enabled:
    ha-cluster           # Configure high availability on the current node
  disabled:
    ambassador           # Ambassador API Gateway and Ingress
    cilium               # SDN, fast with full network policy
    dashboard            # The Kubernetes dashboard
    dns                  # CoreDNS
    fluentd              # Elasticsearch-Fluentd-Kibana logging and monitoring
    gpu                  # Automatic enablement of Nvidia CUDA
    helm                 # Helm 2 - the package manager for Kubernetes
    helm3                # Helm 3 - Kubernetes package manager
    host-access          # Allow Pods connecting to Host services smoothly
    ingress              # Ingress controller for external access
    istio                # Core Istio service mesh services
    jaeger               # Kubernetes Jaeger operator with its simple config
    knative              # The Knative framework on Kubernetes.
    kubeflow             # Kubeflow for easy ML deployments
    linkerd              # Linkerd is a service mesh for Kubernetes and other frameworks
    metallb              # Loadbalancer for your Kubernetes cluster
    metrics-server       # K8s Metrics Server for API access to service metrics
    multus               # Multus CNI enables attaching multiple network interfaces to pods
    prometheus           # Prometheus operator for monitoring and logging
    rbac                 # Role-Based Access Control for authorisation
    registry             # Private image registry exposed on localhost:32000
    storage              # Storage class; allocates storage from host directory
~$ sudo microk8s.enable dashboard dns
Enabling Kubernetes Dashboard
Enabling Metrics-Server
clusterrole.rbac.authorization.k8s.io/system:aggregated-metrics-reader created
clusterrolebinding.rbac.authorization.k8s.io/metrics-server:system:auth-delegator created
rolebinding.rbac.authorization.k8s.io/metrics-server-auth-reader created
Warning: apiregistration.k8s.io/v1beta1 APIService is deprecated in v1.19+, unavailable in v1.22+; use apiregistration.k8s.io/v1 APIService
apiservice.apiregistration.k8s.io/v1beta1.metrics.k8s.io created
serviceaccount/metrics-server created
deployment.apps/metrics-server created
service/metrics-server created
clusterrole.rbac.authorization.k8s.io/system:metrics-server created
clusterrolebinding.rbac.authorization.k8s.io/system:metrics-server created
clusterrolebinding.rbac.authorization.k8s.io/microk8s-admin created
Metrics-Server is enabled
Applying manifest
serviceaccount/kubernetes-dashboard created
service/kubernetes-dashboard created
secret/kubernetes-dashboard-certs created
secret/kubernetes-dashboard-csrf created
secret/kubernetes-dashboard-key-holder created
configmap/kubernetes-dashboard-settings created
role.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrole.rbac.authorization.k8s.io/kubernetes-dashboard created
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
deployment.apps/kubernetes-dashboard created
service/dashboard-metrics-scraper created
deployment.apps/dashboard-metrics-scraper created

If RBAC is not enabled access the dashboard using the default token retrieved with:

token=$(microk8s kubectl -n kube-system get secret | grep default-token | cut -d " " -f1)
microk8s kubectl -n kube-system describe secret $token

In an RBAC enabled setup (microk8s enable RBAC) you need to create a user with restricted
permissions as shown in:
https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md

Enabling DNS
Applying manifest
serviceaccount/coredns created
configmap/coredns created
deployment.apps/coredns created
service/kube-dns created
clusterrole.rbac.authorization.k8s.io/coredns created
clusterrolebinding.rbac.authorization.k8s.io/coredns created
Restarting kubelet
DNS is enabled

~$ sudo microk8s.status --wait-ready
microk8s is running
high-availability: no
  datastore master nodes: 127.0.0.1:19001
  datastore standby nodes: none
addons:
  enabled:
    dashboard            # The Kubernetes dashboard
    dns                  # CoreDNS
    ha-cluster           # Configure high availability on the current node
    metrics-server       # K8s Metrics Server for API access to service metrics

~$ sudo microk8s.kubectl get pods --all-namespaces
NAMESPACE     NAME                                         READY   STATUS    RESTARTS   AGE
kube-system   metrics-server-8bbfb4bdb-849rc               1/1     Running   0          6m56s
kube-system   calico-node-l5j74                            1/1     Running   0          25h
kube-system   calico-kube-controllers-847c8c99d-zzp9s      1/1     Running   0          25h
kube-system   kubernetes-dashboard-7ffd448895-6l44t        1/1     Running   0          6m27s
kube-system   dashboard-metrics-scraper-6c4568dc68-l47jd   1/1     Running   0          6m19s
kube-system   coredns-86f78bb79c-d9nh6                     1/1     Running   0          3m17s

~$ hostname -I
172.30.159.100 172.17.0.1 10.1.201.128

~$ sudo microk8s.config
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURBVENDQWVtZ0F3SUJBZ0lKQUxhNGsyOFNzUTAvTUEwR0NTcUdTSWIzRFFFQkN3VUFNQmN4RlRBVEJnTlYKQkFNTURERXdMakUxTWk0eE9ETXVNVEFlRncweU1EQTVNVGd4TVRVNE5ERmFGdzB6TURBNU1UWXhNVFU0TkRGYQpNQmN4RlRBVEJnTlZCQU1NRERFd0xqRTFNaTR4T0RNdU1UQ0NBU0l3RFFZSktvWklodmNOQVFFQkJRQURnZ0VQCkFEQ0NBUW9DZ2dFQkFOZGZIYmM4MWdvZXVmbkEwZ3pqQ3ZWM1ptYkdhSmtHQzlTT3Y4cWUvM2lyT3ZWYXFTanYKT2VMems5ZlM1aDErNTRvSzRTaDl4aWxsYVZOZzJFOTduNmhsMlg2OHFod2I3MkdsRTFUVXVKbm1uTkJJOHlCcAplYUVxa2Q2RGtLOGpBS2l3T2s3NXM0SG44MEkwdlpnTUxMc0hXRDIyTlhvbnJhdmxJZ2VMK3dTOUZhYXNXN2hyCmF4MVRBeUh3ZG5IMHJ1bjRycjAvd0dSeTJmOVNUWTJvTnphc0JjYVp5VDI4Y29XVHdCOWxZL0NuVjg4SnFhUFAKUENEcndVTWFJRFdBYUEzSVNJdUtkdVJZbDhScGlWeFVTWlBtaHRQeFhqUGU4SEkvaXhxQzdxb2p3dTVIM0dqTgpoVm1RUHlqNWlackpQd0s4WHZBODhmd09GNVltWVlFa1FUc0NBd0VBQWFOUU1FNHdIUVlEVlIwT0JCWUVGSm9UCkhrZ0x5c3RjQ1JlWjNYMnd4eXNtamVnbE1COEdBMVVkSXdRWU1CYUFGSm9USGtnTHlzdGNDUmVaM1gyd3h5c20KamVnbE1Bd0dBMVVkRXdRRk1BTUJBZjh3RFFZSktvWklodmNOQVFFTEJRQURnZ0VCQUdkRy9NTDNLM0JIbmprOQpzdnBVdmVpdEV4b0xzb2hCY2FaRnAyWnQvbUZhaEpBMU1WWUN6aFlZalhuczVsS2FWclVseGRRRVh1cUdxbEFKCkV2OGsrY2pvVzl4cWlFY1dSVUFmUCtWZWg3cXMvK3YvRE93Uzl6MTNKRGljdWtEQWZFNW1UY3RVY1hWbURUc2MKalR5U0ZPcTZPZmg2LzROQStzejJzZUFHUmM2ME5uYkdENURBblpGL0VHVmlybVp6QzVhZis5MVlzN0pRcHkxUwpxZ3Z2cHA1MVhvYmVSUHBXUGxIcEhKVUtPcnd5c29xbTA0RFAzRGwwOWdMMjc4dW9XeVRiWWdyQTFWTmtETlNMCks4SlBuaTlEc21FRXJQVWpIbnVDR0RIQzFGOGxUSzlzdEdWbXc4SzdhdjdXM1dzbmFsbDlnQmRHOFNuRFk3WGoKbll1cldHND0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
    server: https://172.30.159.100:16443
  name: microk8s-cluster
contexts:
- context:
    cluster: microk8s-cluster
    user: admin
  name: microk8s
current-context: microk8s
kind: Config
preferences: {}
users:
- name: admin
  user:
    token: RXlXV0FWa3ZEZ2wxak5Ob0JmdzhGRkgwWTErd3Vwd0UzNjhSTlRlWllMZz0K

JY: save name and token to:

/var/snap/microk8s/current/credentials/basic_auth.csv

~$ sudo ls -la /var/snap/microk8s/current/credentials/
total 32
drwxrwx---  2 root microk8s 4096 Sep 18 07:58 .
drwxr-xr-x 10 root root     4096 Sep 18 07:58 ..
-rw-rw----  1 root microk8s 1841 Sep 18 07:58 client.config
-rw-rw----  1 root microk8s 1851 Sep 18 07:58 controller.config
-rw-rw----  1 root microk8s  482 Sep 18 07:58 known_tokens.csv
-rw-rw----  1 root microk8s 1845 Sep 18 07:58 kubelet.config
-rw-rw----  1 root microk8s 1849 Sep 18 07:58 proxy.config
-rw-rw----  1 root microk8s 1849 Sep 18 07:58 scheduler.config

~$ sudo cat /var/snap/microk8s/current/credentials/known_tokens.csv
RXlXV0FWa3ZEZ2wxak5Ob0JmdzhGRkgwWTErd3Vwd0UzNjhSTlRlWllMZz0K,admin,admin,"system:masters"
eDV0bE13cWZRQUdaV0E5Qm56M0RjM3A2N0F5eXNtdE1FUjUvdnYxVUZ0ND0K,system:kube-proxy,kube-proxy
ZHl0dGtvaWszMUx5ZUpUeHlFY3djYVdLaVg2V08xTUFBZlRrUk1UVHZQdz0K,system:node:emily15,kubelet-0,"system:nodes"
ZHgzVGFtUDRKZC9tVnNoblowdHJrcFRHOERMNXZpeENYNkxZbXEwTFdCST0K,system:kube-controller-manager,controller
cTBscUNuNFdxNk5RUzBlVVZISkE5cHRsVVBRSFJkNWg1cmwwdFZCNGRWTT0K,system:kube-scheduler,scheduler

~$ sudo cat /var/snap/microk8s/current/credentials/client.config
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURBVENDQWVtZ0F3SUJBZ0lKQUxhNGsyOFNzUTAvTUEwR0NTcUdTSWIzRFFFQkN3VUFNQmN4RlRBVEJnTlYKQkFNTURERXdMakUxTWk0eE9ETXVNVEFlRncweU1EQTVNVGd4TVRVNE5ERmFGdzB6TURBNU1UWXhNVFU0TkRGYQpNQmN4RlRBVEJnTlZCQU1NRERFd0xqRTFNaTR4T0RNdU1UQ0NBU0l3RFFZSktvWklodmNOQVFFQkJRQURnZ0VQCkFEQ0NBUW9DZ2dFQkFOZGZIYmM4MWdvZXVmbkEwZ3pqQ3ZWM1ptYkdhSmtHQzlTT3Y4cWUvM2lyT3ZWYXFTanYKT2VMems5ZlM1aDErNTRvSzRTaDl4aWxsYVZOZzJFOTduNmhsMlg2OHFod2I3MkdsRTFUVXVKbm1uTkJJOHlCcAplYUVxa2Q2RGtLOGpBS2l3T2s3NXM0SG44MEkwdlpnTUxMc0hXRDIyTlhvbnJhdmxJZ2VMK3dTOUZhYXNXN2hyCmF4MVRBeUh3ZG5IMHJ1bjRycjAvd0dSeTJmOVNUWTJvTnphc0JjYVp5VDI4Y29XVHdCOWxZL0NuVjg4SnFhUFAKUENEcndVTWFJRFdBYUEzSVNJdUtkdVJZbDhScGlWeFVTWlBtaHRQeFhqUGU4SEkvaXhxQzdxb2p3dTVIM0dqTgpoVm1RUHlqNWlackpQd0s4WHZBODhmd09GNVltWVlFa1FUc0NBd0VBQWFOUU1FNHdIUVlEVlIwT0JCWUVGSm9UCkhrZ0x5c3RjQ1JlWjNYMnd4eXNtamVnbE1COEdBMVVkSXdRWU1CYUFGSm9USGtnTHlzdGNDUmVaM1gyd3h5c20KamVnbE1Bd0dBMVVkRXdRRk1BTUJBZjh3RFFZSktvWklodmNOQVFFTEJRQURnZ0VCQUdkRy9NTDNLM0JIbmprOQpzdnBVdmVpdEV4b0xzb2hCY2FaRnAyWnQvbUZhaEpBMU1WWUN6aFlZalhuczVsS2FWclVseGRRRVh1cUdxbEFKCkV2OGsrY2pvVzl4cWlFY1dSVUFmUCtWZWg3cXMvK3YvRE93Uzl6MTNKRGljdWtEQWZFNW1UY3RVY1hWbURUc2MKalR5U0ZPcTZPZmg2LzROQStzejJzZUFHUmM2ME5uYkdENURBblpGL0VHVmlybVp6QzVhZis5MVlzN0pRcHkxUwpxZ3Z2cHA1MVhvYmVSUHBXUGxIcEhKVUtPcnd5c29xbTA0RFAzRGwwOWdMMjc4dW9XeVRiWWdyQTFWTmtETlNMCks4SlBuaTlEc21FRXJQVWpIbnVDR0RIQzFGOGxUSzlzdEdWbXc4SzdhdjdXM1dzbmFsbDlnQmRHOFNuRFk3WGoKbll1cldHND0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
    server: https://127.0.0.1:16443
  name: microk8s-cluster
contexts:
- context:
    cluster: microk8s-cluster
    user: admin
  name: microk8s
current-context: microk8s
kind: Config
preferences: {}
users:
- name: admin
  user:
    token: RXlXV0FWa3ZEZ2wxak5Ob0JmdzhGRkgwWTErd3Vwd0UzNjhSTlRlWllMZz0K

~$ sudo cat /var/snap/microk8s/current/credentials/controller.config
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURBVENDQWVtZ0F3SUJBZ0lKQUxhNGsyOFNzUTAvTUEwR0NTcUdTSWIzRFFFQkN3VUFNQmN4RlRBVEJnTlYKQkFNTURERXdMakUxTWk0eE9ETXVNVEFlRncweU1EQTVNVGd4TVRVNE5ERmFGdzB6TURBNU1UWXhNVFU0TkRGYQpNQmN4RlRBVEJnTlZCQU1NRERFd0xqRTFNaTR4T0RNdU1UQ0NBU0l3RFFZSktvWklodmNOQVFFQkJRQURnZ0VQCkFEQ0NBUW9DZ2dFQkFOZGZIYmM4MWdvZXVmbkEwZ3pqQ3ZWM1ptYkdhSmtHQzlTT3Y4cWUvM2lyT3ZWYXFTanYKT2VMems5ZlM1aDErNTRvSzRTaDl4aWxsYVZOZzJFOTduNmhsMlg2OHFod2I3MkdsRTFUVXVKbm1uTkJJOHlCcAplYUVxa2Q2RGtLOGpBS2l3T2s3NXM0SG44MEkwdlpnTUxMc0hXRDIyTlhvbnJhdmxJZ2VMK3dTOUZhYXNXN2hyCmF4MVRBeUh3ZG5IMHJ1bjRycjAvd0dSeTJmOVNUWTJvTnphc0JjYVp5VDI4Y29XVHdCOWxZL0NuVjg4SnFhUFAKUENEcndVTWFJRFdBYUEzSVNJdUtkdVJZbDhScGlWeFVTWlBtaHRQeFhqUGU4SEkvaXhxQzdxb2p3dTVIM0dqTgpoVm1RUHlqNWlackpQd0s4WHZBODhmd09GNVltWVlFa1FUc0NBd0VBQWFOUU1FNHdIUVlEVlIwT0JCWUVGSm9UCkhrZ0x5c3RjQ1JlWjNYMnd4eXNtamVnbE1COEdBMVVkSXdRWU1CYUFGSm9USGtnTHlzdGNDUmVaM1gyd3h5c20KamVnbE1Bd0dBMVVkRXdRRk1BTUJBZjh3RFFZSktvWklodmNOQVFFTEJRQURnZ0VCQUdkRy9NTDNLM0JIbmprOQpzdnBVdmVpdEV4b0xzb2hCY2FaRnAyWnQvbUZhaEpBMU1WWUN6aFlZalhuczVsS2FWclVseGRRRVh1cUdxbEFKCkV2OGsrY2pvVzl4cWlFY1dSVUFmUCtWZWg3cXMvK3YvRE93Uzl6MTNKRGljdWtEQWZFNW1UY3RVY1hWbURUc2MKalR5U0ZPcTZPZmg2LzROQStzejJzZUFHUmM2ME5uYkdENURBblpGL0VHVmlybVp6QzVhZis5MVlzN0pRcHkxUwpxZ3Z2cHA1MVhvYmVSUHBXUGxIcEhKVUtPcnd5c29xbTA0RFAzRGwwOWdMMjc4dW9XeVRiWWdyQTFWTmtETlNMCks4SlBuaTlEc21FRXJQVWpIbnVDR0RIQzFGOGxUSzlzdEdWbXc4SzdhdjdXM1dzbmFsbDlnQmRHOFNuRFk3WGoKbll1cldHND0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
    server: https://127.0.0.1:16443
  name: microk8s-cluster
contexts:
- context:
    cluster: microk8s-cluster
    user: controller
  name: microk8s
current-context: microk8s
kind: Config
preferences: {}
users:
- name: controller
  user:
    token: ZHgzVGFtUDRKZC9tVnNoblowdHJrcFRHOERMNXZpeENYNkxZbXEwTFdCST0K

~$ sudo cat /var/snap/microk8s/current/credentials/kubelet.config
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURBVENDQWVtZ0F3SUJBZ0lKQUxhNGsyOFNzUTAvTUEwR0NTcUdTSWIzRFFFQkN3VUFNQmN4RlRBVEJnTlYKQkFNTURERXdMakUxTWk0eE9ETXVNVEFlRncweU1EQTVNVGd4TVRVNE5ERmFGdzB6TURBNU1UWXhNVFU0TkRGYQpNQmN4RlRBVEJnTlZCQU1NRERFd0xqRTFNaTR4T0RNdU1UQ0NBU0l3RFFZSktvWklodmNOQVFFQkJRQURnZ0VQCkFEQ0NBUW9DZ2dFQkFOZGZIYmM4MWdvZXVmbkEwZ3pqQ3ZWM1ptYkdhSmtHQzlTT3Y4cWUvM2lyT3ZWYXFTanYKT2VMems5ZlM1aDErNTRvSzRTaDl4aWxsYVZOZzJFOTduNmhsMlg2OHFod2I3MkdsRTFUVXVKbm1uTkJJOHlCcAplYUVxa2Q2RGtLOGpBS2l3T2s3NXM0SG44MEkwdlpnTUxMc0hXRDIyTlhvbnJhdmxJZ2VMK3dTOUZhYXNXN2hyCmF4MVRBeUh3ZG5IMHJ1bjRycjAvd0dSeTJmOVNUWTJvTnphc0JjYVp5VDI4Y29XVHdCOWxZL0NuVjg4SnFhUFAKUENEcndVTWFJRFdBYUEzSVNJdUtkdVJZbDhScGlWeFVTWlBtaHRQeFhqUGU4SEkvaXhxQzdxb2p3dTVIM0dqTgpoVm1RUHlqNWlackpQd0s4WHZBODhmd09GNVltWVlFa1FUc0NBd0VBQWFOUU1FNHdIUVlEVlIwT0JCWUVGSm9UCkhrZ0x5c3RjQ1JlWjNYMnd4eXNtamVnbE1COEdBMVVkSXdRWU1CYUFGSm9USGtnTHlzdGNDUmVaM1gyd3h5c20KamVnbE1Bd0dBMVVkRXdRRk1BTUJBZjh3RFFZSktvWklodmNOQVFFTEJRQURnZ0VCQUdkRy9NTDNLM0JIbmprOQpzdnBVdmVpdEV4b0xzb2hCY2FaRnAyWnQvbUZhaEpBMU1WWUN6aFlZalhuczVsS2FWclVseGRRRVh1cUdxbEFKCkV2OGsrY2pvVzl4cWlFY1dSVUFmUCtWZWg3cXMvK3YvRE93Uzl6MTNKRGljdWtEQWZFNW1UY3RVY1hWbURUc2MKalR5U0ZPcTZPZmg2LzROQStzejJzZUFHUmM2ME5uYkdENURBblpGL0VHVmlybVp6QzVhZis5MVlzN0pRcHkxUwpxZ3Z2cHA1MVhvYmVSUHBXUGxIcEhKVUtPcnd5c29xbTA0RFAzRGwwOWdMMjc4dW9XeVRiWWdyQTFWTmtETlNMCks4SlBuaTlEc21FRXJQVWpIbnVDR0RIQzFGOGxUSzlzdEdWbXc4SzdhdjdXM1dzbmFsbDlnQmRHOFNuRFk3WGoKbll1cldHND0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
    server: https://127.0.0.1:16443
  name: microk8s-cluster
contexts:
- context:
    cluster: microk8s-cluster
    user: kubelet
  name: microk8s
current-context: microk8s
kind: Config
preferences: {}
users:
- name: kubelet
  user:
    token: ZHl0dGtvaWszMUx5ZUpUeHlFY3djYVdLaVg2V08xTUFBZlRrUk1UVHZQdz0K

~$ sudo cat /var/snap/microk8s/current/credentials/proxy.config
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURBVENDQWVtZ0F3SUJBZ0lKQUxhNGsyOFNzUTAvTUEwR0NTcUdTSWIzRFFFQkN3VUFNQmN4RlRBVEJnTlYKQkFNTURERXdMakUxTWk0eE9ETXVNVEFlRncweU1EQTVNVGd4TVRVNE5ERmFGdzB6TURBNU1UWXhNVFU0TkRGYQpNQmN4RlRBVEJnTlZCQU1NRERFd0xqRTFNaTR4T0RNdU1UQ0NBU0l3RFFZSktvWklodmNOQVFFQkJRQURnZ0VQCkFEQ0NBUW9DZ2dFQkFOZGZIYmM4MWdvZXVmbkEwZ3pqQ3ZWM1ptYkdhSmtHQzlTT3Y4cWUvM2lyT3ZWYXFTanYKT2VMems5ZlM1aDErNTRvSzRTaDl4aWxsYVZOZzJFOTduNmhsMlg2OHFod2I3MkdsRTFUVXVKbm1uTkJJOHlCcAplYUVxa2Q2RGtLOGpBS2l3T2s3NXM0SG44MEkwdlpnTUxMc0hXRDIyTlhvbnJhdmxJZ2VMK3dTOUZhYXNXN2hyCmF4MVRBeUh3ZG5IMHJ1bjRycjAvd0dSeTJmOVNUWTJvTnphc0JjYVp5VDI4Y29XVHdCOWxZL0NuVjg4SnFhUFAKUENEcndVTWFJRFdBYUEzSVNJdUtkdVJZbDhScGlWeFVTWlBtaHRQeFhqUGU4SEkvaXhxQzdxb2p3dTVIM0dqTgpoVm1RUHlqNWlackpQd0s4WHZBODhmd09GNVltWVlFa1FUc0NBd0VBQWFOUU1FNHdIUVlEVlIwT0JCWUVGSm9UCkhrZ0x5c3RjQ1JlWjNYMnd4eXNtamVnbE1COEdBMVVkSXdRWU1CYUFGSm9USGtnTHlzdGNDUmVaM1gyd3h5c20KamVnbE1Bd0dBMVVkRXdRRk1BTUJBZjh3RFFZSktvWklodmNOQVFFTEJRQURnZ0VCQUdkRy9NTDNLM0JIbmprOQpzdnBVdmVpdEV4b0xzb2hCY2FaRnAyWnQvbUZhaEpBMU1WWUN6aFlZalhuczVsS2FWclVseGRRRVh1cUdxbEFKCkV2OGsrY2pvVzl4cWlFY1dSVUFmUCtWZWg3cXMvK3YvRE93Uzl6MTNKRGljdWtEQWZFNW1UY3RVY1hWbURUc2MKalR5U0ZPcTZPZmg2LzROQStzejJzZUFHUmM2ME5uYkdENURBblpGL0VHVmlybVp6QzVhZis5MVlzN0pRcHkxUwpxZ3Z2cHA1MVhvYmVSUHBXUGxIcEhKVUtPcnd5c29xbTA0RFAzRGwwOWdMMjc4dW9XeVRiWWdyQTFWTmtETlNMCks4SlBuaTlEc21FRXJQVWpIbnVDR0RIQzFGOGxUSzlzdEdWbXc4SzdhdjdXM1dzbmFsbDlnQmRHOFNuRFk3WGoKbll1cldHND0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
    server: https://127.0.0.1:16443
  name: microk8s-cluster
contexts:
- context:
    cluster: microk8s-cluster
    user: kubeproxy
  name: microk8s
current-context: microk8s
kind: Config
preferences: {}
users:
- name: kubeproxy
  user:
    token: eDV0bE13cWZRQUdaV0E5Qm56M0RjM3A2N0F5eXNtdE1FUjUvdnYxVUZ0ND0K

~$ sudo cat /var/snap/microk8s/current/credentials/scheduler.config
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURBVENDQWVtZ0F3SUJBZ0lKQUxhNGsyOFNzUTAvTUEwR0NTcUdTSWIzRFFFQkN3VUFNQmN4RlRBVEJnTlYKQkFNTURERXdMakUxTWk0eE9ETXVNVEFlRncweU1EQTVNVGd4TVRVNE5ERmFGdzB6TURBNU1UWXhNVFU0TkRGYQpNQmN4RlRBVEJnTlZCQU1NRERFd0xqRTFNaTR4T0RNdU1UQ0NBU0l3RFFZSktvWklodmNOQVFFQkJRQURnZ0VQCkFEQ0NBUW9DZ2dFQkFOZGZIYmM4MWdvZXVmbkEwZ3pqQ3ZWM1ptYkdhSmtHQzlTT3Y4cWUvM2lyT3ZWYXFTanYKT2VMems5ZlM1aDErNTRvSzRTaDl4aWxsYVZOZzJFOTduNmhsMlg2OHFod2I3MkdsRTFUVXVKbm1uTkJJOHlCcAplYUVxa2Q2RGtLOGpBS2l3T2s3NXM0SG44MEkwdlpnTUxMc0hXRDIyTlhvbnJhdmxJZ2VMK3dTOUZhYXNXN2hyCmF4MVRBeUh3ZG5IMHJ1bjRycjAvd0dSeTJmOVNUWTJvTnphc0JjYVp5VDI4Y29XVHdCOWxZL0NuVjg4SnFhUFAKUENEcndVTWFJRFdBYUEzSVNJdUtkdVJZbDhScGlWeFVTWlBtaHRQeFhqUGU4SEkvaXhxQzdxb2p3dTVIM0dqTgpoVm1RUHlqNWlackpQd0s4WHZBODhmd09GNVltWVlFa1FUc0NBd0VBQWFOUU1FNHdIUVlEVlIwT0JCWUVGSm9UCkhrZ0x5c3RjQ1JlWjNYMnd4eXNtamVnbE1COEdBMVVkSXdRWU1CYUFGSm9USGtnTHlzdGNDUmVaM1gyd3h5c20KamVnbE1Bd0dBMVVkRXdRRk1BTUJBZjh3RFFZSktvWklodmNOQVFFTEJRQURnZ0VCQUdkRy9NTDNLM0JIbmprOQpzdnBVdmVpdEV4b0xzb2hCY2FaRnAyWnQvbUZhaEpBMU1WWUN6aFlZalhuczVsS2FWclVseGRRRVh1cUdxbEFKCkV2OGsrY2pvVzl4cWlFY1dSVUFmUCtWZWg3cXMvK3YvRE93Uzl6MTNKRGljdWtEQWZFNW1UY3RVY1hWbURUc2MKalR5U0ZPcTZPZmg2LzROQStzejJzZUFHUmM2ME5uYkdENURBblpGL0VHVmlybVp6QzVhZis5MVlzN0pRcHkxUwpxZ3Z2cHA1MVhvYmVSUHBXUGxIcEhKVUtPcnd5c29xbTA0RFAzRGwwOWdMMjc4dW9XeVRiWWdyQTFWTmtETlNMCks4SlBuaTlEc21FRXJQVWpIbnVDR0RIQzFGOGxUSzlzdEdWbXc4SzdhdjdXM1dzbmFsbDlnQmRHOFNuRFk3WGoKbll1cldHND0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
    server: https://127.0.0.1:16443
  name: microk8s-cluster
contexts:
- context:
    cluster: microk8s-cluster
    user: scheduler
  name: microk8s
current-context: microk8s
kind: Config
preferences: {}
users:
- name: scheduler
  user:
    token: cTBscUNuNFdxNk5RUzBlVVZISkE5cHRsVVBRSFJkNWg1cmwwdFZCNGRWTT0K
	
git 
git_repos: https://github.com/dontrebootme/docker-microbot.git
description: small container with simple webpage for clustering demo

sudo microk8s.kubectl create deployment microbot --image=dontrebootme/microbot:v1

~$ sudo microk8s.kubectl create deployment microbot --image=dontrebootme/microbot:v1
deployment.apps/microbot created

~$ sudo microk8s.kubectl get pods --all-namespaces
NAMESPACE     NAME                                         READY   STATUS    RESTARTS   AGE
kube-system   metrics-server-8bbfb4bdb-849rc               1/1     Running   0          46m
kube-system   calico-node-l5j74                            1/1     Running   0          25h
kube-system   calico-kube-controllers-847c8c99d-zzp9s      1/1     Running   0          25h
kube-system   kubernetes-dashboard-7ffd448895-6l44t        1/1     Running   0          46m
kube-system   dashboard-metrics-scraper-6c4568dc68-l47jd   1/1     Running   0          46m
kube-system   coredns-86f78bb79c-d9nh6                     1/1     Running   0          43m
default       microbot-5f5499d479-g8qc5                    1/1     Running   0          2m12s

~$ sudo microk8s.kubectl scale deployment microbot --replicas=5
deployment.apps/microbot scaled
~$ sudo microk8s.kubectl get pods --all-namespaces
NAMESPACE     NAME                                         READY   STATUS              RESTARTS   AGE
kube-system   metrics-server-8bbfb4bdb-849rc               1/1     Running             0          48m
kube-system   calico-node-l5j74                            1/1     Running             0          25h
kube-system   calico-kube-controllers-847c8c99d-zzp9s      1/1     Running             0          25h
kube-system   kubernetes-dashboard-7ffd448895-6l44t        1/1     Running             0          47m
kube-system   dashboard-metrics-scraper-6c4568dc68-l47jd   1/1     Running             0          47m
kube-system   coredns-86f78bb79c-d9nh6                     1/1     Running             0          44m
default       microbot-5f5499d479-g8qc5                    1/1     Running             0          3m48s
default       microbot-5f5499d479-lpjfl                    0/1     Pending             0          10s
default       microbot-5f5499d479-h4kk6                    0/1     Pending             0          8s
default       microbot-5f5499d479-b79w2                    0/1     Pending             0          10s
default       microbot-5f5499d479-56p9b                    0/1     ContainerCreating   0          12s
~$ sudo microk8s.kubectl get pods --all-namespaces
NAMESPACE     NAME                                         READY   STATUS              RESTARTS   AGE
kube-system   metrics-server-8bbfb4bdb-849rc               1/1     Running             0          48m
kube-system   calico-node-l5j74                            1/1     Running             0          25h
kube-system   calico-kube-controllers-847c8c99d-zzp9s      1/1     Running             0          25h
kube-system   kubernetes-dashboard-7ffd448895-6l44t        1/1     Running             0          48m
kube-system   dashboard-metrics-scraper-6c4568dc68-l47jd   1/1     Running             0          47m
kube-system   coredns-86f78bb79c-d9nh6                     1/1     Running             0          44m
default       microbot-5f5499d479-g8qc5                    1/1     Running             0          3m55s
default       microbot-5f5499d479-56p9b                    0/1     ContainerCreating   0          19s
default       microbot-5f5499d479-h4kk6                    0/1     Pending             0          15s
default       microbot-5f5499d479-lpjfl                    0/1     ContainerCreating   0          17s
default       microbot-5f5499d479-b79w2                    0/1     ContainerCreating   0          17s
~$ sudo microk8s.kubectl get pods --all-namespaces
NAMESPACE     NAME                                         READY   STATUS              RESTARTS   AGE
kube-system   metrics-server-8bbfb4bdb-849rc               1/1     Running             0          48m
kube-system   calico-node-l5j74                            1/1     Running             0          25h
kube-system   calico-kube-controllers-847c8c99d-zzp9s      1/1     Running             0          25h
kube-system   kubernetes-dashboard-7ffd448895-6l44t        1/1     Running             0          48m
kube-system   dashboard-metrics-scraper-6c4568dc68-l47jd   1/1     Running             0          48m
kube-system   coredns-86f78bb79c-d9nh6                     1/1     Running             0          44m
default       microbot-5f5499d479-g8qc5                    1/1     Running             0          4m3s
default       microbot-5f5499d479-lpjfl                    0/1     ContainerCreating   0          25s
default       microbot-5f5499d479-b79w2                    0/1     ContainerCreating   0          25s
default       microbot-5f5499d479-56p9b                    0/1     ContainerCreating   0          27s
default       microbot-5f5499d479-h4kk6                    0/1     ContainerCreating   0          23s
~$ sudo microk8s.kubectl get pods --all-namespaces
NAMESPACE     NAME                                         READY   STATUS              RESTARTS   AGE
kube-system   metrics-server-8bbfb4bdb-849rc               1/1     Running             0          48m
kube-system   calico-node-l5j74                            1/1     Running             0          25h
kube-system   calico-kube-controllers-847c8c99d-zzp9s      1/1     Running             0          25h
kube-system   kubernetes-dashboard-7ffd448895-6l44t        1/1     Running             0          48m
kube-system   dashboard-metrics-scraper-6c4568dc68-l47jd   1/1     Running             0          48m
kube-system   coredns-86f78bb79c-d9nh6                     1/1     Running             0          45m
default       microbot-5f5499d479-g8qc5                    1/1     Running             0          4m11s
default       microbot-5f5499d479-lpjfl                    0/1     ContainerCreating   0          33s
default       microbot-5f5499d479-b79w2                    0/1     ContainerCreating   0          33s
default       microbot-5f5499d479-56p9b                    0/1     ContainerCreating   0          35s
default       microbot-5f5499d479-h4kk6                    0/1     ContainerCreating   0          31s
~$ sudo microk8s.kubectl get pods --default
Error: unknown flag: --default
See 'kubectl get --help' for usage.
~$ sudo microk8s.kubectl get pods --all-namespaces
NAMESPACE     NAME                                         READY   STATUS    RESTARTS   AGE
kube-system   metrics-server-8bbfb4bdb-849rc               1/1     Running   0          49m
kube-system   calico-node-l5j74                            1/1     Running   0          25h
kube-system   calico-kube-controllers-847c8c99d-zzp9s      1/1     Running   0          25h
kube-system   kubernetes-dashboard-7ffd448895-6l44t        1/1     Running   0          49m
kube-system   dashboard-metrics-scraper-6c4568dc68-l47jd   1/1     Running   0          49m
kube-system   coredns-86f78bb79c-d9nh6                     1/1     Running   0          45m
default       microbot-5f5499d479-g8qc5                    1/1     Running   0          5m4s
default       microbot-5f5499d479-56p9b                    1/1     Running   0          88s
default       microbot-5f5499d479-lpjfl                    1/1     Running   0          86s
default       microbot-5f5499d479-b79w2                    1/1     Running   0          86s
default       microbot-5f5499d479-h4kk6                    1/1     Running   0          84s

~$ sudo microk8s.kubectl get pods -n default
NAME                        READY   STATUS    RESTARTS   AGE
microbot-5f5499d479-g8qc5   1/1     Running   0          7m
microbot-5f5499d479-56p9b   1/1     Running   0          3m24s
microbot-5f5499d479-lpjfl   1/1     Running   0          3m22s
microbot-5f5499d479-b79w2   1/1     Running   0          3m22s
microbot-5f5499d479-h4kk6   1/1     Running   0          3m20s


~$ sudo microk8s.kubectl expose deployment microbot --type=NodePort --port=8088 --name=microbot-service
service/microbot-service exposed

~$ sudo microk8s.kubectl get all --all-namespaces
NAMESPACE     NAME                                             READY   STATUS    RESTARTS   AGE
kube-system   pod/metrics-server-8bbfb4bdb-849rc               1/1     Running   0          55m
kube-system   pod/calico-node-l5j74                            1/1     Running   0          25h
kube-system   pod/calico-kube-controllers-847c8c99d-zzp9s      1/1     Running   0          25h
kube-system   pod/kubernetes-dashboard-7ffd448895-6l44t        1/1     Running   0          54m
kube-system   pod/dashboard-metrics-scraper-6c4568dc68-l47jd   1/1     Running   0          54m
kube-system   pod/coredns-86f78bb79c-d9nh6                     1/1     Running   0          51m
default       pod/microbot-5f5499d479-g8qc5                    1/1     Running   0          10m
default       pod/microbot-5f5499d479-56p9b                    1/1     Running   0          6m52s
default       pod/microbot-5f5499d479-lpjfl                    1/1     Running   0          6m50s
default       pod/microbot-5f5499d479-b79w2                    1/1     Running   0          6m50s
default       pod/microbot-5f5499d479-h4kk6                    1/1     Running   0          6m48s

NAMESPACE     NAME                                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                  AGE
default       service/kubernetes                  ClusterIP   10.152.183.1     <none>        443/TCP                  25h
kube-system   service/metrics-server              ClusterIP   10.152.183.86    <none>        443/TCP                  55m
kube-system   service/kubernetes-dashboard        ClusterIP   10.152.183.8     <none>        443/TCP                  54m
kube-system   service/dashboard-metrics-scraper   ClusterIP   10.152.183.217   <none>        8000/TCP                 54m
kube-system   service/kube-dns                    ClusterIP   10.152.183.10    <none>        53/UDP,53/TCP,9153/TCP   54m
default       service/microbot-service            NodePort    10.152.183.69    <none>        8088:31069/TCP           86s

NAMESPACE     NAME                         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
kube-system   daemonset.apps/calico-node   1         1         1       1            1           kubernetes.io/os=linux   25h

NAMESPACE     NAME                                        READY   UP-TO-DATE   AVAILABLE   AGE
kube-system   deployment.apps/calico-kube-controllers     1/1     1            1           25h
kube-system   deployment.apps/metrics-server              1/1     1            1           55m
kube-system   deployment.apps/kubernetes-dashboard        1/1     1            1           54m
kube-system   deployment.apps/dashboard-metrics-scraper   1/1     1            1           54m
kube-system   deployment.apps/coredns                     1/1     1            1           54m
default       deployment.apps/microbot                    5/5     5            5           10m

NAMESPACE     NAME                                                   DESIRED   CURRENT   READY   AGE
kube-system   replicaset.apps/calico-kube-controllers-847c8c99d      1         1         1       25h
kube-system   replicaset.apps/metrics-server-8bbfb4bdb               1         1         1       55m
kube-system   replicaset.apps/kubernetes-dashboard-7ffd448895        1         1         1       54m
kube-system   replicaset.apps/dashboard-metrics-scraper-6c4568dc68   1         1         1       54m
kube-system   replicaset.apps/coredns-86f78bb79c                     1         1         1       53m
default       replicaset.apps/microbot-5f5499d479                    5         5         5       10m


### Referecnces:
https://www.section.io/engineering-education/installing-k8-ubuntu-server/
https://github.com/anbox/anbox/issues/265
https://discourse.ubuntu.com/t/using-snapd-in-wsl2/12113
https://forum.snapcraft.io/t/running-snaps-on-wsl2-insiders-only-for-now/13033
https://dev.to/bartr/install-docker-on-windows-subsystem-for-linux-v2-ubuntu-5dl7