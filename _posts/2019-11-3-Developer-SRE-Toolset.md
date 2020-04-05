---
layout: single
title: What's in my cli tool belt?   
tags: tools cli mac kubernetes k8s
---

Everyone has built a new laptop from scratch before and it's always a struggle to make it "yours" again. I'll walk through what I install to make it "usable" for my day-to-day job. I work a lot with Kubernetes, Docker, and VMware technologies. So my toolset focuses a lot on making my terminal more user friendly and the cli tools/plugins to make that experience faster and  more efficient.

Step one, install Brew! Owning a Mac I can't emphasize this enough. It's like `pip` but it actually works! I try to install everything with brew because of ease of install/upgrade/re-install/automation.

**NOTE:** Please use the tools below at your own risk.
{: .notice--warning}

---
### General

I left this part up to you(except `brew` :grin:). Brave as a browser has started to grow on me and I've used Tunnelblick for my VPN client for many years.

- [Brew](https://brew.sh/)
- [Tunnelblick](https://tunnelblick.net/)
- [Brave](https://brave.com/)

---
### Terminal

The combinations here are endless, between getting the correct theme, font, and iTerm2 profile, you could spend hours on this step. In the end it will be completely worth it though. The version control (git) feedback as you are working with code additions/commits/branching/pushing/etc has tremendous value in tracking where you currently stand with your repository.

- [iTerm2](https://iterm2.com/index.html) - `brew install iterm2`
- [Oh my zsh](https://ohmyz.sh/)
- [Fonts](https://github.com/romkatv/powerlevel10k#fonts)
- [Powerline10k](https://github.com/romkatv/powerlevel10k)
- [HTTPie](https://httpie.org/)
- [Colorls](https://github.com/athityakumar/colorls)
- [Bat](https://github.com/sharkdp/bat)
- VIM - `brew install vim`

Before:
![Before](/images/2019-11-3/terminal-before.png)

After:
![After](/images/2019-11-3/terminal-after.png)

---
### Container Tools

This section is pretty self-explanatory. One of my colleagues introduced me to `dive` as a cli tool to dig through docker image layers.

- [Docker for Mac](https://docs.docker.com/docker-for-mac/install/)
	- Docker-compose (included)
- [Dive](https://github.com/wagoodman/dive) - `brew install dive`

---
### Kubernetes Tools

I spend the majority of my time working with Kubernetes. Tools like Kubectx/Kubens make cluster/context switching a breeze. Krew is relatively new, but it's a community of plugins that extend `kubectl` cli functionality in various ways. Think of it has a plugin manager for `kubectl`. 

Kind (Kubernetes in Docker) was introduced to me by @mauilion (Duffie Cooley). A tool for bringing up local Kubernetes clusters in Docker. It can also be used as a bootstrap cluster for ClusterAPI. Has a simple cli and cluster(s) are defined in `yaml`. 

- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) - `brew install Kubernetes-cli`
- [Krew](https://github.com/kubernetes-sigs/krew)
	- [Plugins](https://github.com/kubernetes-sigs/krew-index/blob/master/plugins.md)
		- View-secrets - `kubectl krew install view-secret`
		- Konfig - `kubectl krew install konfig`
		- Access-matrix - `kubectl krew install access-matrix`
- [Kubectx/Kubens](https://github.com/ahmetb/kubectx) - `brew install kubectx`
- [Kustomize](https://github.com/kubernetes-sigs/kustomize) - `brew install kustomize`
- [Kind](https://github.com/kubernetes-sigs/kind)
- [Clusterctl](https://github.com/kubernetes-sigs/cluster-api/tree/master/cmd/clusterctl)
- [Velero](https://github.com/vmware-tanzu/velero)
- [Helm](https://github.com/helm/helm) - `brew install kubernetes-helm`
- [Octant](https://github.com/vmware-tanzu/octant)
- [K9s](https://github.com/derailed/k9s) - `brew install derailed/k9s/k9s`
- [Sonobouy](https://github.com/vmware-tanzu/sonobuoy)

---
### Developer Tools

In the dev tools, `git` is a tool that I can't live without. It allows you to clone repo's, track changes, commit code, push to remote repo's, and much more. After you have your terminal setup with Git integration it becomes second nature to use for the basics. VS Code is my preferred editor because of all the plugins and native Git integration.

- Git `brew install git`
- [VS Code](https://code.visualstudio.com/download)
	- Plugins
		- Docker
		- Kubernetes
		- Python
		- Terraform
		- Markdown auto-preview
- [Postman](https://www.getpostman.com/downloads/)
- Go `brew install go`
- Python3 `brew install python3`
- [Skaffold](https://github.com/GoogleContainerTools/skaffold) - `brew install skaffold`
- [Fly](https://github.com/concourse/concourse)
- [Jx](https://jenkins-x.io/docs/getting-started/setup/install/) - `brew tap jenkins-x/jx && brew install jx`
- jq - `brew install jq`
- wget - `brew install wget`
- watch - `brew install watch`
- vault - `brew install vault`

---
### Infrastructure Tools

Huge fan of Terraform and I've been using it for years for different use cases. The [vSphere](https://github.com/terraform-providers/terraform-provider-vsphere) and [NSX provider](https://github.com/terraform-providers/terraform-provider-nsxt) have matured over time and continue to do so. The ability to document how your infrastructure is deployed with `.tf` files provides a reference of how it was deployed and also can be tracked in version control.

- [Govc](https://github.com/vmware/govmomi/tree/master/govc) - `brew install govmomi/tap/govc`
- [Terraform](https://www.terraform.io/) - `brew install terraform`
- [Packer](https://www.packer.io/) - `brew install packer`
- [Pulumi](https://github.com/pulumi/pulumi) - `brew install pulumi`
