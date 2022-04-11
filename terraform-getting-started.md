# Getting Started with Terraform

Terraform is an open-source infrastructure as code (IaC) tool that enables you to safely and predictably create, change, and improve infrastructure. 
With Terraform you can manage infrastructure using configuration files rather than through a graphical user interface. 

This tutorial teaches you how to install Terraform and write your own infrastructure configuration to build and delete an NGINX server using Docker. 

## Prerequisites

To follow this tutorial you will need:

- Terminal on OS X
- code editor (e.g. [Visual Studio Code](https://code.visualstudio.com/download))
- [Docker Desktop for Mac](https://docs.docker.com/desktop/mac/install/)

## Install Terraform

To install Terraform, find the [appropriate package](https://www.terraform.io/downloads.html) for your system and download it as a zip archive. 

Unzip the package. Terraform runs as a single binary named `terraform`. 

Make sure that the `terraform` binary is available on your `PATH`. To check this, print a colon-separated list of locations in your `PATH`.

```shell
$ echo $PATH
```
Move the Terraform binary to one of the listed locations. This command assumes that the binary is currently in your downloads folder and that your `PATH` includes `/usr/local/bin`, but you can customize it if your locations are different.

```shell
$ mv ~/Downloads/terraform /usr/local/bin/
```
Verify that the installation worked by opening a new terminal session and listing Terraform's available subcommands.

```shell
$ terraform -help
Usage: terraform [global options] <subcommand> [args]

The available commands for execution are listed below.
The primary workflow commands are given first, followed by
less common or more advanced commands.

##...
```

## Build infrastructure

Now that you have installed Terraform, you can provision an NGINX server in less than a minute using Docker on Mac. 

After you install Docker on your local machine, start Docker Desktop.

```shell
$ open -a Docker
```

You need to store Terraform configuration code locally. Create a new directory on your local machine and navigate to it.

```shell
$ mkdir terraform-demo
$ cd terraform-demo
```

Create the `main.tf` file for your Terraform configuration code.

```shell
$ touch main.tf
```

Paste the following lines into the file and save your changes.

```hcl
terraform {
  required_providers {
    docker = {
      source = "kreuzwerker/docker"
    }
  }
}
provider "docker" {
    host = "unix:///var/run/docker.sock"
}
resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name  = "training"
  ports {
    internal = 80
    external = 80
  }
}
resource "docker_image" "nginx" {
  name = "nginx:latest"
}
```

Initialize the project, which downloads a plugin that allows Terraform to interact with Docker.

```shell
$ terraform init
```

If there are no errors, provision the NGINX server container with `terraform apply`. 

```shell
$ terraform apply

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # docker_container.nginx will be created
  + resource "docker_container" "nginx" {
  ##... 

  # docker_image.nginx will be created
  + resource "docker_image" "nginx" {
  ##...   

Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.
  
  Enter a value:
```

When Terraform asks you to confirm, type `yes` and press `ENTER`.
The command will take up to a few minutes to run and will display a message indicating that the resource was created.

```shell
  Enter a value: yes

docker_image.nginx: Creating...
docker_image.nginx: Still creating... [10s elapsed]
docker_image.nginx: Creation complete after 13s [id=sha256:fd3d31a07ae69fb788a579676d2c5f4c3dd201f57bcd6c174cd0bd6475886f23nginx:latest]
docker_container.nginx: Creating...
docker_container.nginx: Creation complete after 0s [id=ceda9233cc50d666ce4ea00ca88651492378e4c5e059f1416177ad0bfbc23fcb]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

Verify the existence of the NGINX container by visiting [localhost:80](localhost:80) in your web browser.

![nginx](https://mktg-content-api-hashicorp.vercel.app/api/assets?product=tutorials&version=main&asset=public%2Fimg%2Fterraform%2Fgetting-started%2Fterraform-docker-nginx.png)

## Destroy infrastructure

To destroy the provisioned NGINX container, run `terraform destroy`. This command is the inverse of `terraform apply` in that it terminates all the resources specified in your Terraform state. 

```shell
$ terraform destroy
Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # docker_container.nginx will be destroyed
  - resource "docker_container" "nginx" {
##...    

  # docker_image.nginx will be destroyed
  - resource "docker_image" "nginx" {
##...   

Plan: 0 to add, 0 to change, 2 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value:
```

When Terraform asks you to confirm, type `yes` and press `ENTER`. Terraform will now destroy the resources it had created earlier.

```shell
  Enter a value: yes

docker_container.nginx: Destroying... [id=ceda9233cc50d666ce4ea00ca88651492378e4c5e059f1416177ad0bfbc23fcb]
docker_container.nginx: Destruction complete after 1s
docker_image.nginx: Destroying... [id=sha256:fd3d31a07ae69fb788a579676d2c5f4c3dd201f57bcd6c174cd0bd6475886f23nginx:latest]
docker_image.nginx: Destruction complete after 0s

Destroy complete! Resources: 2 destroyed.
```
## Next Steps

You've provisioned and destroyed an NGINX webserver with Terraform. The resource configuration created in this tutorial lets you version, reuse, and share the webserver provisioning workflow. You can use Terraform to manage your infrastructure in a consistent and a repeatable way.

Now that you are familiar with the core concepts of infrastructure as code and Terraform, you are ready to write your own infrastructure configuration. Next, you will create real infrastructure in the cloud of your choice.

- [Amazon Web Services (AWS)](https://learn.hashicorp.com/tutorials/terraform/aws-build)
- [Azure](https://learn.hashicorp.com/tutorials/terraform/azure-build)
- [Google Cloud Platform (GCP)](https://learn.hashicorp.com/tutorials/terraform/google-cloud-platform-build)
- [Oracle Cloud Platform (OCI)](https://learn.hashicorp.com/tutorials/terraform/oci-build)