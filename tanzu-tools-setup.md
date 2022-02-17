# Tanzu Tools Setup

### <a id=tanzu-essential> </a> Step 1: Install tanzu cluster essentials 

Once you have an empty AKS cluster, you will need to install the essential cluster tools into it. You're basically install the kapp-controller and the secrets manager.

Download the cluster-essential tar and install it. Provide following user inputs into commands and execute them to install tanzu cluster essentials and tanzu cli into your machine.. 

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

cd $HOME/tanzu-cluster-essentials
./install.sh
```

Check that there are two controllers installed:
```
 kubectl get pods -A --field-selector metadata.namespace!=kube-system
 kapp-controller             kapp-controller-98c69584f-28kb5                        1/1     Running   0          27h
 secretgen-controller        secretgen-controller-65d5bcf94c-w847l                  1/1     Running   0          27h
```

Add kapp to your $PATH and check it:
```
sudo cp $HOME/tanzu-cluster-essentials/kapp /usr/local/bin/kapp

kapp version
app version 0.45.0

Succeeded
```

Step 2: Install/Validate that plugins have been installed

export INSTALL_BUNDLE=registry.tanzu.vmware.com/tanzu-cluster-essentials/cluster-essentials-bundle@sha256:82dfaf70656b54dcba0d4def85ccae1578ff27054e7533d08320244af7fb0343
export INSTALL_REGISTRY_HOSTNAME=registry.tanzu.vmware.com
export INSTALL_REGISTRY_USERNAME=<TANZU-NET-USER>
export INSTALL_REGISTRY_PASSWORD=<TANZU-NET-PASSWORD>


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



