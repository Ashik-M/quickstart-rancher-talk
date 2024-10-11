# Our Kubernete Workshop Lab set-up

### Idea behind this is to allow Cluster creation to be decoupled from the User /Developer/ Student experience running lab environements for workshop purposes or playground purposes. 

**RECIPE**

- **1-Build a cluster** (choose your own path)
  Build a Rancher server (using IaC is preferrable): we used packer to build the image on which ever cloud, and terraform / docker for rancher server
- **2-Import the cluster** and use the Rancher provider to provision the users and resources within the cluster. (note Rancher uses the verbiage of "registering") 
      Terraform/Docker were used for the Rancher server.
    
- **3-Automate everything** with a pipeline!
  - We accomplished this with GitLab Pipelines. 😊
    
>[!TIP]
> This Is is what I made for demonstration purposes but this  could easily be translated to your own use case, your own lab enviornement, etc.
>For in depth docs please read the official info : https://ranchermanager.docs.rancher.com/how-to-guides/new-user-guides/kubernetes-clusters-in-rancher-setup/register-existing-clusters
        
  

```markdown
# Setting up a Rancher Server on GKE with Terraform

## 1. Build a Rancher Server on Google Cloud

### 1.1 Use Packer to Build a Custom Image on GCP
- [Packer Documentation](https://www.packer.io/docs)
- Create a Packer template for building a custom GCE image.
  ```json
  {
    "builders": [{
      "type": "googlecompute",
      "project_id": "YOUR_PROJECT_ID",
      "source_image_family": "debian-10",
      "zone": "us-central1-a",
      "image_name": "rancher-server-image-{{timestamp}}",
      "ssh_username": "packer"
    }]
  }
  ```
- Build the image:
  ```bash
  packer build packer.json
  ```

### 1.2 Use Terraform to Deploy the Rancher Server
- [Terraform GCP Provider](https://registry.terraform.io/providers/hashicorp/google/latest/docs)
- Terraform configuration for GCP:
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

### 1.3 Use Docker to Run Rancher on the Instance
- SSH into the instance and install Docker:
  ```bash
  sudo apt-get update
  sudo apt-get install -y docker.io
  ```

- Run Rancher using Docker:
  ```bash
  sudo docker run -d --restart=unless-stopped \
    -p 80:80 -p 443:443 \
    --name rancher-server \
    rancher/rancher:latest
  ```

## 2. Create and Import a GKE Cluster into Rancher

### 2.1 Use Terraform to Create a GKE Cluster
- [GKE Quickstart Documentation](https://cloud.google.com/kubernetes-engine/docs/quickstart)
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
- [Rancher Importing GKE Clusters](https://ranchermanager.docs.rancher.com/v2.5/getting-started/cluster-deployment/import-clusters)
- Steps:
  1. Navigate to **Clusters** -> **Import Cluster** in the Rancher UI.
  2. Select **Google Kubernetes Engine** and follow the instructions.
  3. Run the provided `kubectl` command to import the cluster.

## 3. Use Terraform to Provision Users and Resources in Rancher

### 3.1 Install Rancher Terraform Provider
- [Rancher Provider Documentation](https://registry.terraform.io/providers/rancher/rancher2/latest/docs)
- Rancher provider setup in Terraform:
  ```hcl
  provider "rancher2" {
    api_url    = "https://your-rancher-url"
    token_key  = "your-rancher-api-key"
    insecure   = true
  }
  ```

### 3.2 Use Terraform to Create Resources
- Example Terraform to create a user and namespace in the GKE cluster:
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

This setup demonstrates how to build a Rancher server on Google Cloud, import a GKE cluster, and provision resources using Terraform. But don't do this without reading the docs :D

https://ranchermanager.docs.rancher.com/how-to-guides/new-user-guides/kubernetes-clusters-in-rancher-setup/register-existing-clusters
```
