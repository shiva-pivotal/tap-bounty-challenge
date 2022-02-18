## Deploy Sample application 

**[Prerequisites](prereqs.md)**

[1. Create an AKS Cluster](azure-setup.md)<br>
[2. Install Tanzu tools](tanzu-tools-setup.md)<br>
[3. Install TAP](tap-install.md)<br>
[4. Sample App](sample-app.md)<br>
[5. Tips and Tricks](tap-tips-and-tricks-install.md)<br>

See the steps to deploy and test [sample application](#tap-sample-app). You can refer [Deploy Application documentation](https://docs.vmware.com/en/Tanzu-Application-Platform/1.0/tap/GUID-getting-started.html) for further details.

### Scaffold out a DotNet app 
Create a dotnet application and commit it to a git repository. How you create the app really doesn't matter but make sure that the proj (ie *.csproj) is in the root of the directory. The file is needed for cloudnative buildpacks to build dotnet by default (without additional config). Below is an example of configuring with the dotnet cli. 

Download and install dotnet on you your computer. Follow online docs, to do this.

<!-- /* cSpell:disable */ -->

scaffold out a dotnet application:
```
dotnet new webapi --no-https -n dotnet-weatherforecast
```


### Commit to a repo
Add this code to a git repo:
```
cd dotnet-weatherforecast
git init
git remote add origin https://github.com/wesreisz/dotnet-weatherforecast.git
git add . && git commit -m "initial commit"
```

### Add the app to TAP
Add dotnet app to TAP
```
export app_name=dotnet-weatherforecast
export app_git_url=https://github.com/wesreisz/dotnet-weatherforecast
```

Validate the settings are there.
```
env | grep app_
app_name=dotnet-weatherforecast
app_git_url=https://github.com/wesreisz/dotnet-weatherforecast
```

Make sure there isn't an existing app of the same name. Delete it if need be,
```
tanzu apps workload list -n workload
tanzu apps workload delete dotnet-weatherforecast -n workload
```

Create the workload. You can run this command with `--dry-run` first to preview before creating
```
tanzu apps workload create ${app_name} \
--git-repo ${app_git_url} \
--git-branch main \
--type web \
--label app.kubernetes.io/part-of=${app_name}  \
--yes \
--namespace workload 
```

Tail the app deployment logs to watch kpack build your image
```
tanzu apps workload tail   -n workload ${app_name} --since 10m --timestamp
```

Check/monitor the status of the apps
```
tanzu apps workload list
tanzu apps workload get ${app_name} -n workload

#get app url 
kubectl get all -A | grep route.serving.knative
NAME                                             STATUS      RESTARTS   AGE
dotnet-weatherforecast-build-1-build-pod         Succeeded   0          81m
dotnet-weatherforecast-config-writer-k964k-pod   Succeeded   0          79m
```


Test the application by calling the url from above. Make sure to add /weatherforecast for this app to resolved. Otherwise, you'll get a 404.
```
curl http://dotnet-weatherforecast.tap-build.wesleyreisz.com/weatherforecast | jq
[
  {
    "date": "2022-02-19T16:53:18.6094559+00:00",
    "temperatureC": 42,
    "temperatureF": 107,
    "summary": "Scorching"
  },
  {
    "date": "2022-02-20T16:53:18.6094829+00:00",
    "temperatureC": -19,
    "temperatureF": -2,
    "summary": "Cool"
  },
  {
    "date": "2022-02-21T16:53:18.6094833+00:00",
    "temperatureC": 34,
    "temperatureF": 93,
    "summary": "Cool"
  },
  {
    "date": "2022-02-22T16:53:18.6094834+00:00",
    "temperatureC": -8,
    "temperatureF": 18,
    "summary": "Sweltering"
  },
  {
    "date": "2022-02-23T16:53:18.6094836+00:00",
    "temperatureC": 50,
    "temperatureF": 121,
    "summary": "Scorching"
  }
]

```
