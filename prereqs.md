# Prerequisites

The following software needs to be available in the environment where you will be installing your Tanzu Application Platform on Azure
- Azure Account with proper permissions to provision AKS cluster
- Disable agentpool Autoscaling
- Set Node pool capacity to the following:
- 10 Node count
- VM Size: Standard DS2 v2 (2 vcpus, 7 GiB memory)
- Cores : 20 vCPUs
- Memory: 70 GiB
- This is encapsulated in the script

```
az aks create \
 --resource-group ${AKS_RESOURCE_GROUP} \
 --name ${AKS_CLUSTER_NAME} \
 --node-count 5 \
 --generate-ssh-keys \
 --load-balancer-sku standard \
 --node-vm-size Standard_D4_v3 \
 --enable-addons monitoring \
 --kubernetes-version ${AKS_CLUSTER_VERSION} 
Standard_D4_v3 == 4 vCPUS / 16GB Memory
```
4*5 == 20 vCPUS
16*5 == 80GB Memory

- If Installing from Windows, it's highly recommend to leverage WSL2 https://docs.microsoft.com/en-us/windows/wsl/install , it makes your job much easier.