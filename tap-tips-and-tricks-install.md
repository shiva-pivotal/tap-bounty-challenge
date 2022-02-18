## Tips and Tricks for TAP Install

**[Prerequisites](prereqs.md)**

[1. Create an AKS Cluster](azure-setup.md)<br>
[2. Install Tanzu tools](tanzu-tools-setup.md)<br>
[3. Install TAP](tap-install.md)<br>
[4. Sample App](sample-app.md)<br>
[5. Tips and Tricks](tap-tips-and-tricks-install.md)<br>

- Extract friendly error messages : ```kubectl describe pkgi <package name> -n tap-install ``` for example ```kubectl describe pkgi grype -n tap-install ```
- Setup DNS:
    - Extract the external IP from ```kubectl get svc -n tanzu-system-ingress```
    - Add DNS Record :
        - Type : A Record
        - Name: *.ingressdomain
        - Content: External IP Address from command above ```kubectl get svc -n tanzu-system-ingress```
- Setup Contour: kubectl get svc -n tanzu-system-ingress
- Setup GIT-CATALOG-URL
    - Download  tap-gui-yelb-catalog.tgz from Pivnet (https://network.pivotal.io/products/tanzu-application-platform/#/releases/1049494/file_groups/6091)
    - Extract and place in a public Git Repo (i.e. https://github.com/kadourah/tap-install)
    - Update tap-values to
         ```
             catalog:
              locations:
                - type: url
                  target: GIT-CATALOG-URL/catalog-info.yaml
         ```
- Troubleshood training center not loading, or stuck on loading 
	- Access http://learning-center-guided.learningcenter.<tap-domain> then click on "Workshop building tutorial"
	- if it's stuck on loading, try the following:
	- ```kubectl delete pod -l deployment=learningcenter-operator -n learningcenter```
	- ```kubectl delete trainingportals  learning-center-guide```
	- Kapp controller should recreate the trainingportal after some minutes (Less than 10 minutes)
	- Try again