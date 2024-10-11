
# Kubernetes Workshop Lab Setup Guide

### Overview
The purpose of this guide is to enable cluster creation to be decoupled from the user/developer/student experience for running lab environments, workshops, or playgrounds.

---

## Recipe for Setting Up Kubernetes with Rancher

### 1. Build a Cluster
Choose your preferred method to build a Kubernetes cluster and set up a Rancher server. Ideally, use **Infrastructure as Code (IaC)** for reproducibility.

- **Build a Rancher Server**:
  - We used **Packer** to build a server image on the cloud of your choice (e.g., AWS, GCP).
  - We used **Terraform** and **Docker** to set up the Rancher server.

### 2. Import the Cluster
- Import the cluster and use the Rancher provider to provision users and resources within the cluster.
  - Note: Rancher uses the term **"registering"** for this step.

### 3. Automate Everything with a Pipeline!
- Automate cluster setup using a pipeline.
  - We used **GitLab Pipelines** to achieve this. 😊

> **Tip**: This setup was used for demonstration purposes but can easily be adapted to suit your own use case or lab environment.  
> For detailed information, refer to the official [Rancher Documentation](https://ranchermanager.docs.rancher.com/how-to-guides/new-user-guides/kubernetes-clusters-in-rancher-setup/register-existing-clusters).

---

## Setting up a Rancher Server on Google Kubernetes Engine (GKE) with Terraform

### 1. Build a Rancher Server on Google Cloud

#### 1.1 Use Packer to Build a Custom Image on GCP
- Refer to the [Packer Documentation](https://www.packer.io/docs) for more details.
- Create a Packer template to build a custom GCE image:

  ```json
  {
    "builders": [
      {
        "type": "googlecompute",
        "project_id": "YOUR_PROJECT_ID",
        "source_image_family": "debian-10",
        "zone": "us-central1-a",
        "image_name": "rancher-server-image-{{timestamp}}",
        "ssh_username": "packer"
      }
    ]
  }
  ```

- Build the image:
  ```bash
  packer build packer.json
  ```

#### 1.2 Use Terraform to Deploy the Rancher Server
- Refer to the [Terraform GCP Provider Documentation](https://registry.terraform.io/providers/hashicorp/google/latest/docs).
- Terraform configuration to deploy a Rancher server on GCP:

  ```hcl
  provider "google" {
    project = "YOUR_PROJECT_ID"
    region  = "us-central1"
  }

  resource "google_compute_instance" "rancher_server" {
    name         = "rancher-server"
    machine_type = "n1-standard-1"
    zone         = "us-central1-a"

    boot_disk {
      initialize_params {
        image = "rancher-server-image-from-packer"
      }
    }

    network_interface {
      network = "default"
      access_config {}
    }
  }
  ```

---

## 2. Create and Import a GKE Cluster into Rancher

### 2.1 Create a GKE Cluster with Terraform
- Example Terraform configuration for GKE:

  ```hcl
  resource "google_container_cluster" "primary" {
    name     = "gke-cluster"
    location = "us-central1-a"
    initial_node_count = 3

    node_config {
      machine_type = "e2-medium"
    }
  }
  ```

### 2.2 Import the GKE Cluster into Rancher
- Refer to the [Rancher Documentation for Importing GKE Clusters](https://ranchermanager.docs.rancher.com/v2.5/getting-started/cluster-deployment/import-clusters).
- Steps to import:
  1. Navigate to **Clusters** -> **Import Cluster** in the Rancher UI.
  2. Select **Google Kubernetes Engine** and follow the provided instructions.
  3. Run the provided `kubectl` command to import the cluster.

---

## 3. Use Terraform to Provision Users and Resources in Rancher

### 3.1 Install Rancher Terraform Provider
- Refer to the [Rancher Terraform Provider Documentation](https://registry.terraform.io/providers/rancher/rancher2/latest/docs).
- Rancher provider setup in Terraform:

  ```hcl
  provider "rancher2" {
    api_url    = "https://your-rancher-url"
    token_key  = "your-rancher-api-key"
    insecure   = true
  }
  ```

### 3.2 Use Terraform to Create Resources
- Example Terraform configuration to create a user and namespace in the GKE cluster:

  ```hcl
  resource "rancher2_user" "user1" {
    username = "gkeuser"
    password = "securepassword"
  }

  resource "rancher2_namespace" "namespace1" {
    name       = "gke-demo-namespace"
    cluster_id = rancher2_cluster.gke_cluster.id
  }

  resource "rancher2_cluster" "gke_cluster" {
    name = "gke-cluster"
    gke_config {
      project_id     = "YOUR_PROJECT_ID"
      zone           = "us-central1-a"
      cluster_name   = "gke-cluster"
      oauth_token    = "your-oauth-token"
    }
  }
  ```

### 3.3 Apply Terraform
- Initialize and apply your Terraform configuration:
  ```bash
  terraform init
  terraform apply
  ```

> **Note**: Make sure to read the [official Rancher documentation](https://ranchermanager.docs.rancher.com/how-to-guides/new-user-guides/kubernetes-clusters-in-rancher-setup/register-existing-clusters) before proceeding.

---

## Conclusion
This guide demonstrates how to:
1. Build a Rancher server on Google Cloud.
2. Create and import a GKE cluster.
3. Provision resources using Terraform.

Feel free to adapt these steps for your own use case or lab environment. 😊
