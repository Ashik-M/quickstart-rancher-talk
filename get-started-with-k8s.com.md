
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

## Running Kubernetes with Minikube

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
1. Start a Kubernetes cluster:
   ```bash
   minikube start
   ```

2. Verify that Minikube is running:
   ```bash
   minikube status
   ```

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

4. Visit the URL in your browser to confirm the "Hello World" container is running.

---

## Running Kubernetes with Kubeadm

### Installing Kubeadm
Kubeadm helps you bootstrap a Kubernetes cluster on your local machine.

- **Linux** and **macOS**:
  1. Install Docker:
     - **Linux**: [Docker Installation Guide](https://docs.docker.com/engine/install/)
     - **macOS**: Install Docker Desktop from [here](https://www.docker.com/products/docker-desktop/).

  2. Install `kubeadm`, `kubelet`, and `kubectl`:
     - **Linux**:
       ```bash
       sudo apt-get update && sudo apt-get install -y apt-transport-https curl
       curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
       echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
       sudo apt-get update
       sudo apt-get install -y kubelet kubeadm kubectl
       sudo apt-mark hold kubelet kubeadm kubectl
       ```

     - **macOS**:
       Using Homebrew:
       ```bash
       brew install kubectl kubeadm kubelet
       ```

For more details, visit the [Kubeadm Documentation](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/).

### Initialize the Kubernetes Cluster
1. Initialize the control plane (for Linux, you may need to disable swap):
   ```bash
   sudo kubeadm init
   ```

2. Set up `kubectl` for your user:
   ```bash
   mkdir -p $HOME/.kube
   sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   sudo chown $(id -u):$(id -g) $HOME/.kube/config
   ```

3. Deploy a Pod network (required for the cluster to function):
   ```bash
   kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
   ```

4. Check the node status to confirm it's running:
   ```bash
   kubectl get nodes
   ```

### Deploy a Hello World Container on Kubeadm
1. Create a deployment using the `nginx` image:
   ```bash
   kubectl create deployment hello-kubeadm --image=nginx
   ```

2. Expose the deployment as a service:
   ```bash
   kubectl expose deployment hello-kubeadm --type=NodePort --port=80
   ```

3. Get the external IP or port to access the service:
   ```bash
   kubectl get services
   ```

4. Use `curl` or a browser to visit the service and confirm it's running.

---

## Conclusion
You have now successfully set up Kubernetes using Minikube and Kubeadm, and deployed a simple "Hello World" container to test both environments. Explore more about Kubernetes in the [Kubernetes Documentation](https://kubernetes.io/docs/home/).
