# Tanzu Application Platform - Install


<!-- /* cSpell:enable */ -->
### Add the Tanzu Application Platform package repository

Execute following commands to add TAP package. 

Create two namespaces. One for tap-install itself and another for where your workloads will be created:
```
kubectl create ns tap-install
kubectl create ns workload
```

Create a secret to be used for accessing the tanzu registry
```
export INSTALL_REGISTRY_HOSTNAME=registry.tanzu.vmware.com
export INSTALL_REGISTRY_USERNAME='example@vmware.com'
export INSTALL_REGISTRY_PASSWORD='<MYPASSWORD>'

#tanzu registry secret creation
tanzu secret registry add tap-registry \
  --username "${INSTALL_REGISTRY_USERNAME}" --password "${INSTALL_REGISTRY_PASSWORD}" \
  --server "${INSTALL_REGISTRY_HOSTNAME}" \
  --export-to-all-namespaces --yes --namespace tap-install
```  

Add the tanzu package repository where the TAP packages are stored.
```
#tanzu repo add
tanzu package repository add tanzu-tap-repository \
  --url registry.tanzu.vmware.com/tanzu-application-platform/tap-packages:1.0.0 \
  --namespace tap-install

tanzu package repository get tanzu-tap-repository --namespace tap-install
```

Validate that you can get the package list:
```
#tap available package list
tanzu package available list --namespace tap-install
```

### Install Tanzu Application Platform full profile

Provide following user inputs to set environments variables into commands and execute them to install build profile

* registry_server - uri of registry server like Azure container registry or Harbor etc. (example - tappoc.azurecr.io)
* registry_user - registry server user
* registry_password - registry server user

<!-- /* cSpell:disable */ -->
```
export registry_server=<registry server uri>
export registry_user=<registry_user>
export registry_password=<registry_password>

#create a tap-values.yaml


tanzu package install tap -p tap.tanzu.vmware.com -v 1.0.1 --values-file tap-values.yaml -n tap-install
tanzu package installed get tap -n tap-install

#check all cluster package installed succesfully
tanzu package installed list -A

```

