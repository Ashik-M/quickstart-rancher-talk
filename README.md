
# Quickstart Rancher Talk

## CNCF Meetup: How to Quickly Set Up Rancher for Multi-Cloud Managed Kubernetes Deployments with a Unified Control Plane

---

### Introduction
This guide provides an overview of the Rancher environment, which is an open-source container management platform. Rancher allows you to manage Kubernetes clusters, deploy applications, and monitor their health, all through a unified interface.


---
There is a template in this repo should you want to know how to build the rancher server yousrelf [here](https://github.com/Ashik-M/quickstart-rancher-talk/blob/main/quickstart-template-rancher-registration.md)  What you'll find below is what the end-user experience should look like at a glance once you have set-everything up properly :)

In this guide, you will:
- Log in to the Rancher UI
- Navigate the UI and Cluster Explorer
- Use the built-in Kubectl shell
- Understand Projects and Namespaces

---

## Logging into the Rancher UI

To log in to Rancher, follow these steps:

1. **Navigate to the Rancher URL** provided to you in your web browser.
2. **Enter your username and password**:
   - Use the link provided in Google Docs to find your username.

![Rancher Login Screen](https://github.com/user-attachments/assets/8a14fd68-a604-4b44-8f39-5c105d09b36a)

After logging in successfully, you'll be taken to the **Rancher Cluster Dashboard** where you can view your workshop cluster. You will also have access to your projects and namespaces.

![Rancher Cluster Dashboard](https://github.com/user-attachments/assets/f290fd66-1a41-4de0-891e-edb7985de704)

---

## Overview of the Rancher UI Dashboard

### Cluster Explorer
The **Cluster Explorer** offers a user-friendly way to manage Kubernetes resources, including creating, modifying, and deleting resources.

- Access your resources through the **sidebar** located on the left side of the Cluster Explorer page.

![Cluster Explorer Sidebar](https://github.com/user-attachments/assets/4053b339-95d3-4a64-828b-3ca81ba6600b)

You can deploy and expose your applications either by using **Kubectl** or by using the forms available in Cluster Explorer. In this workshop, we will use the **Kubectl shell** provided by the Rancher UI.

The **Cluster Dashboard** also provides an overview of the health of your Kubernetes cluster, including resource utilization and the status of nodes and workloads. You can filter resources by namespaces and projects.

![Cluster Dashboard](https://github.com/user-attachments/assets/b690e107-8204-4eb3-b544-f071a14fb6eb)

---

## Using the Built-in Kubectl Shell
In this workshop, you'll use the built-in Rancher **Kubectl Shell** for running commands during the lab exercises.

- Access the **Kubectl command line** by clicking the Kubectl shell button located in the **navigation bar** at the top of the screen.

![Kubectl Shell Button](https://github.com/user-attachments/assets/cd4f02ef-5e9e-40ff-a599-c17fc16b55e5)

- Click the button to open the Kubectl shell.

![Kubectl Shell](https://github.com/user-attachments/assets/940510da-31b2-4bc4-96b6-2269e4df1c1a)

> **Warning**  
> Before you start: Set the context for the workshop. The shell provides temporary access to operate within a specific namespace. If the shell session times out, your context information will be lost, and you will be placed back in the **Default Namespace**, where access is limited. Keep the snippet below handy to reset your context.

### Configure Default Context
To set your default context to use one of your custom namespaces instead of the default namespace, run:

```bash
cp .kube/config ./
export KUBECONFIG=/home/shell/config
kubectl config set-context --current --namespace=<your-custom-namespace>
```

---

## Projects and Namespaces
As a user, you will have a dedicated **Project** in the Rancher environment that contains two custom **Namespaces**. You will use these namespaces to deploy your resources as you go through the workshop modules.

- Projects and namespaces are displayed in the **sidebar** on the left side of the Rancher UI.

![Projects and Namespaces](https://github.com/user-attachments/assets/a3c26642-c963-4b55-b1a8-91272a72bff1)

---

## Summary
You now have an overview of this multi-tenant Rancher platform. You can explore other people's work, but please do not log in as other users unless you are assisting with debugging. Enjoy exploring the Rancher environment and learning more about managing Kubernetes deployments!

