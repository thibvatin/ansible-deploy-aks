# ansible-deploy-aks

## Introduction
Ansible deployment of AKS

## Information
Two playbooks to deploy and configure Azure Kubernetes Service.

Includes the following in Azure:
* AKS Cluster (RBAC)
* ContainerInsights
* DNS Zone (to be configured with external-dns)
* Azure Container Registry
* Not implemented: Azure AD Configuration

Includes the following in Kubernetes:
* Istio
* cert-manager
* external-dns
* goldpinger
* ark (velero)

## Configure the following files
* [deploy-aks/group_vars/all.yml](deploy-aks/group_vars/all.yml)
* [configure-aks/group_vars/all.yml](configure-aks/group_vars/all.yml)
* [configure-aks/roles/configure-aks/defaults/main.yml](configure-aks/roles/configure-aks/defaults/main.yml)

## How to run

### TODO: Create Azure AD Application (isn't implemented as of now)
Follow this guide: [Integrate Azure Active Directory with Azure Kubernetes Service](https://docs.microsoft.com/en-us/azure/aks/aad-integration)

### Ansible
#### Deploy AKS
```ansible
cd deploy-aks
ansible-playbook -i hosts deploy-aks.yml -e "ansible_python_interpreter=<python>" -e AZURE_CLIENT_ID="<ClientID>" -e AZURE_SECRET='"<Secret>"' -e AZURE_SUBSCRIPTION_ID="<SubscriptionID>" -e AZURE_TENANT="<TenantID>" --flush-cache
```

~~TODO: AAD Integration: (not included as of now)~~

~~ansible-playbook -i hosts-prd deploy-aks.yml -e "ansible_python_interpreter=<python>" -e AZURE_CLIENT_ID="<ClientID>" -e AZURE_SECRET='"<Secret>"' -e AZURE_SUBSCRIPTION_ID="<SubscriptionID>" -e AZURE_TENANT="<TenantID>" -e aksAADClientAppID="<AKS AAD Client App ID>" -e aksAADServerAppID="<AKS AAD Server App ID>" -e aksAADServerAppSecret='"<AKS AAD Server App Secret>"' -e aksAADTenantID="<AKS AAD Tenant ID>" --flush-cache~~


Manual steps:
* Configure the nameservers of the domain, pointing to the zone created in the resource group. Do this before running configure-aks.

#### Configure AKS
```ansible
cd configure-aks
ansible-playbook -i hosts configure-aks.yml -e "ansible_python_interpreter=<python>" -e AZURE_CLIENT_ID="<ClientID>" -e AZURE_SECRET='"<Secret>"' -e AZURE_SUBSCRIPTION_ID="<SubscriptionID>" -e AZURE_TENANT="<TenantID>" --flush-cache
```

### Kubernetes
#### Goldpinger
```kubectl
kubectl -n goldpinger port-forward $(kubectl -n goldpinger get pod -l app=goldpinger -o jsonpath='{.items[0].metadata.name}') 8080:80
```