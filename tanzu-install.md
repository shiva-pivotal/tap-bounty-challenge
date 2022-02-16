# Tanzu Install

##Access Tap UI
- To access the tap ui, run the following command : `kubectl get svc -n tap-gui` , that should give you something like the below:
```
NAME     TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)          AGE
server   LoadBalancer   10.0.98.101   40.89.246.36   7000:32276/TCP   3d7h
```

You can can then add DNS entry as follow:
 Type : A
 Name: *.<ingressDomain>
 Content: <External-IP from command above> for example: 40.89.246.36 