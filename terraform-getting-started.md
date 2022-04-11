# Getting Started with Terraform

Terraform is an open-source infrastructure as code (IaC) tool that enables you to safely and predictably create, change, and improve infrastructure. 
With Terraform you can manage infrastructure using configuration files rather than through a graphical user interface. 

This tutorial teaches you how to install Terraform and write your own infrastructure configuration to build and delete an NGINX server using Docker. 

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
```

When Terraform asks you to confirm, type `yes` and press `ENTER`.
The command will take up to a few minutes to run and will display a message indicating that the resource was created.

## Destroy infrastructure

To destroy the provisioned NGINX container, run `terraform destroy`. This command is the inverse of `terraform apply` in that it terminates all the resources specified in your Terraform state. 

```shell
$ terraform destroy
```

When Terraform asks you to confirm, type `yes` and press `ENTER`. Terraform will now destroy the resources it had created earlier.
