# Omni Documentation

Welcome to the Omni user guide! This guide shows you everything from getting started to more advanced deployments with Omni.

<button type="button" style="color: blue;">Get Started</button>

## What is Omni?

Omni is a Kubernetes management platform that simplifies the creation and management of Kubernetes clusters on any environment to provide a simple, secure, and resilient platform. It automates cluster creation, management and upgrades, and integrates Kubernetes and Omni access into enterprise identity providers. While Omni does provide a powerful UI, tight integration with Talos Linux means the platform is 100% API-driven from Linux to Kubernetes to Omni.

### Simple

Omni automatically creates a highly available Kubernetes API endpoint, transparently provides secure encryption, and automates Kubernetes and OS upgrades. Omni is perfectly suited for managing edge clusters or single node clusters, and in large data centers.

Omni is available as a SaaS operated by Sidero Labs, and for licensing for on-premises installations.

{% hint style="info" %}
Omni is released under the BUSL. Using Omni for production use requires a commercial license. Contact [sales@SideroLabs.com](mailto:sales@siderolabs.com) for more information. Non-production use, such as using Omni for a home lab, does not require a license.
{% endhint %}

Enterprise support and fully managed and operated plans are available.

### Secure

Omni creates clusters with both Kubernetes and the OS configured for best-practices security. All traffic to Omni is wireguard-encrypted. Optionally, traffic between the cluster nodes can be encrypted, allowing clusters to span insecure networks. Integration with enterprise identity providers ensures that even admin-level kubeconfig is validated against current user access-lists.

## Is Omni for me?

Omni is excellent for managing clusters in just about any environment you have. Machines in the cloud, on-premise, edge, home - they all can be managed with Omni. With Omni you can even create hybrid clusters consisting of machines in disparate locations around the world.

Some common use cases are:

- On-premise bare metal clusters that can be scaled up with machines in the cloud
- Edge clusters that are supported by machines in the data center and/or cloud
- Mixed cloud
- Single node edge clusters

## Ready to get started?

[Sign Up](https://signup.siderolabs.io/) to start your free 2 week trial and start exploring today!

We suggest you join the #omni channel in our community [slack channel](https://slack.dev.talos-systems.io/)

## Status

For real-time status of Omni and other Sidero Labs services, see [https://status.siderolabs.com/](https://status.siderolabs.com/)

You can subscribe to updates by clicking the Bell icon in the top right corner.

For recent updates, bug fixes and changes, subscribe to the GitHub [Release notes.](https://github.com/siderolabs/omni/releases)

## Security notifications

If you have a security question, concern or issue with Omni, please email [security@siderolabs.com](mailto:security@siderolabs.com)

{% hint style="info" %}
Omni and Talos Linux may utilize KubeSpan, covered under U.S. Patent 11,968,180
{% endhint %}
