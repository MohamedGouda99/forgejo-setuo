# 🛠️ Forgejo Setup on Kubernetes 🐳
## 🌟 Objective
The goal is to set up Forgejo 🛠️ (a lightweight Git server) along with runners 🚀 in a Kubernetes environment 🐳. The entire setup will be secured using Tailscale VPN 🔒 to ensure that it operates in a private network 🌐. Here's an outline of the approach:
1.	Forgejo Deployment:
   o	Deploy Forgejo on Kubernetes using manifests or Helm charts 🗂️.
   o	Configure persistent storage 📦 to retain repositories and metadata even during pod restarts.
   o	Expose the Forgejo service internally 🛡️, ensuring it is accessible only within the private Tailscale VPN network.
2.	Runner Configuration:
   o	Deploy Forgejo runners as separate Kubernetes pods 🏗️.
   o	Use Kubernetes ConfigMaps 📃 and Secrets 🔑 to store Forgejo server details and authentication tokens securely.
   o	Set up autoscaling 🚦 for the runners to manage workloads dynamically.
3.	Tailscale VPN Integration:
   o	Install and configure Tailscale on the Kubernetes nodes 🌉 to establish a private VPN network.
   o	Ensure the Forgejo service and runners communicate over the Tailscale private network, making the infrastructure invisible to the public internet 🌍.
   o	Use Tailscale ACLs (Access Control Lists) 📜 to manage access to the Forgejo server and runners.
4.	Security and Networking:
   o	Restrict Forgejo access to authenticated users 🧑‍💻 and internal services via Tailscale.
   o	Use Kubernetes NetworkPolicies 🛡️ to isolate Forgejo and its runners from other workloads.
5.	Monitoring and Logging:
   o	Set up monitoring 📈 for Forgejo and the runners using tools like Prometheus or Grafana.
   o	Collect logs 📝 for debugging and performance analysis.

##
## 🌐 How to Set Up Tailscale as a Kubernetes Operator

#### ✅ Prerequisites

•  Configure Tailscale Kubernetes Operator with OAuth client credentials 🔑. These credentials allow the operator to manage devices via Tailscale API and create auth keys for itself and managed devices.

•  In your tailnet policy file, create the following tags:

   •	tag:k8s-operator (owner of tag:k8s).
   •	Additional tags if needed, ensuring tag:k8s-operator owns them.



```
"tagOwners": {
   "tag:k8s-operator": [],
   "tag:k8s": ["tag:k8s-operator"],
}
```
##
## 🚀 Installation
A default operator installation creates a tailscale namespace, an operator Deployment in the tailscale namespace, RBAC for the operator, and ProxyClass and Connector Custom Resource Definitions.

Helm
Tailscale Kubernetes Operator's Helm charts are available from two chart repositories.

The https://pkgs.tailscale.com/helmcharts repository contains well-tested charts for stable Tailscale versions.

Helm charts and container images for a new stable Tailscale version are released a few days after the official release. This is done to avoid releasing image versions with potential bugs in the core Linux client or core libraries.

The https://pkgs.tailscale.com/unstable/helmcharts repository contains charts with the very latest changes, published in between official releases.

The charts in both repositories are different versions of the same chart and you can upgrade from one to the other.

To install the latest Kubernetes Tailscale operator from https://pkgs.tailscale.com/helmcharts in tailscale namespace:

Add https://pkgs.tailscale.com/helmcharts to your local Helm repositories:
```
helm repo add tailscale https://pkgs.tailscale.com/helmcharts
helm repo update
helm upgrade \
  --install \
  tailscale-operator \
  tailscale/tailscale-operator \
  --namespace=tailscale \
  --create-namespace \
  --set-string oauth.clientId="<OAauth client ID>" \
  --set-string oauth.clientSecret="<OAuth client secret>" \
  --wait
```
##
## ⚙️ How to Deploy Forgejo with Helm

```
helm install forgejo ./forgejo-chart \
  --set runner.authKey="your-auth-key-here" \
  --set runner.token="your-token-here"
  -- set runner.forgejoInstanceUrl="Forgejo DNS Name"
  -- set forgejo.rootUrl="Forgejo DNS Name"
```
