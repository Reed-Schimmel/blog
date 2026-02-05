title: Bringing Work Home: Replicating Enterprise DevOps in a Homelab
date: 2024-07-01T10:01:18.000Z
draft: false
slug: bringing-work-home-replicating-enterprise-devops-in-a-homelab
github_link: "https://github.com/Reed-Schimmel/reeds-homepage"
author: "Reed Schimmel"
tags:
  - Terraform
  - Proxmox
  - Talos
  - Kubernetes
bg_image: ""
description: ""
toc: 
---

# Bringing Work Home: Replicating Enterprise DevOps in a Homelab

This project began as a way for me to master the technology stack used by the Cloud DevOps team at Actian. Entering a team of seasoned engineers who had been building on the cloud for a decade was intimidating. I was trying to understand how code from dozens of repositories interacted while simultaneously learning the underlying technologies from scratch.

My solution? Build a miniature version at home.

The goal was to replicate the design choices and architectural patterns used at work, but swap out the proprietary or cloud-specific components for self-hosted alternatives. This provided a sandbox where I could experiment freely without the fear of breaking development environments for my co-workers. It also turned "work" into a passion project, leveraging my love for homelabbing to accelerate my career growth.

## The Stack: Work vs. Home

At work, we used Terraform to provision Kubernetes engines across the major cloud providers (GCP, AWS, Azure). I wanted to keep Terraform as the common language but needed a substitute for the public cloud.

| Component | Work (Enterprise) | Home (Homelab) |
| :--- | :--- | :--- |
| **Infrastructure** | AWS / GCP / Azure | Proxmox VE (Mini PCs) |
| **Provisioning** | Terraform | Terraform (Proxmox Provider) |
| **OS / K8s** | Managed K8s (EKS/GKE/AKS) | Talos Linux VMs |
| **CD** | Harness / ArgoCD | ArgoCD |
| **CI / Workflows** | Harness / Argo Workflows | Argo Workflows |
| **SCM** | Bitbucket Cloud | GitHub |

### The "Cloud" Layer: Proxmox
The most dramatic difference is the infrastructure layer. Instead of calling AWS APIs, I'm using **Proxmox VE** running on Mini PCs to act as my private cloud. By using the Proxmox and Talos Terraform providers, I can wrap complex configurations into clean **Terraform Modules**. This abstraction allows me to define resources that look and feel uniform, similar to how we define high-level resources for the "Big 3" cloud providers.

### The Kubernetes Layer: Talos Linux
To mimic the managed Kubernetes experience (where you don't manage the underlying OS details of the nodes), I chose **Talos Linux**. It is an immutable, API-managed operating system designed specifically for Kubernetes. It allows me to define the OS configuration via Terraform, keeping the entire stack declarative.

### The GitOps Engine: ArgoCD
When I joined, the team was already using **ArgoCD** for Kubernetes application deployment, but we used Harness to deploy our Infrastructure-as-Code (Terraform). Midway through my first year, we migrated those pipelines to **Argo Workflows**. This was a stroke of luck for my homelab journey. Since the entire Argo stack is open source, I didn't need to find a "homelab equivalent"—I could run the exact same enterprise-grade tooling at home.

### Source Control: Why GitHub?
My initial vision included self-hosting **Gitea** to replace Bitbucket, achieving total self-reliance. However, this introduced a "chicken and egg" problem: hosting the code that builds the infrastructure *on* the infrastructure itself is risky during the bootstrap phase. To ensure my Infrastructure-as-Code (IaC) remained accessible even if the lab went down, I decided to stick with **GitHub** as the external source of truth.

## A Career Portfolio
Now, two years later, I am polishing up this repository to share with the community. This blog series documents the journey of building this platform—from the initial Terraform struggles to a fully functioning GitOps environment. It serves as both a tutorial for others and a portfolio of the skills I sharpened during those late-night debugging sessions.


---

Date: July 1, 2024
Copyright (c) 2024 Reed Schimmel. All rights reserved.