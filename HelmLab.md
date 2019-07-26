# Helm Lab

## Install minikube

Note: minikube is not working under VirtualBox. Use as a alternative micro8ks. 

1. Install Virtualbox from [here](https://www.virtualbox.org/)
2. Install kubectl from [here](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
3. Install Minikube from [here](https://github.com/kubernetes/minikube)

## Install helm
For windows users, you can install helm using chocolatey (see [here](https://helm.sh/docs/using_helm/)):
```
%% Open a command line with administrative rights
%% If chocolatey is not installed, uncomment the following line:
%% @"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
%% If minikube is not installed, uncomment the following line:
%% choco install minikube
choco install kubernetes-helm
exit
```

For linux users, you can install helm using Snap (see [here](https://helm.sh/docs/using_helm/)):

```
sudo apt-get install snapd -y
sudo snap install helm --classic
```

For macOS users, you can install helm using Homebrew (see [here](https://helm.sh/docs/using_helm/)):

```
brew install kubernetes-helm
```

## Check that helm is properly installed.
Run the following:

```
helm version 
```
Helm should be installed but the Tiller should give an error, since it is not installed yet.

## Install tiller
Run the following:

```
helm init
```

Wait some minutes and run the following:

```
helm version 
```

Wait some minutes and check that all the pods are running correctly by running:

```
kubectl get pods --all-namespaces
```

Run the following:

```
helm version 
```

Helm and tiller should be installed. 

## Update and add repositories in helm

To update the default repository (see [here](https://github.com/helm/charts/tree/master/stable)), run the following:

```
helm repo update
```

To search for a chart, for example the prometheus-operator, just run the following

```
helm search prometheus-operator
```

[Bitnami](https://bitnami.com/) has a library for helm charts as well, which might be interesting. To add a new repository to helm, run the following:

```
helm repo add bitnami https://charts.bitnami.com/bitnami
```

test with:

```
helm search bitnami
```

## Install a chart

We will install nginx from the bitnami repository, see [here](https://github.com/bitnami/charts/tree/master/bitnami/nginx)

If we are only interested in installing the repository, but it won't be modified, just uncomment and run:

```
# helm install bitnami/nginx
```

However, we usually want to make some changes. To fetch the repository, but not install it, run:

```
helm fetch --untar bitnami/nginx
```

The previous command will also uncompress the repository, although it is not needed for helm to work.


To install the repository, run the following:

```
helm install nginx
```

To check the installation procedure, run:

``` 
kubectl get pods -w
```

Once installed, run:

```
kubectl get svc
```

and 

```
minikube service <NAME_SERVICE>
```
It should return the nginx welcome page.

## Update a chart and rollback

1. Change version in Chart.yaml file
2. Open the values.yaml file and edit server block to:

``` 
serverBlock: |-
  # example PHP-FPM server block
  server {
    listen 8080;
    root /app;
    location / {
      add_header Content-Type text/plain; # Prevents download
      return 200 "hello!";
    }
  }
```  

Check the release name with the following command:

```
helm ls
```

Upgrade the helm chart as follows:

```
helm upgrade <RELEASE_NAME> nginx
```

Check the history of the release with:

```
helm history <RELEASE_NAME>
```

Image that you don't want the new version. Just roll it back using the following command:

```
helm rollback <RELEASE_NAME> 
```

# Uninstall and delete a release

The following command removes all the components installed in Kubernetes and deletes the release. 
Additionally, the option --purge removes records and make that name free to be reused for another installation.

```
helm delete --purge <RELEASE_NAME> 
```

**BE CAREFULL, WITH THE PURGE ARGUMENT YOU CANNOT ROLLBACK**

# Use a github repository to track changes and share releases

By running the following command, a compressed version of the release is created to be shared:

```
helm package nginx
```

To generate an index file based on the charts found, run:

```
helm repo index .
```

Then, add, commit and push the files to github:

```
git add .

git commit -m "First commit"

git push
```

Go to the RAW file link, should look like this **https://github.com/angelsevillacamins/helm-test/raw/master**

To add a repository to helm, run the following:

```
helm repo add myrepo https://github.com/angelsevillacamins/helm-test/raw/master
```

Check with 

```
helm repo list
```
Search for the new nginx chart added by:

```
helm search nginx
```

and install it:

```
helm install myrepo/nginx
```

Check the installation using:

```
helm ls
```
Remove the release by running the following command:
```
# helm delete --purge <RELEASE_NAME> 
```
**BE CAREFULL, WITH THE PURGE ARGUMENT YOU CANNOT ROLLBACK**

## Exercise, try to install Spark in minikube from [here](https://github.com/bitnami/charts/tree/master/bitnami/spark)
