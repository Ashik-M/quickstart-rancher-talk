# quickstart-rancher-talk
CNCF meetup- talk - how to quickly set up rancher for having multi-cloud managed kubernetes deployments with unified control plane

Introduction to Rancher Environment¶
The Rancher UI is a web-based user interface for the Rancher platform which is an open-source container management platform. It provides a graphical interface for managing Kubernetes clusters, deploying and managing applications, and monitoring cluster and application health.

You'll be working primarily in the Rancher UI to run the labs in this course. You'll also be taken through some demos of various Kubernetes and cloud native tools like ArgoCD, Prometheus and Grafana using this UI environment.

Logging in to Rancher UI¶
To log in to Rancher, follow these steps:

Open your web browser and navigate to the Rancher URL provided to you.

Enter your username and password in the login screen : Use this link with Google Docs to find your username

![image](https://github.com/user-attachments/assets/8a14fd68-a604-4b44-8f39-5c105d09b36a)


After successfully logging in, you'll be taken to the Rancher Cluster Dashboard, where you can view the workshop cluster you'll be working with. You'll also have access to your project and namespaces.

![image](https://github.com/user-attachments/assets/f290fd66-1a41-4de0-891e-edb7985de704)

Overview of the UI Dashboard¶
Cluster Explorer¶
The Cluster Explorer offers a user-friendly way to view, create, modify and remove Kubernetes resources.

You can access your resources using the sidebar located to the left of the Cluster Explorer page.

![image](https://github.com/user-attachments/assets/4053b339-95d3-4a64-828b-3ca81ba6600b)

You can use either the Kubectl or the forms in Cluster Explorer to deploy and expose your applications depending on your preference. But for the purpose of this workshop, you'll use the kubectl shell provided as part of the Rancher UI setup.

The Cluster Dashboard, which is also part of the Cluster Explorer page gives you an overview of the total resources, nodes and deployments in the cluster as well as some metrics such as CPU used, Memory used and the number of pods deployed in the cluster.

![image](https://github.com/user-attachments/assets/3f39f029-3853-4c3f-bba8-f02631c51217)

In Cluster Explorer, you can filter your view of your resources by namespaces and also by projects.

![image](https://github.com/user-attachments/assets/b690e107-8204-4eb3-b544-f071a14fb6eb)

Kubectl Shell¶
In this workshop, you'll use the built-in Rancher Kubectl Shell for your labs. The Cluster Explorer provides you direct and easy access to Kubectl command line through the navigation bar at the top of the screen.

![image](https://github.com/user-attachments/assets/cd4f02ef-5e9e-40ff-a599-c17fc16b55e5)
To open the kubectl shell, click on the kubectl shell button which is located to the right of the Cluster Explorer at the top of the screen.

![image](https://github.com/user-attachments/assets/940510da-31b2-4bc4-96b6-2269e4df1c1a)


> [!WARNING]  
> Before you start: Set the context for the workshop. The Shell gives you temporary access to operate within the finite Namespace we have provided for you. When the shell expires or times-out, your information regarding your context dissapears and you'll be back   
> on the Default Namespace where your user has very limited access to operate. We will recommend you to keep the snippet below handy in a notepad.
  
## ​To configure the default context to use one of your custom namespaces as default instead of the default namespace, run:


    cp .kube/config ./
    export KUBECONFIG=/home/shell/config
    kubectl config set-context --current --namespace=<your-custom-namespace1>

Projects and Namespaces¶
As a user, you have a dedicated Project in the Rancher environment which has two custom Namespaces, where you'll deploy your resources as you go through the modules.

In the Rancher UI, projects and namespaces are displayed in the sidebar to the left of the screen.

![image](https://github.com/user-attachments/assets/a3c26642-c963-4b55-b1a8-91272a72bff1)


## That's it!

You have a birds-eye view of this multi-tenant platform. You can sniff-around other peoples work but try not to log-in as their user (Unless you're helping them debug something!)

