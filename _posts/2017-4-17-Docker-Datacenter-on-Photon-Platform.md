---

layout: post
title: Using Terraform to deploy Docker Datacenter on Photon Platform 

---

In this post I will discuss how a developer or infrastructure
administrator can use Terraform to automate infrastructure provisioning.
I will demonstrate how you can use Terraform with Photon Platform to
deploy and scale Docker Datacenter. Terraform is a tool for building, changing,
and versioning infrastructure. Terraform uses configuration files to
describe the infrastructure you wish to provision.

I have created two configuration files for this deployment. This first
one deploys the Docker Datacenter Manager (manager.tf) and the second one deploys the
workers (worker.tf) and automatically adds them to the Swarm Cluster. Photon Platform is
used for multi-tenancy and control how much infrastructure you are able
to deploy.

![Terraform Directory](/images/2017-4-17/terraform-directory.png)

Let's take a look at the manager.tf. The purpose of this file is to
deploy a VM and install docker ucp on top of it. For that we use the
Photon Platform provider to deploy the VM and Terraform to issue the
remote execution commands to deploy the Docker Datacenter Manager.

{% highlight yaml %}
provider "photon" {
    photon_target = "${var.PC_TARGET}"
    user = "${var.USER}"
    password = "${var.PASSWORD}"
    allow_unverified_ssl = "${var.TRUST_SSL_CERT}"
}

data "photon_tenant" "tenant" {
    name = "${var.TENANT_NAME}"
}

data "photon_project" "project" {
    tenant = "${data.photon_tenant.tenant.id}"
    name = "${var.PROJECT_NAME}"
}

data "photon_image" "vm_image" {
    name ="${var.VM_IMAGE_NAME}"
}

data "photon_network" "vm_network" {
    name = "${var.NETWORK_NAME}"
}

resource "photon_virtual_machine" "docker_manager" {

    project = "${data.photon_project.project.id}"
    name="${var.VM_NAME}"
    flavor = "${var.VM_FLAVOR_NAME}"
    sourceImageId = "${data.photon_image.vm_image.id}"
    subnets = [ { id = "${data.photon_network.vm_network.id}"} ]
    ephemeralDisks = [
        {
            name = "boot-disk"
            kind = "ephemeral-disk"
            flavorName = "${var.EPHEMERAL_DISK_FLAVOR_NAME}"
            bootDisk = true
        }
    ]

    state = "STARTED"

    provisioner "remote-exec" {

        inline = [
            "docker run --rm -it --name ucp -v /var/run/docker.sock:/var/run/docker.sock docker/ucp:2.1.3 install --host-address ${self.ipAddress} --admin-username ${var.DOCKER_USER} --admin-password ${var.DOCKER_USER_PWD} --san docker-ucp.corp.local",
	    "docker swarm join-token worker| grep -iv 'To Add'  > swarm.token",
        ]

        connection {
            type     = "ssh"
            user     = "${var.VM_USER}"
            password = "${var.VM_USER_PWD}"
        }
    }

}

output "manager_vm_id" {
  value = "${photon_virtual_machine.docker_manager.id}"
}

output "manager_vm_ip" {
  value = "https://${photon_virtual_machine.docker_manager.ipAddress}"
}
{% endhighlight %}

Now that the manager is deployed we can start deploying swarm workers
and add them into the cluster. That is where the worker.tf comes in.

{% highlight yaml %}
resource "photon_virtual_machine" "docker_worker" {

    project = "${data.photon_project.project.id}"
    #name="${var.WORKER_VM_NAME}"
    name="docker-worker-${count.index}"
    flavor = "${var.VM_FLAVOR_NAME}"
    sourceImageId = "${data.photon_image.vm_image.id}"
    subnets = [ { id = "${data.photon_network.vm_network.id}"} ]
    ephemeralDisks = [
        {
            name = "boot-disk"
            kind = "ephemeral-disk"
            flavorName = "${var.EPHEMERAL_DISK_FLAVOR_NAME}"
            bootDisk = true
        }
    ]

    count = "${var.COUNT}"

    state = "STARTED"

    provisioner "remote-exec" {

        inline = [
	    "sshpass -p VMware1! scp -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null root@${photon_virtual_machine.docker_manager.ipAddress}:~/swarm.token .;sh -e swar*"
        ]

        connection {
            type     = "ssh"
            user     = "${var.VM_USER}"
            password = "${var.VM_USER_PWD}"
        }
    }

}

output "worker_vm_id" {
  value = "${photon_virtual_machine.docker_worker.id}"
}

output "worker_vm_ip" {
  value = "${photon_virtual_machine.docker_worker.ipAddress}"
}
{% endhighlight %}

Now that we have the configuration files for the manager and workers, let's deploy it. In our terminal we issue `terraform apply` and it will ask for the number of worker nodes you want to deploy. On the right you can see Photon Platform and the available infrastructure resources you have assigned to your project. 
![Terraform Directory](/images/2017-4-17/terraform-photon-start.png)

After you execute `terraform apply` it will start deploying the
infrastructure described in the configuration files. The first thing it
deploys is the Docker Datacenter Manager so that workers can join the
cluster.
![Terraform Directory](/images/2017-4-17/photon-manager.png)

After the manager is deployed, it will spin up the number of workers you
specified for the "COUNT" variable. 
![Terraform Directory](/images/2017-4-17/photon-complete.png)

The last part of a terraform template is the "outputs". I want to know
the endpoint for my Docker Datacenter VM so I have defined that as an
output. Terraform also tells you what it added/changed/removed whenever
you issue a `terraform plan` or `terraform apply`.
![Terraform Directory](/images/2017-4-17/terraform-complete.png)

Success! We have Docker Datacenter up and running with 5 worker nodes.
Now you can start deploying your contanerized workloads with the Docker
API.
![Terraform Directory](/images/2017-4-17/ddc-ui.png)

Now let's say we need to scale up the number of worker nodes. Simply run
`terraform apply` and specify the new number of worker nodes you want.
Terraform will examine the current state of the infrastructure and
deploy 5 more VM's on top of Photon Platform and add them into the Swarm
cluster.
![Terraform Directory](/images/2017-4-17/scaleout-10-nodes-cli.png)

This is a simple example of how you can use "Infrastructure as Code"
with Terraform and Photon Platform. You can store your configuration
files in version control to track changes and help rollback if something
went wrong.

Thank you!
