## datou-helm
---

#### Summary

Helm is a package manager for Kubernetes that facilitates deploying applications. In this project, you will learn the minimum basics needed so you know how to find and install a third party helm chart package. In addition, you will inspect the structure of the chart so you can author your own for deployment.

#### Pre-requisites

| Application | Version | Description | Command |
| -- | -- | -- | -- |
| Minikube | v1.16.0 | Use the datou-k8 project | `git clone git@github.com:datou-tech/datou-k8.git` |
| Helm | 3.5.0 | K8 package manager | `brew install helm` |

#### Basics

| Term | Description | 
| -- | -- |
| Chart | A bundle that contains all the resources needed to deploy application into cluster |
| Repository | A location where helm packages can be shared. |

#### Using Helm Repositories

In this section, you will add a public helm repo, search it and install one its packages.

1. Make sure minikube is running - `minikube start`
1. Install Helm's stable repository - `helm repo add stable https://charts.helm.sh/stable`
1. View all charts in the repository - `helm search repo stable`

#### Installing a Chart

Let's go find a helm package for one of the applications we will be installing to our cluster in the datou series - Prometheus. This application facilitates instrumentation and monitoring in the cluster.

1. Install the repo - `helm repo add prometheus-community https://prometheus-community.github.io/helm-charts`
1. Search the charts in this repo - `helm search repo prometheus-community stable`
1. Install the main chart `helm install prometheus prometheus-community/prometheus`
1. Check the installation - `kubectl get po`
```
    > kubectl get po
    NAME                                            READY   STATUS    RESTARTS   AGE
    prometheus-alertmanager-58b5b9d6d8-2knxb        2/2     Running   0          64s
    prometheus-kube-state-metrics-95d956569-74kqz   1/1     Running   0          64s
    prometheus-node-exporter-h24rg                  1/1     Running   0          64s
    prometheus-pushgateway-5d6884b99-92l56          1/1     Running   0          64s
    prometheus-server-6b687c4b87-88x9z              2/2     Running   0          64s
```

#### Deleting a Chart

1. Delete the chart `helm delete prometheus`
1. Check the resources/pods are gone `kubectl get po`

#### Inspecting a Chart

Helm follows a convention for folder structure

```
    /mychart
        .helmignore             # files to ignore when packaging
        _helper.tpl             # a file with helper functions
        Chart.yaml              # top level chart definition
        values.yaml             # env values to pass into the templates
        dev.yaml                # example, env file that will override default values.yaml
        /templates
            deployment.yaml     # a K8 deployment resource definition
            service.yaml        # a K8 service resource definition
```

#### Authoring Charts

When authoring charts - follow the folder/file conventions in the previous section. Here are some helpful commands:

| Command | Description |
| -- | -- |
| `helm lint .` | Check for any issues before packaging |
| `helm install -f dev.yaml` | Install helm chart with overridden values for dev |
| `helm install my-app . --dry-run --debug` | Dry run will allow you see what values will be installed | 

#### Helm Template Language Basics

In this section, you will learn the main syntax used to write dynamic templates. Helm templates have limited but powerful language constructs to make 

| Example | Explanation |
| -- | -- |
| {{ }} | Helm template dynamic functionality is encapsulated in double curly brackets |
| {{ .Release.Name }} | Variables start with a preceding period `.` |
| {{ .Values.someVariable }} | `Release` and `Values` are reserved keywords that reference values in Chart.yaml and values.yaml, respectively |
| {{ quote .Values.someVariable }} | Example of a function call in helm. This will yield someVariable passed in from values.yaml and put quotes around it. |
| {{ .Values.someVariable \| quote }} | Example of pipeline syntax. Similar to Linux command line chaining of commands piped into the next command. |
| AND MORE | Reference the helm [documentation](https://helm.sh/docs/chart_template_guide/function_list/) here to find out more about core functions in Helm |  
