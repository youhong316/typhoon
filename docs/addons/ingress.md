# Ingress Controller

!!! bug "In Progress"
    These docs are incomplete.

Nginx Ingress controller pods can accept HTTP/HTTPS and TCP/UDP traffic and demultiplex traffic to backend services. Ingress controllers watch the Kubernetes API for Ingress resources and update configuration accordingly. Ingress resources for HTTP(S) applications supports virtual hosts (FQDNs), path rules, TLS termination, and SNI.

On Google Cloud and Digital Ocean, Ingress controllers bind worker host ports 80 and 443. A Google Cloud network load balancer can be assigned a static IP and can load balance ports to different node instances (i.e. backends).

## Google Cloud

Create the Ingress controller deployment, service, RBAC roles, RBAC bindings, default backend, and namespace.

```
kubectl apply -R addons/nginx-ingress/google-cloud
```

A Google Cloud network load balancer distributes traffic across worker nodes (i.e. a target pool of backends) running an Ingress controller. Firewall rules allow traffic to host ports 80 and 443. Health check rules ensure the target pool only includes worker nodes with a healthy Nginx Ingress pod.

The network load balancer is addressed with a static IPv4 address.

```
gcloud compute addresses list
```

For each application that should resolve to the cluster's ingress controller, add a DNS record resolving to the load balancer.

```
app1.example.com -> 11.22.33.44
aap2.example.com -> 11.22.33.44
app3.example.com -> 11.22.33.44
```

Depending on your DNS service of choice, you may be able to manage these records with Terraform. The Typhoon module helpfully outputs an `ingress_static_ip` variable.

```tf
resource "google_dns_record_set" "some-application" {
  # DNS zone name
  managed_zone = "example-zone"

  # DNS record
  name    = "app.example.com."
  type    = "A"
  ttl     = 300
  rrdatas = ["${module.google-cloud-yavin.ingress_static_ip}"]
}
```

## Digital Ocean

Create the Ingress controller deployment, service, RBAC roles, RBAC bindings, default backend, and namespace.

```
kubectl apply -R addons/nginx-ingress/digital-ocean
```

Depending on your DNS service of choice, you may be able to manage these records with Terraform. Each Digital Ocean cluster outputs a `workers_dns` variable. Create a CNAME record.

```tf
resource "google_dns_record_set" "some-application" {
  # DNS zone name
  managed_zone = "example-zone"

  # DNS record
  name    = "app.example.com."
  type    = "CNAME"
  ttl     = 300
  rrdatas = ["${module.digital-ocean-nemo.workers_dns}."]
}
```
