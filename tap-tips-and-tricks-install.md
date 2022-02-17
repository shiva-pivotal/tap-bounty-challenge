# Tips and Tricks for TAP Install

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