# jenkins-helm

## Prerequisites

This guide assumes that you have access to a k8s cluster where the Jenkins CI/CD tool will be installed.

If you don’t have the Helm command line interface installed and configured locally, see the sections below to Install Helm and Configure Helm.

### Install Helm

To install the Helm CLI, follow the instructions from the [Installing Helm](https://helm.sh/docs/intro/install/) page.

### Configure Helm

Once Helm is installed and set up properly, add the Jenkins repo as follows: 

```
helm repo add jenkinsci https://charts.jenkins.io

helm repo update
```

The Helm charts in the Jenkins repo can be listed with the following command:

```
helm search repo jenkinsci
```

### Configure k8s resources

### Create a namespace

A namespace will be created to store the resources associated with this Jenkins installation. To create the namespace, run the following command:

```
kubectl apply -f jenkins-namespace.yaml
```


### Create a persistent volume

A persistent volume is needed for the Jenkins controller, to prevent losing our whole configuration of the controller and any jobs when we reboot the cluster. 

To create the persistent volume, run the following command:

```
kubectl apply -f jenkins-volume.yaml
```

### Create a service account

In K8s, service accounts provide an identity for pods. Pods that want to interact with the API server will authenticate with a particular service account. By default, applications will authenticate as the __default__ service acocunt in the namespace they are running in. 

Run the following command to create an associated service account, ClusterRole & RoleBinding within the jenkins namespace.

```
kubectl apply -f jenkins-sa.yaml
```

### Install Jenkins

The file __jenkins-values.yaml__ contains all necessary overrides to configure the Jenkins installation with:

- Persistence
- Ingress
- Service Account

Also, note that the above override file, will configure a default user/passwd of: __admin:admin__ when logging into the UI.

You can now install Jenkins by running the *helm install* command:
```
helm install jenkins -n jenkins -f ./helm/jenkins-values.yaml jenkinsci/jenkins
```

NB. The override values provided in file __jenkins-values.yaml__, assume that an Ingress Controller has been installed within the cluster. 

As a suggestion, the Nginx Ingress Controller could be leveraged here using the following command:

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```

# References

[Installing Jenkins with Helm 3](https://www.jenkins.io/doc/book/installing/kubernetes/)

