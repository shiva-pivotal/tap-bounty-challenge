## Tips and Tricks for TAP Install

**[Prerequisites](prereqs.md)**

[1. Create an AKS Cluster](azure-setup.md)<br>
[2. Install Tanzu tools](tanzu-tools-setup.md)<br>
[3. Install TAP](tap-install.md)<br>
[4. Sample App](sample-app.md)<br>
[5. Tips and Tricks](tap-tips-and-tricks-install.md)<br>

- Extract friendly error messages 
- Setup DNS
- Setup Contour: kubectl get svc -n tanzu-system-ingress
- Troubleshoot catalog errors
- Setup GIT-CATALOG-URL
 - Download  tap-gui-yelb-catalog.tgz
 - Extract and place in a public Git Repo (i.e. https://github.com/kadourah/tap-install)
 - Update tap-values to
 ```
     catalog:
      locations:
        - type: url
          target: GIT-CATALOG-URL/catalog-info.yaml
 ```