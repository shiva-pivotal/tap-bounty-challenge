# Tanzu Application Platform - Install

## Overview of the Install Steps
1. [Setup Tanzu Application Platform Tools](#tanzu-essential)
2. [Install Tanzu CLI and Configure kubectl Plugins](#tanzu-cli)
2. [Setup Tanzu Application Platform package repository](#tap-package-repo)
3. [Setup Tanzu Application Platform Install](#tap-full-profile-install)
4. [App Deployment](#tap-sample-app)


### <a id=tanzu-essential> </a> Step 2: Install Tanzu cluster essentials 
Installing Cluster Essentials must happen on each new cluster. If you previously downloaded it, cd into that direction and run './install.sh`.


### <a id=tanzu-cli> </a> Step 2: Install Tanzu CLI and Configure kubectl Plugins

Provide following user inputs into commands and execute them to install tanzu cluster essentials and tanzu cli into your mac machine. 

* Tanzu-Net-API-Token(refresh_token) Follow [Tanzu API Token Setup](https://tanzu.vmware.com/developer/guides/tanzu-network-gs/) for further instructions
* TANZU-NET-USER 
* TANZU-NET-PASSWORD

<!-- /* cSpell:disable */ -->
```
#login to kubernetes workload cluster using cluster config
kubectl config get-contexts
kubectl config use-context <cluster config name>

#install pivnet/tanzunet cli
brew install pivotal/tap/pivnet-cli
pivnet version

#login to Tanzu net (https://network.tanzu.vmware.com/). And get API token from profile page.
pivnet login --api-token=<Tanzu-Net-API-Token>

#install tanzu cluster essential(mac)
pivnet download-product-files --product-slug='tanzu-cluster-essentials' --release-version='1.0.0' --product-file-id=1105820
mkdir $HOME/tanzu-cluster-essentials
tar -xvf tanzu-cluster-essentials-darwin-amd64-1.0.0.tgz -C $HOME/tanzu-cluster-essentials

export INSTALL_BUNDLE=registry.tanzu.vmware.com/tanzu-cluster-essentials/cluster-essentials-bundle@sha256:82dfaf70656b54dcba0d4def85ccae1578ff27054e7533d08320244af7fb0343
export INSTALL_REGISTRY_HOSTNAME=registry.tanzu.vmware.com
export INSTALL_REGISTRY_USERNAME=<TANZU-NET-USER>
export INSTALL_REGISTRY_PASSWORD=<TANZU-NET-PASSWORD>
cd $HOME/tanzu-cluster-essentials
./install.sh

sudo cp $HOME/tanzu-cluster-essentials/kapp /usr/local/bin/kapp

cd $HOME

#install tanzu cli v(0.10.0) and plug-ins (linux)
mkdir $HOME/tanzu
pivnet download-product-files --product-slug='tanzu-application-platform' --release-version='0.10.0' --product-file-id=1080177
tar -xvf tanzu-framework-darwin-amd64.tar -C $HOME/tanzu
cd $HOME/tanzu
sudo install cli/core/v0.10.0/tanzu-core-darwin_amd64  /usr/local/bin/tanzu
tanzu version

cd $HOME/tanzu
export TANZU_CLI_NO_INIT=true
tanzu plugin install --local cli all
tanzu plugin list

cd $HOME
```

<!-- /* cSpell:enable */ -->
### <a id=tap-package-repo> </a>Step 3: Add the Tanzu Application Platform package repository

Execute following commands to add TAP package. 

<!-- /* cSpell:disable */ -->
```
kubectl create ns tap-install

#tanzu registry secret creation
tanzu secret registry add tap-registry \
  --username "${INSTALL_REGISTRY_USERNAME}" --password "${INSTALL_REGISTRY_PASSWORD}" \
  --server "${INSTALL_REGISTRY_HOSTNAME}" \
  --export-to-all-namespaces --yes --namespace tap-install

#tanzu repo add
tanzu package repository add tanzu-tap-repository \
  --url registry.tanzu.vmware.com/tanzu-application-platform/tap-packages:1.0.0 \
  --namespace tap-install

tanzu package repository get tanzu-tap-repository --namespace tap-install

#tap available package list
tanzu package available list --namespace tap-install

```

<!-- /* cSpell:enable */ -->

### <a id=tap-full-profile-install> </a>Step 4: Install Tanzu Application Platform full profile

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
<!-- /* cSpell:enable */ -->

### <a id=tap-sample-app>Step 5: Deploy Sample application 
See the steps to deploy and test [sample application](#tap-sample-app). You can refer [Deploy Application documentation](https://docs.vmware.com/en/Tanzu-Application-Platform/1.0/tap/GUID-getting-started.html) for further details.

Execute following command to see the demo of sample app deployment into Tanzu Application Platform

<!-- /* cSpell:disable */ -->
```
# login to kubernetes workload build cluster 
kubectl config get-contexts
kubectl config use-context <cluster config name>

export app_name=tap-demo
export git_app_url=https://github.com/sample-accelerators/spring-petclinic

tanzu apps workload delete --all

tanzu apps workload list

#generate work load yml file
tanzu apps workload create ${app_name} --git-repo ${git_app_url} --git-branch main --type web --label app.kubernetes.io/part-of=${app_name} --yes --dry-run


#create work load for app
tanzu apps workload create ${app_name} \
--git-repo ${git_app_url} \
--git-branch main \
--git-tag tap-1.0 \
--type web \
--label app.kubernetes.io/part-of=${app_name} \
--yes

#app deployment logs
tanzu apps workload tail ${app_name} --since 10m --timestamp

#get app workload list
tanzu apps workload list

#get app details
tanzu apps workload get ${app_name}

#saved deliverables yaml configuration into local directory. check below sample file below 
kubectl get deliverables ${app_name} -o yaml |  yq 'del(.status)'  | yq 'del(.metadata.ownerReferences)' | yq 'del(.metadata.resourceVersion)' | yq 'del(.metadata.uid)' >  ${app_name}-delivery.yaml"

#sample deliverables 
##################################################
apiVersion: carto.run/v1alpha1
kind: Deliverable
metadata:
  creationTimestamp: "2022-02-01T20:19:19Z"
  generation: 1
  labels:
    app.kubernetes.io/component: deliverable
    app.kubernetes.io/part-of: tap-demo
    app.tanzu.vmware.com/deliverable-type: web
    carto.run/cluster-supply-chain-name: source-to-url
    carto.run/cluster-template-name: deliverable-template
    carto.run/resource-name: deliverable
    carto.run/template-kind: ClusterTemplate
    carto.run/workload-name: tap-demo
    carto.run/workload-namespace: default
  name: tap-demo
  namespace: default
spec:
  source:
    image: tapdemo2.azurecr.io/supply-chain/tap-demo-default-bundle:83c468d4-4fd0-4f3b-9e57-9cdfe57e730a

##################################################################

 # login to kubernetes workload run cluster 
kubectl config get-contexts
kubectl config use-context <cluster config name>  

#apply app-delivery into run cluster 

kubectl apply -f app-delivery.yaml

#check app status
kubectl get deliverables ${app_name}

#get app url 
kubectl get all -A | grep route.serving.knative

#copy  app url and paste into browser to see the sample app

```
<!-- /* cSpell:enable */ -->