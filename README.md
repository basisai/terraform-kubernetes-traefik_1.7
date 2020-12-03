# Traefik 1.7 on Kubernetes

This module provisions the deprecated Traefik 1.7 on Kubernetes using the no longer updated
stable chart.

Deploys a [Traefik](https://traefik.io/)
[Ingress Controller](https://docs.traefik.io/user-guide/kubernetes/) on a Kubernetes cluster.

The Kubernetes cluster can be managed
(i.e. [Google Kubernetes Engine (GKE)](https://cloud.google.com/kubernetes-engine/)) or not.

## Requirements

You will need to have the following resources available:

- A Kubernetes cluster, managed by GKE, or not
- [Helm](https://helm.sh/) with Tiller running on the Cluster or you can opt to run
    [Tiller locally](https://docs.helm.sh/using_helm/#running-tiller-locally)

You will need to have the following configured on your machine:

- Credentials for GCP
- Credentials for Kubernetes configured for `kubectl`

In addition, if you would like to run more than one replica of Traefik, you have to provision a
[Consul cluster](../consul) on your Kubernetes cluster. We recommend that you use the linked
Terraform module to do so.

If you would like to enable ACME certificate, you _must_ have a Consul cluster running. In addition,
you will have to configure additional variables for the ACME challenge to work.

### GKE RBAC

If you are using GKE and have configured `kuebctl` with credentials using
`gcloud container clusters get-credentials [CLUSTER_NAME]` only, your account in Kubernetes might
not have the necessary rights to deploy this Helm chart. You can
[give](https://cloud.google.com/kubernetes-engine/docs/how-to/role-based-access-control#prerequisites_for_using_role-based_access_control)
yourself the necessary rights by running

```bash
kubectl create clusterrolebinding cluster-admin-binding \
    --clusterrole cluster-admin --user [USER_ACCOUNT]
```

where `[USER_ACCOUNT]` is your email address.

## Provisioned Resources

This module deploys a Traefik Ingress controller to Kubernetes using a
[Helm Chart](https://github.com/helm/charts/tree/master/stable/traefik).

### KV Store

In addition, this module optionally supports using the [Consul](../consul) module to provide
[KV Store](https://docs.traefik.io/user-guide/kv-config) configuration for Traefik. This allows you
to define additional resources like additional entrypoints etc.

Some features require that you have a KV store. For example, to enable additional replicas AND
support the ACME protocol, you will need to have a KV store.

### Let's Encrypt Certificates

To support [Let's Encrypt](https://letsencrypt.org/) certificates using the ACME protocol, you
__must__ use the Consul KV store. See the previous section for more information.

For more information, read the guide on [Traefik Documentation](https://docs.traefik.io/configuration/acme/).
