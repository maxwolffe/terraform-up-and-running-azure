# Terraform Up And Running - Azure

A short tutorial for using [Terraform](terraform.io) in Azure. This project is an attempt to port the code from [terraform-up-and-running-code](https://github.com/brikis98/terraform-up-and-running-code) to Azure.

## What is Terraform?

Terraform is a provisioning tool for building, changing, and versioning infrastructure safely and efficiently.

It interfaces with popular cloud providers to allow one to deploy their topology (virtual machines, network groups, storage engines, etc) in a consistent and safe way.

[Read more here](https://www.terraform.io/intro/index.html)

## Pre-requisites

The below assume that you have [homebrew](https://brew.sh/) installed.

1. [Install the Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).
    ```
    brew update && brew install azure-cli
    ```
2. Sign up for an Azure Account. You can start with [a free account by using this promotion](https://azure.microsoft.com/en-us/free/).
3. Log into the azure command line:
    ```
    az login
    ```
4. Install Terraform.
    ```
    brew update && brew install terraform
    ```

## Lesson 0 - Getting Started - The very basics.

This lesson is going to cover creating a single resource in Azure and cleaning it up.

1. Create a terraform file in your working directory: `main.tf`. For this tutorial, we'll be making all of our changes within this file.
2. We're going to use Azure for this tutorial, so we start by configuring the Azure provider, which is the client library we'll use to interact with the Azure cloud platform. We'll pin the resource manager to a specific version, so the following examples are more likely to work :P.
```
file: main.tf

provider "azurerm" {
  version = "=1.36.0"
}
```
3. Let's check to make sure this worked by running our first terraform command. You should see something like the following:
```
$ terraform init

Initializing the backend...

Initializing provider plugins...
- Checking for available provider plugins...
- Downloading plugin for provider "azurerm" (hashicorp/azurerm) 1.36.0...

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```
4. Next lets create some reasources! Azure stores references to common pieces of infrastructure (or resources) in resource groups. So we'll start by creating a resource group.

```
file: main.tf

provider "azurerm" {
  version = "=1.36.0"
}

resource "azurerm_resource_group" "main" {
  name     = "mwolffe-resources"
  location = "West US 2"
}
```
5. Now that we have our first piece of insfrastructure defined in code, let's create it on Azure!

`terraform plan` allows you to see the changes which Terraform will make on your behalf. Let's do that now.
```
$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # azurerm_resource_group.main will be created
  + resource "azurerm_resource_group" "main" {
      + id       = (known after apply)
      + location = "westus2"
      + name     = "mwolffe-resources"
      + tags     = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

`terraform plan` doesn't actually create anything for us, it just shows us the changes we're going to make. Let's actually create those resources now, with `terraform apply`.

```
$ terraform apply

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # azurerm_resource_group.main will be created
  + resource "azurerm_resource_group" "main" {
      + id       = (known after apply)
      + location = "westus2"
      + name     = "mwolffe-resources"
      + tags     = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

azurerm_resource_group.main: Creating...
azurerm_resource_group.main: Creation complete after 1s [id=/subscriptions/35f585a2-f242-4e08-9041-e630a14bafe7/resourceGroups/mwolffe-resources]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

Let's go to the Azure portal to make sure that resource group has actually been created for us!

- Visit ms.portal.azure.com
- Sign in
- Navigate to Resource Groups
- Observe your resource group and rejoice!

6. Let's clean up this new resource group by running `terraform destroy`. This will make sure we're not charged for item's we're not using.

## Lesson 1 - Getting Started - Something kinda real

In this Lesson, we're going to create a VM running a very simple web server and deploy it to Azure.

Check out the template file - `/gettingStarted/main.tf`.

This file contains a few things:
- input variables
- A few new resources:
   - [Virtual Network](https://www.terraform.io/docs/providers/azurerm/r/virtual_network.html)
   - [Subnet](https://www.terraform.io/docs/providers/azurerm/r/subnet.html)
   - [Public IP](https://www.terraform.io/docs/providers/azurerm/r/public_ip.html)
   - [Network Security Group](https://www.terraform.io/docs/providers/azurerm/r/network_security_group.html)
   - [Network Interface (NIC)](https://www.terraform.io/docs/providers/azurerm/r/network_security_group.html)
   - [Virtual Machine](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html)
- output variables



