# Custom addons for Konvoy with addon controller

Kubernetes Base Addons (KBA) provide preconfigured day two functionality and services, on top of Kubernetes, like monitoring, logging, and external DNS. KBA are patched on a bi-monthly basis. 


## Developers Info

[Konvoy 1.8 docs](https://docs.d2iq.com/dkp/konvoy/1.8/addons/)

Steal basics from [kubernetes-base-addons](https://github.com/mesosphere/kubernetes-base-addons)

High level you will need a metadata folder and a addons folder at the root of the folder.

```text
docs-addon-repo
   |- addons
   |     |- cockroachdb
   |           |- 19.2.x                     <- appVersion <major>.<minior>.x
   |                 |- cockroachdb-1.yaml   <- Addon yaml manifest, filename with revision

   |- metadata
   |     |- root.yaml                        <- Addon metadata
   |     |- static
   |           |- cockroachdb                <- folder for logo, overview documentation, ...
   |                 |- logo.svg
   |                 |- overview.md
   |- README.md
```

The `Deployments` folder and `repository.yaml` in the docs are depreciated and not used anymore.

The `repository.yaml` file does have uses for Kommander.

### metadata

`root.yaml` a File tha contains a map of all the different items in the repo. Not versions of items just the items. The folders are to organize the related items. These details could be used to show the package in the Konvoy or Kommander UIs.

### addons

Here is where all the addons are organized. 

create a folder for each repo item. Then a yaml for the Item. This yaml will contain the meta data for the repo item as well as the default values to pass to teh helm chart. all these values and subsequent values in hte helm cahrt can be overridden in the `cluster.yaml` values for each item.


`spec.kubernetes.minSupportedVersion` for setting the minimum version of kubernetes the addon can deploy to successfully.

`spec.cloudProvider.[]` for settign which providers of Konvoy/Kommander the plugin can work on, basic example is for AWS EFS driver so its is only alowed to deploy on an AWS hosted cluster. FOr example Prometheus would be viable for all.

Current options are aws, azure, gcp, non, vshpere, docker.

`spec.chartReference.version` will be the default version of the chart deployed  

`spec.chartReference.version` the version of the chart in the repo to use. Specified version will be default

`spec.chartReference.repo` helm repo to refence for this addon, must be a properly configured helm repo. See [Helm Repo](https://helm.sh/docs/topics/chart_repository/)

`spec.chartReference.values` teh default values to pass to the helm chart in hte repo. this is teh equivalent of a local values.yaml file or command override of values on `helm install` command.

## Konvoy

TBD

## Kommander

Adding a Repo to Kommander Project.

```yaml
apiVersion: kubeaddons.mesosphere.io/v1beta1
kind: AddonRepository
metadata:
  name: Name-of-Repo-Object
  namespace: {Project-Namespace}    # Namespace of the Project Created
spec:
  priority: "20.5"                 #priority of repo, incase two items exist between two repos, lower number has priority 
  url: "Location-of-addons-ref"    # here it would be "https://github.com/pwlmesos/plo-addons"
  ref: "branch-to-pull-from"       # here it would be "main"
```

Add this configuration to teh kommander cluster in the correct namespace.
