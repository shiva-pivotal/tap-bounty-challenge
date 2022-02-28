## Tanzu Tools Setup

**[Prerequisites](prereqs.md)**

[1. Create an AKS Cluster](azure-setup.md)<br>
[2. Install Tanzu tools](tanzu-tools-setup.md)<br>
[3. Install TAP](tap-install.md)<br>
[4. Sample App](sample-app.md)<br>
[5. Tips and Tricks](tap-tips-and-tricks-install.md)<br>

### Install tanzu cluster essentials 

Once you have an empty AKS cluster, you will need to install the essential cluster tools into it. You're basically install the kapp and secrets controller.

Download the cluster-essential tar and install it. Provide following user inputs into commands and execute them to install tanzu cluster essentials and tanzu cli into your machine.. 

* Tanzu-Net-API-Token(refresh_token) Follow [Tanzu API Token Setup](https://tanzu.vmware.com/developer/guides/tanzu-network-gs/) for further instructions
* TANZU-NET-USER 
* TANZU-NET-PASSWORD

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
```

**Note: Something interesting. If you're familiar with TMC (when you attach a previously created cluster), several components are installed on the clusters. Things likes like pinniped for authentication or gatekeeper for OPA policy integration. In addition, kapp and secretgen-controller are installed. Exactly like this below script does. kapp is used to install apps and secretgen-controller is used for secret management.** 

If you already have it downloaded, just install:
```
cd $HOME/tanzu-cluster-essentials
./install.sh
```


Check that there are two controllers installed:
```
 kubectl get pods -A --field-selector metadata.namespace!=kube-system
 kapp-controller             kapp-controller-98c69584f-28kb5                        1/1     Running   0          27h
 secretgen-controller        secretgen-controller-65d5bcf94c-w847l                  1/1     Running   0          27h
```

Add kapp to your $PATH if it's not already there and check that you have the latest version:
```
sudo cp $HOME/tanzu-cluster-essentials/kapp /usr/local/bin/kapp

kapp version
app version 0.45.0

Succeeded
```

### Step 2: Install/Validate that plugins have been installed

Check that you have the tanzu plugins installed:
```
tanzu plugin list              
  NAME                DESCRIPTION                                                        SCOPE       DISCOVERY  VERSION  STATUS         
  login               Login to the platform                                              Standalone  default    v0.11.1  not installed  
  management-cluster  Kubernetes management-cluster operations                           Standalone  default    v0.11.1  not installed  
  package             Tanzu package management                                           Standalone  default    v0.11.1  installed      
  pinniped-auth       Pinniped authentication operations (usually not directly invoked)  Standalone  default    v0.11.1  not installed  
  secret              Tanzu secret management                                            Standalone  default    v0.11.1  installed      
  accelerator         Manage accelerators in a Kubernetes cluster                        Standalone             v1.0.1   installed      
  apps                Applications on Kubernetes                                         Standalone             v0.4.1   installed      
  services            Discover Service Types and manage Service Instances (ALPHA)        Standalone             v0.1.1   installed   
```

If you don't, install the plugins and recheck. 

```
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




