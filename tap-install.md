## Tanzu Application Platform - Install

**[Prerequisites](prereqs.md)**

[1. Create an AKS Cluster](azure-setup.md)<br>
[2. Install Tanzu tools](tanzu-tools-setup.md)<br>
[3. Install TAP](tap-install.md)<br>
[4. Sample App](sample-app.md)<br>
[5. Tips and Tricks](tap-tips-and-tricks-install.md)<br>

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

Create a basic tap-values.yaml. Here is a sample:
```
profile: full
ceip_policy_disclosed: true # Installation fails if this is set to 'false'
buildservice:
  kp_default_repository: "harbor.example-domain.com/tap/build-service"
  kp_default_repository_username: "your-registry-username"
  kp_default_repository_password: "your-registry-username"
  tanzunet_username: "example@vmware.com"
  tanzunet_password: "tanzu-net-password"
  descriptor_name: "tap-1.0.0-full"
  enable_automatic_dependency_updates: true
supply_chain: basic

ootb_supply_chain_basic:
  registry:
    server: "harbor.example-domain.com"
    repository: "workloads/supply-chain"
  gitops:
    ssh_secret: ""

contour:
  envoy:
    service:
      type: LoadBalancer

learningcenter:
  ingressDomain: "learningcenter.example-domain.com"

tap_gui:
  service_type: ClusterIP
  ingressEnabled: "true"
  ingressDomain: "tap.example-domain.com"
  app_config:
    app:
      baseUrl: http://tap-gui.tap.example-domain.com
    catalog:
      locations:
        - type: url
          target: https://github.com/wesreisz/tap-blank-catalog/blob/main/catalog-info.yaml
    backend:
      baseUrl: http://tap-gui.tap-build.example-domain.com
      cors:
        origin: http://tap-gui.tap-build.example-domain.com

metadata_store:
  app_service_type: LoadBalancer # (optional) Defaults to LoadBalancer. Change to NodePort for distributions that don't support LoadBalancer

grype:
  namespace: "workload" # (optional) Defaults to default namespace.
  targetImagePullSecret: "workload"

cnrs:
  provider: local
  domain_name: tap.example-domain.com 
  domain_template: "{{.Name}}.{{.Domain}}"

# e.g. App Accelerator specific values go under its name
accelerator:
  server:
    service_type: "ClusterIP"
```

Apply the yaml file and install tap
```
tanzu package install tap -p tap.tanzu.vmware.com -v 1.0.1 --values-file tap-values.yaml -n tap-install
```

Check all cluster package installed succesfully
```
tanzu package installed list -A
```


Got back and add the domains to your dns provider:
```
 tap.example-domain.com
 tap-gui.tap.example-domain.com
 learningcenter.example-domain.com
```

NOTE: Learning center and tap require wildcard DNS entries.


