---
title: "Building the Foundation: Terraform, Proxmox, and Talos"
date: 2024-06-29T10:01:18.000Z
draft: false
slug: building-foundation-terraform-proxmox-talos
github_link: "https://github.com/Reed-Schimmel/blog"
author: "Reed Schimmel"
tags:
  - Terraform
  - Proxmox
  - Talos
  - Kubernetes
bg_image: ""
description: ""
toc:
# Copyright (c) 2026 Reed Schimmel. All Rights Reserved.
---

- [Project Source Code](https://github.com/Reed-Schimmel/pve-k8s-iac-homelab/tree/main)

In the [previous post](/posts/Enterprise_Homelab.md), I outlined the goal: a homelab that mirrors enterprise DevOps. Now, let's start building it. We'll begin by setting up the Terraform providers and downloading the Talos Linux ISO.

At work I provision k8s clusters on the big cloud providers using terraform. I want to replicate this workflow in my homelab. Many homelab educators use ansible to install k3s on ubuntu vms. So here is my attempt to do the kubernetes homelab with terraform, proxmox and talos, no Ansible.

## Requirements
- Proxmox VE 8

## Setting up the Provider

First we'll define our Terrform and Proxmox provider versions. I've gone with terraform version 1.8.2 because is it the latest version that OpenTofu, as of this writting, has a migration guide for. In the future we might move to this open-source Terraform fork.

In your terraform directory `provider.tf`:
```
terraform {

  required_version = "1.8.2" # https://opentofu.org/docs/intro/migration/terraform-1.8/

  required_providers {
    proxmox = {
      source  = "telmate/proxmox"
      version = "3.0.1-rc3"
    }
  }
}

# https://registry.terraform.io/providers/Telmate/proxmox/latest/docs#argument-reference
provider "proxmox" {
  pm_api_url          = var.proxmox_api_url
  pm_api_token_id     = var.proxmox_api_token_id
  pm_api_token_secret = var.proxmox_api_token_secret
  pm_tls_insecure     = true
}
```

These variable values must come from somewhere, so lets generate them! Follow [this guide](https://registry.terraform.io/providers/Telmate/proxmox/latest/docs#creating-the-proxmox-user-and-role-for-terraform) and focus on the **Creating the connection via username and API token** part as that is what I'm using here.

Now in `credentials.auto.tfvars`
```
proxmox_api_url = "https://<PROXMOX_MACHINE_IP>:<PROXMOX_MACHINE_PORT>/api2/json"
proxmox_api_token_id = "terraform-prov@pve!mytoken" # example value from docs
proxmox_api_token_secret = "<GENERATED_TOKEN>"
```

We should now be able to run `terraform init`.


## Defining the Resources

Now we make `main.tf` to start our IaC magic!

```
locals {
  pve_node    = "pve01"
  iso_storage = "local"
}

resource "proxmox_storage_iso" "talos-iso" {
  pve_node = local.pve_node
  filename = "talos-v1.7.5-proxmox-amd64.iso"
  url      = "https://factory.talos.dev/image/ce4c980550dd2ab1b17bbf2b08801c7eb59418eafe8f279833297925d67c7515/v1.7.5/nocloud-amd64.iso"
  storage  = local.iso_storage
}
```

What is this doing? We're telling proxmox to make an ISO file called "talos-v1.7.5-proxmox-amd64.iso" and where to download it from. You can learn how to generate an iso with the latest version of talos [here](https://www.talos.dev/latest/talos-guides/install/virtualized-platforms/proxmox/#qemu-guest-agent-support-iso). Set the values in the `locals` block to match your proxmox machine. If you would normal upload an ISO directly to the drive proxmox is installed on, then leave `iso_storage = "local"`. Change the pve_node to match your node's name.

We now have enough to test our setup. Run `terraform apply`:
```shell
$ tf apply

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # proxmox_storage_iso.talos-iso will be created
  + resource "proxmox_storage_iso" "talos-iso" {
      + filename = "talos-v1.7.5-proxmox-amd64.iso"
      + id       = (known after apply)
      + pve_node = "pve01"
      + storage  = "local"
      + url      = "https://factory.talos.dev/image/ce4c980550dd2ab1b17bbf2b08801c7eb59418eafe8f279833297925d67c7515/v1.7.5/nocloud-amd64.iso"
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value:
```
enter "yes".

## The Plan: Import then Code

Let's get to the fun stuff: deploy VMs with code! We'll adapt [this guide](https://www.talos.dev/v1.7/talos-guides/install/virtualized-platforms/proxmox/#create-vms) to use Terraform.

Since figuring out the exact HCL configuration for a VM can be tricky, we'll start by manually creating one in the Proxmox UI and then importing it into Terraform.

1. **Create VM in GUI:** Follow the "Create VMs" section in the proxmox web UI. Make sure in *Memory* to **UNCHECK** "Ballooning Device" in "Advanced". This is required by Talos. I gave my VM 4 CPU cores and 4096 MiB of memory.

2. **Define Import Block:** Create the file `import.tf`:
    ```
    resource "proxmox_vm_qemu" "talos-proxmox-vm" {}
    ```

3. **Run Import:** Run `terraform import proxmox_vm_qemu.talos-proxmox-vm <ID>` where id follow the scheme `<node_name>/qemu/<VMID>`
    
    Mine looks like `terraform import proxmox_vm_qemu.talos-proxmox-vm pve01/qemu/100` because my node's name is "pve01" and the VMID of the talos VM is 100.

Now we have the state for that resource in our `terraform.tfstate`. In the next post, we will use this state to define our actual `main.tf` configuration and launch the full cluster.

---

- [Project Source Code](https://github.com/Reed-Schimmel/pve-k8s-iac-homelab/tree/main)

---

Date: June 29, 2024
Copyright (c) 2024 Reed Schimmel. All rights reserved.