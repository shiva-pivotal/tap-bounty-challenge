### Deploy Sample application 
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