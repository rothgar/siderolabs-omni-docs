---
description: Setting up a Talos Linux cluster with Omni.
---

# Getting Started with Omni

In this Getting Started guide we will create a high availability Kubernetes cluster with [Talos Linux](https://talos.dev) managed by Omni. This guide will use UTM/QEMU, but the same process will work with bare metal machines, cloud instances, and edge devices.

If you would like to watch a quick video of the process you can follow along here.

{% embed url="https://www.youtube.com/watch?v=0gPF0_fLins" %}

## Prerequisites

### Network access

If your machines have outgoing internet access, you are all set. Machines should have access to the Wireguard Endpoint shown on the Omni Home panel, which lists the IP address and port (e.g. 199.99.99.100:10001) that machines will connect to. Machines need to be able to reach that address both on the UDP port specified, and on TCP port 443.

### Create virtual or boot physical machines

The simplest way to experience Omni is to create some virtual machines. We suggest any virtualization platform that can boot off an ISO (UTM, ProxMox, VMware Fusion, etc) although any cloud platform can also be used with minor adjustments. Bare metal can boot from a physical CD, USB drive, virtual media, or PXE.

### `talosctl`

`talosctl` is the command line tool for managing Talos Linux via the management API, but when machines connect to Omni it is not required. Instead cluster management is done via the Omni UI or `omnictl`. We still recommend installing `talosctl` to investigate the state of the nodes and explore functionality.

Download `talosctl`, `kubectl`, `kubectl-oidc_login`, and `omnictl` (macOS and Linux):

```bash
brew install siderolabs/tap/sidero-tools
```

For manual and Windows installation please refer to the [alternate installation methods](https://www.talos.dev/latest/talos-guides/install/talosctl/#manual-and-windows-install) in the Talos documentation.

Make sure you download the talosconfig and omniconfig files from the Omni web interface and place them in the default locations `~/.talos/config` and `~/.config/omni/config/` respectively.

## Download Installation Media

Omni is a BYO Machine platform - all you need to do is boot your machines (virtual or physical) off an Omni image. To download the installation media, go to the Home Screen in Omni, and select "Download Installation Media" from the right hand side. Select the appropriate media and platform type.

Images exist for many platforms, but you will have to follow the specific installation instructions for that platform (which often involve copying the image to S3 type storage, creating a machine image from it, etc.)

## Boot machines off the downloaded image

Create at least 1 virtual machine with 2GB of memory (4GB or more is recommended) using your Hypervisor. Have each virtual machine boot off the ISO image you just downloaded.

After a few seconds, the machines should show in the Machines panel of Omni, with the `available` tag. They will also have tags showing their architecture, memory, cores and other information.

## Create Cluster

Click "Clusters" on the left navigation panel, then "Create Cluster" in the top right. You can give your cluster a name, select the version of Talos Linux to install, and the version of Kubernetes. You can also specify any patches that should be applied to your cluster, but in most cases these are not needed to get started.

In the section headed "Available Machines", select at least one machine to be the control plane, by clicking `CP`. You will want an odd number of control plane nodes (e.g. 1, 3, 5). Select one machine to be a worker, by clicking `W0` next to the machine.

Then click `Create Cluster`. Your cluster is now being created, and you will be taken to the Cluster Overview page. From this page you can download the `kubeconfig` and `talosconfig` files for your cluster, by clicking the buttons on the right hand side.

> Please note that because Omni manages the state of the Talos nodes, and protects the security of the Kubernetes and Talos credentials. Because of this, some `talosctl` commands (such as `talosctl reset`) will return `PermissionDenied` on Omni managed clusters - such operations must be done through the Omni UI or API calls.

### Access Kubernetes

You can query your Kubernetes cluster using normal Kubernetes operations:

```
kubectl --kubeconfig ./talos-default-kubeconfig.yaml get nodes
```

> Note: you will have to change the referenced kubeconfig file depending on the name of the cluster you created.

The first time you use the `kubectl` command to query a cluster, a browser window will open requiring you to authenticate with your identity provider (Google or GitHub most commonly.) If you get a message `error: unknown command "oidc-login" for "kubectl" Unable to connect to the server:` then you need to install the oidc-login plugin as noted above.

### Access Talos commands

You can explore Talos API commands. The first time you access the Talos API, a browser window will start to authenticate your request. The downloaded `talosconfig` file for the cluster includes the Omni endpoint, so you _do not need to specify endpoints_, just nodes.

```bash
talosctl --talosconfig ./talos-default-talosconfig.yaml --nodes 10.5.0.2 get members
```

> You will need to change the name of the `talosconfig` file, if you changed the cluster name from the default; and also use the actual IP or name of the nodes you created (which are shown in Omni) in place of the `node` IP.

> Note that because Omni manages the state of the Talos nodes, and protects the security of the Kubernetes and Talos credentials, some `talosctl` commands (such as `talosctl reset`) will return `PermissionDenied` on Omni managed clusters - such operations must be done through the Omni UI or API calls.

### Explore Omni

Now you have a complete cluster, with a high-availability Kubernetes API endpoint running on the Omni infrastructure, where all authentication is tied in to your enterprise identity provider. It's a good time to explore all that Omni can offer, including other areas of the UI such as:

- etcd backup and restores
- simple cluster upgrades of Kubernetes and Operating System
- proxying of workload HTTP access
- simple scaling up and down of clusters
- the concept of Machine Sets, that let you manage your infrastructure by classes

If you want to declaratively manage your clusters and infrastructure declaratively, as code, check out [Cluster Templates](../content/reference/cluster-templates/).

### Destroy the Cluster

When you are all done, you can remove the cluster by clicking "Destroy Cluster", in the bottom right of the Cluster Overview panel. This will wipe the machines and return them to the Available state.

## Cluster example

We have an example of a managed cluster complete with a monitoring stack and application management. It can be found in our [community contrib repo](https://github.com/siderolabs/contrib/blob/main/examples/omni).

### Components

The contrib example includes:

- [Cilium](https://cilium.io/get-started/) for cluster networking
- [Hubble](https://docs.cilium.io/en/stable/gettingstarted/hubble_intro/) for network observability
- [ArgoCD](https://argo-cd.readthedocs.io/en/stable/) for application management
- [Rook/Ceph](https://rook.io/docs/rook/latest-release/Getting-Started/intro/) for persistent storage
- [Prometheus](https://prometheus.io/docs/introduction/overview/) for metrics collection and alerting
- [Grafana](https://grafana.com/docs/grafana/latest/introduction/) for metrics visualization

### Use

You will need to copy the contents of the `omni` directory to a git repository that can be accessed by the cluster you create. Update the [ArgoCD ApplicationSet](https://github.com/siderolabs/contrib/blob/main/examples/omni/apps/argocd/argocd/bootstrap-app-set.yaml) template to reference your new git repo, and regenerate the ArgoCD bootstrap patch.

```bash
sed -i 's|https://github.com/siderolabs/contrib.git|<your-git-repo>|' apps/argocd/argocd/bootstrap-app-set.yaml
kustomize build apps/argocd/argocd | yq -i 'with(.cluster.inlineManifests.[] | select(.name=="argocd"); .contents=load_str("/dev/stdin"))' infra/patches/argocd.yaml
```

With these changes made you should commit the new values and push them to the git repo.

Next you should register your machines with Omni (see guides for [AWS](../content/how-to-guides/how-to-register-an-aws-ec2-instance/), [GCP](../content/how-to-guides/how-to-register-a-gcp-instance/), [Azure](../content/how-to-guides/how-to-register-an-azure-instance/), [Hetzner](../content/how-to-guides/how-to-register-a-hetzner-server/), and [bare metal](../content/how-to-guides/how-to-register-a-bare-metal-machine-iso/)) and create [machine classes](../content/how-to-guides/how-to-create-machine-classes/) to match your hardware. By default, the example [cluster template](../content/reference/cluster-templates/) is configured to use 3 instances of machine classes named `omni-contrib-controlplane`, and all instances that match a machine class called `omni-contrib-workers`. You can modify these settings in the [cluster-template.yaml](https://github.com/siderolabs/contrib/blob/main/examples/omni/infra/cluster-template.yaml), but keep in mind that for Rook/Ceph to work you will need to use at least 3 instances with additional block devices for storage.

Once machines are registered you can create the cluster using the cluster template in the `infra` directory.

```bash
omnictl cluster template sync --file cluster-template.yaml
```

This should create the cluster as described, bootstrap ArgoCD, and begin installing applications from your repo. Depending on your infrastructure, it should take 5-10 mins for the cluster to come fully online with all applications working and healthy. Monitoring can be viewed directly from Omni using the [workload proxy](../content/how-to-guides/how-to-expose-http-service-from-a-cluster/) feature, with links to Grafana and Hubble found on the left-hand side of the Omni UI.
