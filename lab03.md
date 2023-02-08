# Kubernetes 

> https://learn.microsoft.com/en-us/azure/aks/learn/quick-kubernetes-deploy-cli

---
## Create Cluster 

Follow the walkthrough (Class Video) to create cluster on **web portal** or run the comands below.

    az group create --name tstrg --location eastus

    mkdir .ssh

    ssh-keygen -f .ssh/aks-ssh

    az aks create --resource-group tstrg --name myAKSCluster --ssh-key-value .ssh/aks-ssh.pub --node-count 1

### Check Cluster 

Use the command before and after cluster creation to see the state of your cluster 

    kubectl config get-contexts

    kubectl config get-clusters

    az aks get-credentials --resource-group <Resource Group Name> --name <Kubernetes Cluster Name>

    kubectl config get-clusters

    kubectl get pods


## Create app.yml file

The app.yml file should be created in visual cope the template code from https://learn.microsoft.com/en-us/azure/aks/learn/quick-kubernetes-deploy-cli

> Apply the code with the following commands

    kubectl apply -f app.yml

    kubectl get services

> The external IP exposed by services can be used to see the application in action.

---
