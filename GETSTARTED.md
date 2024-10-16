
# Getting Started with Kubernetes: Minikube and Kubeadm

Here’s a beginner-friendly guide to running Kubernetes on both Minikube and Kubeadm, including instructions for testing the setup with a simple "Hello World" container. The steps include necessary resources for downloading the binaries or installing the apps, and it works for both Linux and macOS.

---

## Table of Contents
1. [Running Kubernetes with Minikube](#running-kubernetes-with-minikube)
   - [Installing Minikube](#installing-minikube)
   - [Start Minikube](#start-minikube)
   - [Deploy a Hello World Container](#deploy-a-hello-world-container)
2. [Running Kubernetes with Kubeadm](#running-kubernetes-with-kubeadm)
   - [Installing Kubeadm](#installing-kubeadm)
   - [Initialize the Kubernetes Cluster](#initialize-the-kubernetes-cluster)
   - [Deploy a Hello World Container on Kubeadm](#deploy-a-hello-world-container-on-kubeadm)
3. [Conclusion](#conclusion)

---
## Running Kubernetes with Minikube (5 minute~)

### Destroy old instances of Minikube (optional)

 Clean Up Existing Minikube Setup
- **Delete the Existing Cluster:**
      
      minikube delete --all
- **Remove the Minikube Cache:**

      rm -rf ~/.minikube/cache
  
This ensures that any corrupted or incomplete cached images are removed.

### Installing Minikube
Minikube is an easy way to run Kubernetes locally.

- **Linux**:
  ```bash
  curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
  sudo install minikube-linux-amd64 /usr/local/bin/minikube
  ```

- **macOS**:
  Using Homebrew:
  ```bash
  brew install minikube
  ```

For more details, refer to the [Minikube Installation Guide](https://minikube.sigs.k8s.io/docs/start/).


### Start Minikube
1. Start a Kubernetes cluster with Minikube with Docker as the driver and set flag for latest stable:
   ```bash
   minikube start --driver=docker --kubernetes-version=stable
   
   ```

- > Note: You can set a particular version like this:

  >     minikube start --driver=docker --kubernetes-version=v1.31.0

2. Verify that Minikube is running:
   ```bash
   minikube status
   ```
![Screenshot 2024-10-15 at 10 11 21 PM](https://github.com/user-attachments/assets/1f8b55da-f395-4246-8074-1b3f346c7eab)

   
>   If you have multiple clusters using kubectl, ensure that you have set your kubectl context to minikube
   "kubectl config use-context minikube"

### Deploy a Hello World Container


1. Create a deployment using the `nginx` image as a simple web server:
   ```bash
   kubectl create deployment hello-minikube --image=nginx
   ```

2. Expose the deployment as a service:
   ```bash
   kubectl expose deployment hello-minikube --type=NodePort --port=80
   ```

3. Get the URL to access the service:
   ```bash
   minikube service hello-minikube --url
   ```

![Screenshot 2024-10-15 at 10 17 58 PM](https://github.com/user-attachments/assets/300256a3-af9c-4c69-8f32-b92614ca878e)


4. Visit the URL in your browser to confirm the "Hello World" container is running.

---

# Running Kubernetes with Kubeadm (15+mins if you're already an expert)

### Installing Kubeadm
Kubeadm helps you bootstrap a Kubernetes cluster on your local machine or a VM/

- **Linux** (This demo was ran on GCP / compute Engine/ Debian 12image with appropriate access to internet.
- You have to figure out how to safely SSH into the vm. 
 Here is the guide formatted in Markdown, ready to be copied and pasted into a `.md` file on GitHub:

> - Read the docs! the official ones if you're  set up is a bit different. [kubeadm-1.30](https://v1-30.docs.kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)
---
# Kubernetes Cluster Setup with Kubeadm on Debian 12 (Bookworm)

This guide provides step-by-step instructions to set up a minimal Kubernetes cluster using **kubeadm** on a **Debian 12 (Bookworm)** instance running on Google Cloud Compute Engine. After setting up the cluster, we'll deploy a "Hello World" application to verify its functionality.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Step 1: Update System Packages](#step-1-update-system-packages)
- [Step 2: Disable Swap](#step-2-disable-swap)
- [Step 3: Install Container Runtime (containerd)](#step-3-install-container-runtime-containerd)
- [Step 4: Install Kubernetes Components](#step-4-install-kubernetes-components)
- [Step 5: Initialize the Kubernetes Cluster](#step-5-initialize-the-kubernetes-cluster)
- [Step 6: Set Up Pod Network (Calico)](#step-6-set-up-pod-network-calico)
- [Step 7: Deploy a Hello World Application](#step-7-deploy-a-hello-world-application)
- [Conclusion](#conclusion)
- [References](#references)

---

## Prerequisites

- A **Debian 12 (Bookworm)** instance on Google Cloud Compute Engine with sudo privileges.
- SSH access to the instance.
- Internet connectivity to download packages.
- Basic knowledge of command-line operations.

---

## Step 1: Update System Packages

Begin by updating the package lists and upgrading existing packages to their latest versions.

```bash
sudo apt update
sudo apt upgrade -y
```

---

## Step 2: Disable Swap

Kubernetes requires swap to be disabled.

```bash
sudo swapoff -a
```

To make this change permanent, comment out any swap entries in the `/etc/fstab` file.

```bash
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
```

---

## Step 3: Install Container Runtime (containerd)

Kubernetes requires a container runtime to run containers. We'll install **containerd**, which is a high-performance container runtime compatible with Kubernetes.

### 1. Install Required Packages

```bash
sudo apt install -y ca-certificates curl gnupg lsb-release
```

### 2. Add Docker's Official GPG Key

```bash
sudo mkdir -p /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

### 3. Add Docker's Repository

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### 4. Update Package Lists

```bash
sudo apt update
```

### 5. Install containerd

```bash
sudo apt install -y containerd.io
```

### 6. Configure containerd

Create a default configuration file for containerd.

```bash
sudo mkdir -p /etc/containerd

sudo containerd config default | sudo tee /etc/containerd/config.toml >/dev/null
```

Set the `SystemdCgroup` to `true` to ensure compatibility with Kubernetes.

```bash
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml
```

### 7. Restart and Enable containerd

```bash
sudo systemctl restart containerd
sudo systemctl enable containerd
```

### 8. Verify containerd Status

```bash
sudo systemctl status containerd
```

You should see that the service is **active (running)**.

---

## Step 4: Install Kubernetes Components

We'll install Kubernetes version **1.30**, the latest stable release as per the Kubernetes documentation.

### 1. Install Required Packages

```bash
sudo apt-get install -y apt-transport-https ca-certificates curl gpg
```

### 2. Add Kubernetes Signing Key

Create the directory for apt keyrings if it doesn't exist.

```bash
sudo mkdir -p -m 755 /etc/apt/keyrings
```

Download the public signing key for the Kubernetes package repositories.

```bash
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```

### 3. Add Kubernetes Repository

Add the Kubernetes apt repository for version 1.30.

```bash
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

### 4. Update Package Lists

```bash
sudo apt-get update
```

### 5. Install Kubernetes Components

```bash
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

### 6. Enable kubelet Service

```bash
sudo systemctl enable --now kubelet
```

---

## Step 5: Initialize the Kubernetes Cluster

### 1. Configure sysctl for Kubernetes Networking

Create a configuration file for sysctl.

```bash
cat <<EOF | sudo tee /etc/sysctl.d/kubernetes.conf
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF
```

Reload sysctl settings.

```bash
sudo sysctl --system
```

### 2. Find Your Node's Internal IP Address

To initialize the Kubernetes cluster, you need to specify your node's internal IP address. This IP address is used by the Kubernetes API server to communicate with other nodes.

**Run the following command:**

```bash
ip addr show
```

**Sample Output:**

```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 ...
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ens4: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 ...
    inet 10.128.0.24/32 brd 10.128.0.24 scope global dynamic ens4
       valid_lft 86397sec preferred_lft 86397sec
```

- **Identify the network interface connected to your network.** On Google Cloud, this is typically `ens4`.
- **Note the IP address assigned to this interface.** In this example, it's `10.128.0.24`.

Alternatively, you can use:

```bash
YOUR_INTERNAL_IP=$(hostname -I | awk '{print $1}')
echo "Internal IP Address: $YOUR_INTERNAL_IP"
```

This command will automatically extract your internal IP address and store it in the `YOUR_INTERNAL_IP` variable.

### 3. Initialize the Cluster Using kubeadm

Use the internal IP address you obtained in the previous step.

```bash
sudo kubeadm init --apiserver-advertise-address=$YOUR_INTERNAL_IP --pod-network-cidr=192.168.0.0/16
```

**Example:**

If your internal IP address is `10.128.0.24`, run:

```bash
sudo kubeadm init --apiserver-advertise-address=10.128.0.24 --pod-network-cidr=192.168.0.0/16
```

- `--apiserver-advertise-address` specifies the IP address the API Server will advertise to members of the cluster.
- `--pod-network-cidr` is the CIDR range for the pod network (using Calico's default).

**Note:** During initialization, `kubeadm` will output commands and tokens required to join additional nodes. Save this information if you plan to add nodes later.

### 4. Set Up Local `kubectl` Access

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

---

## Step 6: Set Up Pod Network (Calico)

Install a pod network add-on so that pods can communicate with each other.

### 1. Install Calico Network Plugin

Apply the Calico manifest to set up networking for your cluster.

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml
```

**Expected Output:**

```
poddisruptionbudget.policy/calico-kube-controllers created
serviceaccount/calico-kube-controllers created
serviceaccount/calico-node created
serviceaccount/calico-cni-plugin created
configmap/calico-config created
...
deployment.apps/calico-kube-controllers created
```

### 2. Verify All Pods are Running

It may take a few minutes for all pods to be up and running.

```bash
kubectl get pods --all-namespaces
```

Ensure that all pods have the status `Running`.

---

## Step 7: Deploy a Hello World Application

Now that the cluster is set up, deploy a simple Nginx application to test functionality.

### 1. Remove Master Node Taint (Optional for Single-Node Cluster)

By default, Kubernetes prevents pods from being scheduled on master nodes. Since this is a single-node cluster, remove the taint to allow scheduling.

```bash
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
```

### 2. Create a Deployment

```bash
kubectl create deployment hello-world --image=nginx
```

### 3. Expose the Deployment as a Service

```bash
kubectl expose deployment hello-world --type=NodePort --port=80
```

### 4. Retrieve the Service Details

```bash
kubectl get services
```

**Sample Output:**

```
NAME          TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
hello-world   NodePort    10.96.108.201    <none>        80:31234/TCP   1m
```

Note the `NodePort` assigned to the `hello-world` service (e.g., `PORT(S): 80:31234/TCP`).

### 5. Allow Traffic to the NodePort in Google Cloud Firewall

Create a firewall rule to allow traffic to the NodePort range (30000-32767).

```bash
gcloud compute firewall-rules create allow-nodeport --allow tcp:30000-32767
```

### 6. Find Your Instance's External IP Address

```bash
curl -s http://whatismyip.akamai.com/
```

Alternatively, you can find it in the Google Cloud Console under **VM instances**.

### 7. Access the Nginx Welcome Page

Open a web browser and navigate to:

```
http://EXTERNAL_IP_ADDRESS:NODE_PORT
```

- Replace `EXTERNAL_IP_ADDRESS` with your instance's external IP.
- Replace `NODE_PORT` with the port number obtained earlier (e.g., `31234`).

You should see the Nginx welcome page.

---

## Conclusion

You have successfully set up a Kubernetes cluster using **kubeadm** on **Debian 12** running on Google Cloud Compute Engine and deployed a "Hello World" application to verify its functionality. 

---

## References

- [Kubernetes Documentation - Installing kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)
- [Kubernetes Documentation - Creating a Cluster with kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)
- [Calico Networking and Network Policy](https://projectcalico.docs.tigera.io/getting-started/kubernetes/self-managed-onprem/onpremises)
- [Containerd Documentation](https://containerd.io/docs/)
- [Docker Documentation - Install on Debian](https://docs.docker.com/engine/install/debian/)
- [Google Cloud Firewall Rules](https://cloud.google.com/vpc/docs/using-firewalls)

---

Feel free to contribute to this guide by submitting issues or pull requests.
