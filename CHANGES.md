# Typhoon

Notable changes between versions.

## Latest

* Update etcd from v3.3.10 to [v3.3.11](https://github.com/etcd-io/etcd/blob/master/CHANGELOG-3.3.md#v3311-2019-1-11)
* Update CoreDNS from v1.3.0 to [v1.3.1](https://coredns.io/2019/01/13/coredns-1.3.1-release/)
* Fix automatic worker deletion on shutdown for cloud platforms
  * Lowering Kubelet privileges in [#372](https://github.com/poseidon/typhoon/pull/372) dropped a needed node deletion authorization. Scale-in due to manual terraform apply (any cloud), AWS spot termination, or Azure low priority deletion left old nodes registered, requiring manual deletion (`kubectl delete node name`)

#### AWS

* Add `ingress_zone_id` output with the NLB DNS name's Route53 zone for use in alias records ([#380](https://github.com/poseidon/typhoon/pull/380))

#### Addons

* Update nginx-ingress from v0.21.0 to v0.22.0
* Update Prometheus from v2.6.0 to v2.6.1
* Update kube-state-metrics from v1.4.0 to v1.5.0
* Update Grafana from v5.4.2 to v5.4.3

## v1.13.2

* Kubernetes [v1.13.2](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.13.md#v1132)
* Add ServiceAccounts for `kube-apiserver` and `kube-scheduler` ([#370](https://github.com/poseidon/typhoon/pull/370))
* Use lower-privilege TLS client certificates for Kubelets ([#372](https://github.com/poseidon/typhoon/pull/372))
* Use HTTPS liveness probes for `kube-scheduler` and `kube-controller-manager` ([#377](https://github.com/poseidon/typhoon/pull/377))
* Update CoreDNS from v1.2.6 to [v1.3.0](https://coredns.io/2018/12/15/coredns-1.3.0-release/)
* Allow the `certificates.k8s.io` API to issue certificates signed by the cluster CA ([#376](https://github.com/poseidon/typhoon/pull/376))
  * Configure controller manager to sign CSRs that are manually [approved](https://kubernetes.io/docs/tasks/tls/managing-tls-in-a-cluster) by an administrator

#### AWS

* Change `controller_type` and `worker_type` default from t2.small to t3.small ([#365](https://github.com/poseidon/typhoon/pull/365))
  * t3.small is cheaper, provides 2 vCPU (instead of 1), and 5 Gbps of pod-to-pod bandwidth!

#### Bare-Metal

* Remove the `kubeconfig` output variable

#### Addons

* Update Prometheus from v2.5.0 to v2.6.0

## v1.13.1

* Kubernetes [v1.13.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.13.md#v1131)
* Update Calico from v3.3.2 to [v3.4.0](https://docs.projectcalico.org/v3.4/releases/) ([#362](https://github.com/poseidon/typhoon/pull/362))
  * Install CNI plugins with an init container rather than a sidecar
  * Improve the `calico-node` ClusterRole
* Recommend updating `terraform-provider-ct` plugin from v0.2.1 to v0.3.0 ([#363](https://github.com/poseidon/typhoon/pull/363))
  * [Migration](https://typhoon.psdn.io/topics/maintenance/#upgrade-terraform-provider-ct) instructions for upgrading `terraform-provider-ct` in-place for v1.12.2+ clusters (**action required**)
  * [Require](https://typhoon.psdn.io/topics/maintenance/#terraform-plugins-directory) switching from `~/.terraformrc` to the Terraform [third-party plugins](https://www.terraform.io/docs/configuration/providers.html#third-party-plugins) directory `~/.terraform.d/plugins/`
  * Require Container Linux 1688.5.3 or newer

#### Google Cloud

* Increase TCP proxy apiserver backend service timeout from 1 minute to 5 minutes ([#361](https://github.com/poseidon/typhoon/pull/361))
  * Align `port-forward` behavior closer to AWS/Azure (no timeout)

#### Addons

* Update Grafana from v5.4.0 to v5.4.2

## v1.13.0

* Kubernetes [v1.13.0](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.13.md#v1130)
* Update Calico from v3.3.1 to [v3.3.2](https://docs.projectcalico.org/v3.3/releases/)

#### Addons

* Update Grafana from v5.3.4 to v5.4.0
* Disable Grafana login form, since admin user can't be disabled ([#352](https://github.com/poseidon/typhoon/pull/352))
  * Example manifests aim to provide a read-only dashboard view

## v1.12.3

* Kubernetes [v1.12.3](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.12.md#v1123)
* Add `enable_reporting` variable (default "false") to provide upstreams with usage data ([#345](https://github.com/poseidon/typhoon/pull/345))
* Change kube-apiserver `--kubelet-preferred-address-types` to InternalIP,ExternalIP,Hostname
* Update Calico from v3.3.0 to [v3.3.1](https://docs.projectcalico.org/v3.3/releases/)
  * Disable Felix usage reporting by default ([#345](https://github.com/poseidon/typhoon/pull/345))
* Improve flannel manifests
  * [Rename](https://github.com/poseidon/terraform-render-bootkube/commit/d045a8e6b8eccfbb9d69bb51953b5a93d23f67f7) `kube-flannel` DaemonSet to `flannel` and `kube-flannel-cfg` ConfigMap to `flannel-config` 
  * [Drop](https://github.com/poseidon/terraform-render-bootkube/commit/39f9afb3360ec642e5b98457c8bd07eda35b6c96) unused mounts and add a CPU resource request
* Update CoreDNS from v1.2.4 to [v1.2.6](https://coredns.io/2018/11/05/coredns-1.2.6-release/)
  * Enable CoreDNS `loop` and `loadbalance` plugins ([#340](https://github.com/poseidon/typhoon/pull/340))
* Fix pod-checkpointer log noise and checkpointable pods detection ([#346](https://github.com/poseidon/typhoon/pull/346))
* Use kubernetes-incubator/bootkube v0.14.0
* [Recommend](https://typhoon.psdn.io/topics/maintenance/#terraform-plugins-directory) switching from `~/.terraformrc` to the Terraform [third-party plugins](https://www.terraform.io/docs/configuration/providers.html#third-party-plugins) directory `~/.terraform.d/plugins/`.
  * Allows pinning `terraform-provider-ct` and `terraform-provider-matchbox` versions
  * Improves safety of later plugin version migrations

#### Azure

* Use eviction policy `Delete` for `Low` priority virtual machine scale set workers ([#343](https://github.com/poseidon/typhoon/pull/343))
  * Fix issue where Azure defaults to `Deallocate` eviction policy, which required manually restarting deallocated instances. `Delete` policy aligns Azure with AWS and GCP behavior.
  * Require `terraform-provider-azurerm` v1.19+ (action required)

#### Bare-Metal

* Add Kubelet `/etc/iscsi` and `iscsadm` mounts on bare-metal for iSCSI ([#103](https://github.com/poseidon/typhoon/pull/103))

#### Addons

* Update nginx-ingress from v0.20.0 to v0.21.0
* Update Prometheus from v2.4.3 to v2.5.0
* Update Grafana from v5.3.2 to v5.3.4

## v1.12.2

* Kubernetes [v1.12.2](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.12.md#v1122)
* Update CoreDNS from 1.2.2 to [1.2.4](https://github.com/coredns/coredns/releases/tag/v1.2.4)
* Update Calico from v3.2.3 to [v3.3.0](https://docs.projectcalico.org/v3.3/releases/)
* Disable Kubelet read-only port ([#324](https://github.com/poseidon/typhoon/pull/324))
* Fix CoreDNS AntiAffinity spec to prefer spreading replicas
* Ignore controller node user-data changes ([#335](https://github.com/poseidon/typhoon/pull/335))
  * Once all managed clusters use v1.12.2, it is possible to update `terraform-provider-ct`

#### AWS

* Add `disk_iops` variable for EBS volume IOPS ([#314](https://github.com/poseidon/typhoon/pull/314))

#### Azure

* Use new `azurerm_network_interface_backend_address_pool_association` ([#332](https://github.com/poseidon/typhoon/pull/332))
  * Require `terraform-provider-azurerm` v1.17+ (action required)
* Add `primary` field to `ip_configuration` needed by v1.17+ ([#331](https://github.com/poseidon/typhoon/pull/331))

#### DigitalOcean

* Add AAAA DNS records resolving to worker nodes ([#333](https://github.com/poseidon/typhoon/pull/333))
  * Hosting IPv6 apps requires editing nginx-ingress with `hostNetwork: true`

#### Google Cloud

* Add an IPv6 address and IPv6 forwarding rules for load balancing IPv6 Ingress ([#334](https://github.com/poseidon/typhoon/pull/334))
  * Add `ingress_static_ipv6` output variable for use in AAAA DNS records
  * Allow serving IPv6 applications via Kubernetes Ingress

#### Addons

* Configure Heapster to scrape Kubelets with bearer token auth ([#323](https://github.com/poseidon/typhoon/pull/323))
* Update Grafana from v5.3.1 to v5.3.2

## v1.12.1

* Kubernetes [v1.12.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.12.md#v1121)
* Update etcd from v3.3.9 to [v3.3.10](https://github.com/etcd-io/etcd/blob/master/CHANGELOG-3.3.md#v3310-2018-10-10)
* Update CoreDNS from 1.1.3 to [1.2.2](https://github.com/coredns/coredns/releases/tag/v1.2.2)
* Update Calico from v3.2.1 to [v3.2.3](https://docs.projectcalico.org/v3.2/releases/)
* Raise scheduler and controller-manager replicas to the larger of 2 or the number of controller nodes ([#312](https://github.com/poseidon/typhoon/pull/312))
  * Single-controller clusters continue to run 2 replicas as before
* Raise default CoreDNS replicas to the larger of 2 or the number of controller nodes ([#313](https://github.com/poseidon/typhoon/pull/313))
  * Add AntiAffinity preferred rule to favor spreading CoreDNS pods
* Annotate control plane and addon containers to use the Docker runtime seccomp profile ([#319](https://github.com/poseidon/typhoon/pull/319))
  * Override Kubernetes default behavior that starts containers with `seccomp=unconfined`

#### Azure

* Remove `admin_password` field (disabled) since it is now optional
  * Require `terraform-provider-azurerm` v1.16+ (action required)

#### Bare-Metal

* Add support for `cached_install` mode with Flatcar Linux ([#315](https://github.com/poseidon/typhoon/pull/315))

#### DigitalOcean

* Require `terraform-provider-digitalocean` v1.0+ (action required)

#### Addons

* Update nginx-ingress from v0.19.0 to v0.20.0
* Update Prometheus from v2.3.2 to v2.4.3
* Update Grafana from v5.2.4 to v5.3.1

## v1.11.3

* Kubernetes [v1.11.3](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.11.md#v1113)
* Introduce Typhoon for Azure as alpha ([#288](https://github.com/poseidon/typhoon/pull/288))
  * Special thanks @justaugustus for an earlier variant
* Update Calico from v3.1.3 to v3.2.1 ([#278](https://github.com/poseidon/typhoon/pull/278))

#### AWS

* Remove firewall rule allowing ICMP packets to nodes ([#285](https://github.com/poseidon/typhoon/pull/285))

#### Bare-Metal

* Remove `controller_networkds` and `worker_networkds` variables. Use Container Linux Config snippets [#277](https://github.com/poseidon/typhoon/pull/277)

#### Google Cloud

* Fix firewall to allow etcd client port 2379 traffic between controller nodes ([#287](https://github.com/poseidon/typhoon/pull/287))
  * kube-apiservers were only able to connect to their node's local etcd peer. While master node outages were tolerated, reaching a healthy peer took longer than neccessary in some cases
  * Reduce time needed to bootstrap the cluster
* Remove firewall rule allowing workers to access Nginx Ingress health check ([#284](https://github.com/poseidon/typhoon/pull/284))
  * Nginx Ingress addon no longer uses hostNetwork, Prometheus scrapes via CNI network

#### Addons

* Update nginx-ingress from 0.17.1 to 0.19.0
* Update kube-state-metrics from v1.3.1 to v1.4.0
* Update Grafana from 5.2.2 to 5.2.4

## v1.11.2

* Kubernetes [v1.11.2](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.11.md#v1112)
* Update etcd from v3.3.8 to [v3.3.9](https://github.com/coreos/etcd/blob/master/CHANGELOG-3.3.md#v339-2018-07-24)
* Use kubernetes-incubator/bootkube v0.13.0
* Fix Fedora Atomic modules' Kubelet version ([#270](https://github.com/poseidon/typhoon/issues/270))

#### Bare-Metal

* Introduce [Container Linux Config snippets](https://typhoon.psdn.io/advanced/customization/#container-linux) on bare-metal
  * Validate and additively merge custom Container Linux Configs during terraform plan
  * Define files, systemd units, dropins, networkd configs, mounts, users, and more
  * [Require](https://typhoon.psdn.io/cl/bare-metal/#terraform-setup) `terraform-provider-ct` plugin v0.2.1 (**action required!**)

#### Addons

* Update nginx-ingress from 0.16.2 to 0.17.1
* Add nginx-ingress manifests for bare-metal
* Update Grafana from 5.2.1 to 5.2.2
* Update heapster from v1.5.3 to v1.5.4

## v1.11.1

* Kubernetes [v1.11.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.11.md#v1111)

#### Addons

* Update Prometheus from v2.3.1 to v2.3.2

#### Errata

* Fedora Atomic modules shipped with Kubelet v1.11.0, instead of v1.11.1. Fixed in [#270](https://github.com/poseidon/typhoon/issues/270).

## v1.11.0

* Kubernetes [v1.11.0](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.11.md#v1110)
* Force apiserver to stop listening on `127.0.0.1:8080`
* Replace `kube-dns` with [CoreDNS](https://coredns.io/) ([#261](https://github.com/poseidon/typhoon/pull/261))
  * Edit the `coredns` ConfigMap to [customize](https://coredns.io/plugins/)
  * CoreDNS doesn't use a resizer. For large clusters, scaling may be required. 

#### AWS

* Update from Fedora Atomic 27 to 28 ([#258](https://github.com/poseidon/typhoon/pull/258))

#### Bare-Metal

* Update from Fedora Atomic 27 to 28 ([#263](https://github.com/poseidon/typhoon/pull/263))

#### Google

* Promote Google Cloud to stable
* Update from Fedora Atomic 27 to 28 ([#259](https://github.com/poseidon/typhoon/pull/259))
* Remove `ingress_static_ip` module output. Use `ingress_static_ipv4`.
* Remove `controllers_ipv4_public` module output.

#### Addons

* Update nginx-ingress from 0.15.0 to 0.16.2
* Update Grafana from 5.1.4 to [5.2.1](http://docs.grafana.org/guides/whats-new-in-v5-2/)
* Update heapster from v1.5.2 to v1.5.3

## v1.10.5

* Kubernetes [v1.10.5](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.10.md#v1105)
* Update etcd from v3.3.6 to v3.3.8 ([#243](https://github.com/poseidon/typhoon/pull/243), [#247](https://github.com/poseidon/typhoon/pull/247))

#### AWS

* Switch `kube-apiserver` port from 443 to 6443 ([#248](https://github.com/poseidon/typhoon/pull/248))
* Combine apiserver and ingress NLBs ([#249](https://github.com/poseidon/typhoon/pull/249))
  * Reduce cost by ~$18/month per cluster. Typhoon AWS clusters now use one network load balancer.
  * Ingress addon users may keep using CNAME records to the `ingress_dns_name` module output (few million RPS)
  * Ingress users with heavy traffic (many million RPS) should create a separate NLB(s)
* Worker pools no longer include an extraneous load balancer. Remove worker module's `ingress_dns_name` output
* Disable detailed (paid) monitoring on worker nodes ([#251](https://github.com/poseidon/typhoon/pull/251))
  * Favor Prometheus for cloud-agnostic metrics, aggregation, and alerting
* Add `worker_target_group_http` and `worker_target_group_https` module outputs to allow custom load balancing
* Add `target_group_http` and `target_group_https` worker module outputs to allow custom load balancing

#### Bare-Metal

* Switch `kube-apiserver` port from 443 to 6443 ([#248](https://github.com/poseidon/typhoon/pull/248))
  * Users who exposed kube-apiserver on a WAN via their router/load-balancer will need to adjust its configuration (e.g. DNAT 6443). Most apiservers are on a LAN (internal, VPN-only, etc) so if you didn't specially configure network gear for 443, no change is needed. (possible action required)
* Fix possible deadlock when provisioning clusters larger than 10 nodes ([#244](https://github.com/poseidon/typhoon/pull/244)) 

#### DigitalOcean

* Switch `kube-apiserver` port from 443 to 6443 ([#248](https://github.com/poseidon/typhoon/pull/248))
  * Update firewall rules and generated kubeconfig's

#### Google Cloud

* Use global HTTP and TCP proxy load balancing for Kubernetes Ingress ([#252](https://github.com/poseidon/typhoon/pull/252))
  * Switch Ingress from regional network load balancers to global HTTP/TCP Proxy load balancing
  * Reduce cost by ~$19/month per cluster. Google bills the first 5 global and regional forwarding rules separately. Typhoon clusters now use 3 global and 0 regional forwarding rules.
* Worker pools no longer include an extraneous load balancer. Remove worker module's `ingress_static_ip` output
* Allow using nginx-ingress addon on Fedora Atomic clusters ([#200](https://github.com/poseidon/typhoon/issues/200))
* Add `worker_instance_group` module output to allow custom global load balancing
* Add `instance_group` worker module output to allow custom global load balancing
* Deprecate `ingress_static_ip` module output. Add `ingress_static_ipv4` module output instead.
* Deprecate `controllers_ipv4_public` module output

#### Addons

* Update CLUO from v0.6.0 to v0.7.0 ([#242](https://github.com/poseidon/typhoon/pull/242))
* Update Prometheus from v2.3.0 to v2.3.1
* Update Grafana from 5.1.3 to 5.1.4
* Drop `hostNetwork` from nginx-ingress addon
  * Both flannel and Calico support host port via `portmap`
  * Allows writing NetworkPolicies that reference ingress pods in `from` or `to`. HostNetwork pods were difficult to write network policy for since they could circumvent the CNI network to communicate with pods on the same node.

## v1.10.4

* Kubernetes [v1.10.4](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.10.md#v1104)
* Update etcd from v3.3.5 to v3.3.6
* Update Calico from v3.1.2 to v3.1.3

#### Addons

* Update Prometheus from v2.2.1 to v2.3.0
* Add Prometheus liveness and readiness probes
* Annotate Grafana service so Prometheus scrapes metrics
* Label namespaces to ease writing Network Policies

## v1.10.3

* Kubernetes [v1.10.3](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.10.md#v1103)
* Add [Flatcar Linux](https://docs.flatcar-linux.org/) (Container Linux derivative) as an option for AWS and bare-metal (thanks @kinvolk folks)
* Allow bearer token authentication to the Kubelet ([#216](https://github.com/poseidon/typhoon/issues/216))
  * Require Webhook authorization to the Kubelet
  * Switch apiserver X509 client cert org to satisfy new authorization requirement
* Require Terraform v0.11.x and drop support for v0.10.x ([migration guide](https://typhoon.psdn.io/topics/maintenance/#terraform-v011x))
* Update etcd from v3.3.4 to v3.3.5 ([#213](https://github.com/poseidon/typhoon/pull/213))
* Update Calico from v3.1.1 to v3.1.2

#### AWS

* Allow Flatcar Linux by setting `os_image` to flatcar-stable (default), flatcar-beta, flatcar-alpha ([#211](https://github.com/poseidon/typhoon/pull/211))
* Replace `os_channel` variable with `os_image` to align naming across clouds
  * Please change values stable, beta, or alpha to coreos-stable, coreos-beta, coreos-alpha (**action required!**)
* Allow preemptible workers via spot instances ([#202](https://github.com/poseidon/typhoon/pull/202))
  * Add `worker_price` to allow worker spot instances. Default to empty string for the worker autoscaling group to use regular on-demand instances
  * Add `spot_price` to internal `workers` module for spot [worker pools](https://typhoon.psdn.io/advanced/worker-pools/)

#### Bare-Metal

* Allow Flatcar Linux by setting `os_channel` to flatcar-stable, flatcar-beta, flatcar-alpha ([#220](https://github.com/poseidon/typhoon/pull/220))
* Replace `container_linux_channel` variable with `os_channel`
  * Please change values stable, beta, or alpha to coreos-stable, coreos-beta, coreos-alpha (**action required!**)
* Replace `container_linux_version` variable with `os_version`
* Add `network_ip_autodetection_method` variable for Calico host IPv4 address detection
  * Use Calico's default "first-found" to support single NIC and bonded NIC nodes 
  * Allow [alternative](https://docs.projectcalico.org/v3.1/reference/node/configuration#ip-autodetection-methods) methods for multi NIC nodes, like can-reach=IP or interface=REGEX
* Deprecate `container_linux_oem` variable

#### DigitalOcean

* Update Fedora Atomic module to use Fedora Atomic 28 ([#225](https://github.com/poseidon/typhoon/pull/225))
  * Fedora Atomic 27 images disappeared from DigitalOcean and forced this early update

#### Addons

* Fix Prometheus data directory location ([#203](https://github.com/poseidon/typhoon/pull/203))
* Configure Prometheus to scrape Kubelets directly with bearer token auth instead of proxying through the apiserver ([#217](https://github.com/poseidon/typhoon/pull/217))
  * Security improvement: Drop RBAC permission from `nodes/proxy` to `nodes/metrics`
  * Scale: Remove per-node proxied scrape load from the apiserver
* Update Grafana from v5.04 to v5.1.3 ([#208](https://github.com/poseidon/typhoon/pull/208))
  * Disable Grafana Google Analytics by default ([#214](https://github.com/poseidon/typhoon/issues/214))
* Update nginx-ingress from 0.14.0 to 0.15.0
* Annotate nginx-ingress service so Prometheus auto-discovers and scrapes service endpoints ([#222](https://github.com/poseidon/typhoon/pull/222))

## v1.10.2

* Kubernetes [v1.10.2](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.10.md#v1102)
* [Introduce](https://typhoon.psdn.io/announce/#april-26-2018) Typhoon for Fedora Atomic ([#199](https://github.com/poseidon/typhoon/pull/199))
* Update Calico from v3.0.4 to v3.1.1 ([#197](https://github.com/poseidon/typhoon/pull/197))
  * https://www.projectcalico.org/announcing-calico-v3-1/
  * https://github.com/projectcalico/calico/releases/tag/v3.1.0
* Update etcd from v3.3.3 to v3.3.4
* Update kube-dns from v1.14.9 to v1.14.10

#### Google Cloud

* Add support for multi-controller clusters (i.e. multi-master) ([#54](https://github.com/poseidon/typhoon/issues/54), [#190](https://github.com/poseidon/typhoon/pull/190))
  * Switch from Google Cloud network load balancer to a TCP proxy load balancer. Avoid a [bug](https://issuetracker.google.com/issues/67366622) in Google network load balancers that limited clusters to only bootstrapping one controller node. 
  * Add TCP health check for apiserver pods on controllers. Replace kubelet check approximation.

#### Addons

* Update nginx-ingress from 0.12.0 to 0.14.0
* Update kube-state-metrics from v1.3.0 to v1.3.1

## v1.10.1

* Kubernetes [v1.10.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.10.md#v1101)
* Enable etcd v3.3 metrics endpoint ([#175](https://github.com/poseidon/typhoon/pull/175))
* Use `k8s.gcr.io` instead of `gcr.io/google_containers` ([#180](https://github.com/poseidon/typhoon/pull/180))
  * Kubernetes [recommends](https://groups.google.com/forum/#!msg/kubernetes-dev/ytjk_rNrTa0/3EFUHvovCAAJ) using the alias to pull from the nearest regional mirror and to abstract the backing container registry
* Update etcd from v3.3.2 to v3.3.3
* Update kube-dns from v1.14.8 to v1.14.9
* Use kubernetes-incubator/bootkube v0.12.0

#### Bare-Metal

* Fix need for multiple `terraform apply` runs to create a cluster with Terraform v0.11.4 ([#181](https://github.com/poseidon/typhoon/pull/181))
  * To SSH during a disk install for debugging, SSH as user "core" with port 2222
  * Remove the old trick of using a user "debug" during disk install

#### Google Cloud

* Refactor out the `controller` internal module

#### Addons

* Add Prometheus discovery for etcd peers on controller nodes ([#175](https://github.com/poseidon/typhoon/pull/175))
  * Scrape etcd v3.3 `--listen-metrics-urls` for metrics
  * Enable etcd alerts and populate the etcd Grafana dashboard
* Update kube-state-metrics from v1.2.0 to v1.3.0

## v1.10.0

* Kubernetes [v1.10.0](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.10.md#v1100)
* Remove unused, unmaintained `pxe-worker` internal module

#### AWS

* Add `disk_type` optional variable for setting the EBS volume type ([#176](https://github.com/poseidon/typhoon/pull/176))
  * Change default type from `standard` to `gp2`. Prometheus etcd alerts are tuned for fast disks.

#### Digital Ocean

* Ensure etcd secrets are only distributed to controller hosts, not workers.
* Remove `networking` optional variable. Only flannel works on Digital Ocean.

#### Google Cloud

* Add `disk_size` optional variable for setting instance disk size in GB
* Add `controller_type` optional variable for setting machine type for controllers
* Add `worker_type` optional variable for setting machine type for workers
* Remove `machine_type` optional variable. Use `controller_type` and `worker_type`.

#### Addons

* Update Grafana from v4.6.3 to v5.0.4 ([#153](https://github.com/poseidon/typhoon/pull/153), [#174](https://github.com/poseidon/typhoon/pull/174))
  * Restrict dashboard organization role to Viewer

## v1.9.6

* Kubernetes [v1.9.6](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.9.md#v196)
* Update Calico from v3.0.3 to v3.0.4

#### Addons

* Update heapster from v1.5.1 to v1.5.2

## v1.9.5

* Kubernetes [v1.9.5](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.9.md#v195)
  * Fix `subPath` volume mounts regression ([kubernetes#61076](https://github.com/kubernetes/kubernetes/issues/61076))
* Introduce [Container Linux Config snippets](https://typhoon.psdn.io/advanced/customization/#container-linux) on cloud platforms ([#145](https://github.com/poseidon/typhoon/pull/145))
  * Validate and additively merge custom Container Linux Configs during `terraform plan`
  * Define files, systemd units, dropins, networkd configs, mounts, users, and more
  * Require updating `terraform-provider-ct` plugin from v0.2.0 to v0.2.1
* Add `node-role.kubernetes.io/controller="true"` node label to controllers ([#160](https://github.com/poseidon/typhoon/pull/160))

#### AWS

* [Require](https://typhoon.psdn.io/topics/maintenance/#terraform-provider-ct-v021) updating `terraform-provider-ct` plugin from v0.2.0 to [v0.2.1](https://github.com/coreos/terraform-provider-ct/releases/tag/v0.2.1) (action required!)

#### Digital Ocean

* [Require](https://typhoon.psdn.io/topics/maintenance/#terraform-provider-ct-v021) updating `terraform-provider-ct` plugin from v0.2.0 to [v0.2.1](https://github.com/coreos/terraform-provider-ct/releases/tag/v0.2.1) (action required!)

#### Google Cloud

* [Require](https://typhoon.psdn.io/topics/maintenance/#terraform-provider-ct-v021) updating `terraform-provider-ct` plugin from v0.2.0 to [v0.2.1](https://github.com/coreos/terraform-provider-ct/releases/tag/v0.2.1) (action required!)
* Relax `os_image` to optional. Default to "coreos-stable".

#### Addons

* Update nginx-ingress from 0.11.0 to 0.12.0
* Update Prometheus from 2.2.0 to 2.2.1

## v1.9.4

* Kubernetes [v1.9.4](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.9.md#v194)
  * Secret, configMap, downward API, and projected volumes now read-only (breaking, [kubernetes#58720](https://github.com/kubernetes/kubernetes/pull/58720))
  * Regressed `subPath` volume mounts (regression, [kubernetes#61076](https://github.com/kubernetes/kubernetes/issues/61076))
  * Mitigated `subPath` [CVE-2017-1002101](https://github.com/kubernetes/kubernetes/issues/60813)
* Introduce [worker pools](https://typhoon.psdn.io/advanced/worker-pools/) for AWS and Google Cloud for joining heterogeneous workers to existing clusters.
* Use new Network Load Balancers and cross zone load balancing on AWS
* Allow flexvolume plugins to be used on any Typhoon cluster (not just bare-metal)
* Upgrade etcd from v3.2.15 to v3.3.2
* Update Calico from v3.0.2 to v3.0.3
* Use kubernetes-incubator/bootkube v0.11.0
* [Recommend](https://typhoon.psdn.io/topics/maintenance/#terraform-provider-ct-v021) updating `terraform-provider-ct` plugin from v0.2.0 to [v0.2.1](https://github.com/coreos/terraform-provider-ct/releases/tag/v0.2.1) (action recommended)

#### AWS

* Promote AWS platform to stable
* Allow groups of workers to be defined and joined to a cluster (i.e. worker pools) ([#150](https://github.com/poseidon/typhoon/pull/150))
* Replace the apiserver elastic load balancer with a network load balancer ([#136](https://github.com/poseidon/typhoon/pull/136))
* Replace the Ingress elastic load balancer with a network load balancer ([#141](https://github.com/poseidon/typhoon/pull/141))
  * AWS [NLBs](https://aws.amazon.com/blogs/aws/new-network-load-balancer-effortless-scaling-to-millions-of-requests-per-second/) can handle millions of RPS with high throughput and low latency.
  * Require `terraform-provider-aws` 1.7.0 or higher
* Enable NLB [cross-zone](https://aws.amazon.com/about-aws/whats-new/2018/02/network-load-balancer-now-supports-cross-zone-load-balancing/) load balancing ([#159](https://github.com/poseidon/typhoon/pull/159))
  * Requests are automatically evenly distributed to targets regardless of AZ
  * Require `terraform-provider-aws` 1.11.0 or higher
* Add kubelet `--volume-plugin-dir` flag to allow flexvolume plugins ([#142](https://github.com/poseidon/typhoon/pull/142))
* Fix controller and worker launch configs to ignore AMI changes ([#126](https://github.com/poseidon/typhoon/pull/126), [#158](https://github.com/poseidon/typhoon/pull/158))

#### Digital Ocean

* Add kubelet `--volume-plugin-dir` flag to allow flexvolume plugins ([#142](https://github.com/poseidon/typhoon/pull/142))
* Fix to pass `ssh_fingerprints` as a list to droplets ([#143](https://github.com/poseidon/typhoon/pull/143))

#### Google Cloud

* Allow groups of workers to be defined and joined to a cluster (i.e. worker pools) ([#148](https://github.com/poseidon/typhoon/pull/148))
* Add kubelet `--volume-plugin-dir` flag to allow flexvolume plugins ([#142](https://github.com/poseidon/typhoon/pull/142))
* Add `kubeconfig` variable to `controllers` and `workers` submodules ([#147](https://github.com/poseidon/typhoon/pull/147))
* Remove `kubeconfig_*` variables from `controllers` and `workers` submodules ([#147](https://github.com/poseidon/typhoon/pull/147))
* Allow initial experimentation with accelerators (i.e. GPUs) on workers ([#161](https://github.com/poseidon/typhoon/pull/161)) (unofficial)
  * Require `terraform-provider-google` v1.6.0

#### Addons

* Update Prometheus from 2.1.0 to 2.2.0 ([#153](https://github.com/poseidon/typhoon/pull/153))
  * Scrape Prometheus itself to enable alerts about Prometheus itself
  * Adjust KubeletDown rule to fire when 10% of kubelets are down
* Update heapster from v1.5.0 to v1.5.1 ([#131](https://github.com/poseidon/typhoon/pull/131))
  * Use separate service account
* Update nginx-ingress from 0.10.2 to 0.11.0

## v1.9.3

* Kubernetes [v1.9.3](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.9.md#v193)
* Network improvements and fixes ([#104](https://github.com/poseidon/typhoon/pull/104))
  * Switch from Calico v2.6.6 to v3.0.2
  * Add Calico GlobalNetworkSet CRD
  * Update flannel from v0.9.0 to v0.10.0
  * Use separate service account for flannel
* Update etcd from v3.2.14 to v3.2.15

#### Digital Ocean

* Use new Droplet [types](https://developers.digitalocean.com/documentation/changelog/api-v2/new-size-slugs-for-droplet-plan-changes/) which offer more CPU/memory, at lower cost. ([#105](https://github.com/poseidon/typhoon/pull/105))
  * A small Digital Ocean cluster costs less than $25 a month!

#### Addons

* Update Prometheus from v2.0.0 to v2.1.0 ([#113](https://github.com/poseidon/typhoon/pull/113))
  * Improve alerting rules
  * Relabel discovered kubelet, endpoint, service, and apiserver scrapes
  * Use separate service accounts
  * Update node-exporter and kube-state-metrics
* Include Grafana dashboards for Kubernetes admins ([#113](https://github.com/poseidon/typhoon/pull/113))
  * Add grafana-watcher to load bundled upstream dashboards
* Update nginx-ingress from 0.9.0 to 0.10.2
* Update CLUO from v0.5.0 to v0.6.0
* Switch manifests to use `apps/v1` Deployments and Daemonsets ([#120](https://github.com/poseidon/typhoon/pull/120))
* Remove Kubernetes Dashboard manifests ([#121](https://github.com/poseidon/typhoon/pull/121))

## v1.9.2

* Kubernetes [v1.9.2](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.9.md#v192)
* Add Terraform v0.11.x support
  * Add explicit "providers" section to modules for Terraform v0.11.x
  * Retain support for Terraform v0.10.4+
* Add [migration guide](https://typhoon.psdn.io/topics/maintenance/#terraform-v011x) from Terraform v0.10.x to v0.11.x (**action required!**)
* Update etcd from 3.2.13 to 3.2.14
* Update calico from 2.6.5 to 2.6.6
* Update kube-dns from v1.14.7 to v1.14.8
* Use separate service account for kube-dns
* Use kubernetes-incubator/bootkube v0.10.0

#### Bare-Metal

* Use per-node Container Linux install profiles ([#97](https://github.com/poseidon/typhoon/pull/97))
  * Allow Container Linux channel/version to be chosen per-cluster
  * Fix issue where cluster deletion could require `terraform apply` multiple times

#### Digital Ocean

* Relax `digitalocean` provider version constraint
* Fix bug with `terraform plan` always showing a firewall diff to be applied ([#3](https://github.com/poseidon/typhoon/issues/3))

#### Addons

* Update CLUO to v0.5.0 to fix compatibility with Kubernetes 1.9 (**important**)
  * Earlier versions can't roll out Container Linux updates on Kubernetes 1.9 nodes ([cluo#163](https://github.com/coreos/container-linux-update-operator/issues/163))
* Update kube-state-metrics from v1.1.0 to v1.2.0
* Fix RBAC cluster role for kube-state-metrics

## v1.9.1

* Kubernetes [v1.9.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.9.md#v191)
* Update kube-dns from 1.14.5 to v1.14.7
* Update etcd from 3.2.0 to 3.2.13
* Update Calico from v2.6.4 to v2.6.5
* Enable portmap to fix hostPort with Calico
* Use separate service account for controller-manager

## v1.8.6

* Kubernetes [v1.8.6](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.8.md#v186)
* Update Calico from v2.6.3 to v2.6.4

## v1.8.5

* Kubernetes [v1.8.5](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.8.md#v185)
* Recommend Container Linux [images](https://coreos.com/releases/) with Docker 17.09
  * Container Linux stable, beta, and alpha now provide Docker 17.09 (instead
  of 1.12)
  * Older clusters (with CLUO addon) auto-update Container Linux version to begin using Docker 17.09
* Fix race where `etcd-member.service` could fail to resolve peers ([#69](https://github.com/poseidon/typhoon/pull/69)) 
* Add optional `cluster_domain_suffix` variable (#74)
* Use kubernetes-incubator/bootkube v0.9.1

#### Bare-Metal

* Add kubelet `--volume-plugin-dir` flag to allow flexvolume providers ([#61](https://github.com/poseidon/typhoon/pull/61))

#### Addons

* Discourage deploying the Kubernetes Dashboard (security)

## v1.8.4

* Kubernetes v1.8.4
* Calico related bug fixes
* Update Calico from v2.6.1 to v2.6.3
* Update flannel from v0.9.0 to v0.9.1
* Service accounts for kube-proxy and pod-checkpointer
* Use kubernetes-incubator/bootkube v0.9.0

## v1.8.3

* Kubernetes v1.8.3
* Run etcd on-host, across controllers
* Promote AWS platform to beta
* Use kubernetes-incubator/bootkube v0.8.2

#### Google Cloud

* Add required variable `region` (e.g. "us-central1")
* Reduce time to bootstrap a cluster
* Change etcd to run on-host, across controllers (etcd-member.service)
* Change controller instances to automatically span zones in the region
* Change worker managed instance group to automatically span zones in the region
* Improve internal firewall rules and use tag-based firewall policies
* Remove support for self-hosted etcd
* Remove the `zone` required variable
* Remove the `controller_preemptible` optional variable

#### AWS

* Promote AWS platform to beta
* Reduce time to bootstrap a cluster
* Change etcd to run on-host, across controllers (etcd-member.service)
* Fix firewall rules for multi-controller kubelet scraping and node-exporter
* Remove support for self-hosted etcd

#### Addons

* Add Prometheus 2.0 addon with alerting rules
* Add Grafana dashboard for observing metrics

## v1.8.2

* Kubernetes v1.8.2
  * Fixes a memory leak in the v1.8.1 apiserver ([kubernetes#53485](https://github.com/kubernetes/kubernetes/issues/53485))
* Switch to using the `gcr.io/google_containers/hyperkube`
* Update flannel from v0.8.0 to v0.9.0
* Add `hairpinMode` to flannel CNI config
* Add `--no-negcache` to kube-dns dnsmasq
* Use kubernetes-incubator/bootkube v0.8.1

## v1.8.1

* Kubernetes v1.8.1
* Use kubernetes-incubator/bootkube v0.8.0

#### Digital Ocean

* Run etcd cluster across controller nodes (etcd-member.service)
* Remove support for self-hosted etcd
* Reduce time to bootstrap a cluster

## v1.7.7

* Kubernetes v1.7.7
* Use kubernetes-incubator/bootkube v0.7.0
* Update kube-dns to 1.14.5 to fix dnsmasq [vulnerability](https://security.googleblog.com/2017/10/behind-masq-yet-more-dns-and-dhcp.html)
* Calico v2.6.1
* flannel-cni v0.3.0
  * Update flannel CNI config to fix hostPort

## v1.7.5

* Kubernetes v1.7.5
* Use kubernetes-incubator/bootkube v0.6.2
* Add AWS Terraform module (alpha)
* Add support for Calico networking (bare-metal, Google Cloud, AWS)
* Change networking default from "flannel" to "calico"

#### AWS

* Add `network_mtu` to allow CNI interface MTU customization

#### Bare-Metal

* Add `network_mtu` to allow CNI interface MTU customization
* Remove support for `experimental_self_hosted_etcd`

## v1.7.3

* Kubernetes v1.7.3
* Use kubernetes-incubator/bootkube v0.6.1

#### Digital Ocean

* Add cloud firewall rules (requires Terraform v0.10)
* Change nodes tags from strings to DO tags

## v1.7.1

* Kubernetes v1.7.1
* Use kubernetes-incubator/bootkube v0.6.0
* Add Bare-Metal Terraform module (stable)
* Add Digital Ocean Terraform module (beta)

#### Google Cloud

* Remove `k8s_domain_name` variable, `cluster_name` + `dns_zone` resolves to controllers
* Rename `dns_base_zone` to `dns_zone`
* Rename `dns_base_zone_name` to `dns_zone_name`

## v1.6.7

* Kubernetes v1.6.7
* Use kubernetes-incubator/bootkube v0.5.1

## v1.6.6

* Kubernetes v1.6.6
* Use kubernetes-incubator/bootkube v0.4.5
* Disable locksmithd on hosts, in favor of [CLUO](https://github.com/coreos/container-linux-update-operator).

## v1.6.4

* Kubernetes v1.6.4
* Add Google Cloud Terraform module (stable)

## Earlier

Earlier versions, back to v1.3.0, used different designs and mechanisms.
