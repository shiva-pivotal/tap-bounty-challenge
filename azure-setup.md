# Azure Setup

# Create an cluster AKS to install tap on
Login to Azure
```
az login
az account set 
  --subscription 0ee6e882-35d9-4b20-9126-XXXXXXXXX
Check the versions of k8s available and the quotas for your nodes. You may need to request
a quota increase if you don’t have enough in a specific  family.
az aks get-versions --location ${AKS_CLUSTER_LOCATION} -o table 
az vm list-usage --location ${AKS_CLUSTER_LOCATION}  -o table
```

Set some variables to use:
```
export AKS_RESOURCE_GROUP="tap-cluster-2-resourcegroup"
export AKS_CLUSTER_NAME="tap-cluster-2"
export AKS_CLUSTER_LOCATION="eastus2"
export AKS_CLUSTER_VERSION="1.23.3"
```

Create the group and aks cluster
```
az group create -l ${AKS_CLUSTER_LOCATION} -n ${AKS_RESOURCE_GROUP}
```

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
```

Get Credentials
```
az aks get-credentials --resource-group ${AKS_RESOURCE_GROUP} --name ${AKS_CLUSTER_NAME}
```
