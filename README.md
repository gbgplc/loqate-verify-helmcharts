# Loqate Verify

[Jump to quickstart Installation](#quick-start)

## The Next Generation of Address Verification & Cleansing

Combining the trusted power of Loqate’s leading Global Address Knowledge Base and simple to use APIs with modern web-scale architectural approaches; Next Gen Verify is the enterprise ready solution for global address parsing, standardising, cleansing, and enhancing.

_[Sign up](https://account.loqate.com/register/) for a free trial account!_

## A Quick Overview of Verify’s Capabilities

The Loqate solution will parse, standardise, verify, cleanse and format address data via a single, easy to integrate API for over 245 countries and territories. Our solution has a proven track record in providing customers with global data coupled powered by our superior parsing and matching engine.

- The Global Knowledge Repository (GKR) is Loqate’s proprietary main database which combines our Knowledge Base data and parsing rules with our location reference datasets for over 245 countries and territories.

- Loqate’s Global Parsing Engine consists of proprietary lexicon and context parsing rules created specifically for each country and territory around the world. The parsing engine standardises and cleanses addresses from unstructured and semi-structured data, automatically placing elements into the correct fields and eliminating erroneous non-address data.

- Our address cleansing engine is capable of processing millions of records per hour and beyond. You’ll see an indication of the validity of each address value included in the input address, reducing costly errors. This solution can validate both partial and full address inputs.

- Loqate’s solution transliterates words or letters from different global character sets into either native or Roman characters across 8 scripts including: Cyrillic, Hellenic, Hebrew, Kanji, Simplified Chinese, Arabic, Thai, Hangul.

_See [loqate.com](https://www.loqate.com/resources/support/cleanse-api/international-batch-cleanse/) for detailed API documentation._

## What Next Generation Verify offers you

We recognise that our partners need solutions which enable them to manage their cloud estate seamlessly, using modern container orchestration to keep cost under control whilst scaling to meet demand.

With Next Generation Verify (NGV) we have decided to provide you with the same implementation we run for our own implementation. Providing out of the box helm charts which allow you to deploy and run in your own cloud infrastructure with ease.

Our implementation of Open telemetry enables you to gain insights into the NGV cluster. Whilst we provide you with sample dashboards using Prometheus and Grafana these tools are loosely coupled to our cluster, providing you the opportunity to customise to your needs, and your IT tools.

## Prerequisites

- Kubernetes 1.19+
- Helm v3.7.0

### Data Storage

The data is accessed using a Persistent Volume (PV).  It is downloaded using the `installmanager` chart and accessed by the `spatial-api` chart.  The provided yaml files mount a local volume and will need updating/replacing with the details of your PV.  The `values.yaml` files for both charts have `storage` properties for configuring the PV.

Currently, to store all the data you will need at least 250Gi of storage and downloading it will take 20-30 minutes.

These charts will work with any RWX (ReadWriteMany) Persistent Volume, but faster storage will produce better response times.

_[Read more](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes) about k8s Persistent Volumes and their Access Modes_

### Routing

Is handled by either Istio or a Kubernetes Ingress.  The default is Ingress.

If you want to use Ingress, you will need an Ingress controller.  The ingress resource only configures the controller, it does not install it.

_[Read more](https://kubernetes.io/docs/concepts/services-networking/ingress/#prerequisites) about the pre-requisites for getting Ingress to work._

### Scaling

You can use either KEDA or HPA not both.  The default is HPA.

_[Read more](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) about HPA and [Learn more](https://keda.sh/resources/) about KEDA_

### Recommended

In addition to minimum:

- Istio [v1.11.4]
- Keda [v2.4]

_Our product is supported with the above versions of pre-requisites, any other versions of these pre-requisites are considered out of scope._

### InstallManager

Downloads and installs the data on a Persistent Volume (PV).

### Memberlist

Used to co-ordinate communication between components

### Spatial-API

Searches and caches data for one country or all countries

### QueryCoordinator

Forwards requests to the appropriate Spatial-API

## How it Works

By having a Spatial-API deployment for the most searched countries, plus a catch-all deployment for the rest of the world (ROW), you can provision and scale each deployment according to expected traffic.

See the `values.yaml` files for resourcing spatialapi and querycoordinator.  The commented out values in these files are able to serve requests for the whole world but are likely to change.

## Get Repo Info

``` bash
helm repo add loqate https://charts.loqate.com
```

_See [helm repo](https://helm.sh/docs/helm/helm_repo/) for command documentation._

## Components

[Getting started with helmfile](#helmfile)

# Individual Component Installation

## Install Data

``` bash
helm install --create-namespace -n verify im loqate/installmanager --set app.licenseKey=<LICENSE KEY> --set app.products=<PRODUCTS TO INSTALL>
```

Ensure the download is fully completed before continuing.

## Install Charts

``` bash
helm install -n verify ml loqate/memberlist
helm install -n verify sa loqate/spatial-api --set app.memberlistService=ml-memberlist.verify.svc
helm install -n verify qc loqate/querycoordinator --set app.memberlistService=ml-memberlist.verify.svc
```

The _memberlistService_ name is composed of `<MEMBERLIST.RELEASE_NAME>-<MEMBERLIST.CHART_NAME>.<NAMESPACE>.svc`, changing any of these will require changing the set arguments to _spatial-api_ and _querycoordinator_.

_See [helm install](https://helm.sh/docs/helm/helm_install/) for command documentation._

## Uninstall Chart

``` bash
helm uninstall -n verify <RELEASE_NAME>
```

This removes all the Kubernetes components associated with the chart and deletes the release.

``` bash
kubectl delete namespace verify
```

This deletes the kubernetes namespace that was created for the Helm release.

_See [helm uninstall](https://helm.sh/docs/helm/helm_uninstall/) for command documentation._

## Upgrading Chart

```bash
helm upgrade verify <CHART> --install
```

_See [helm upgrade](https://helm.sh/docs/helm/helm_upgrade/) for command documentation._

## Adding the Loqate chart repo
```bash
helm repo add loqate https://charts.loqate.com
```

# Helmfile

Helmfile can be used to easily install all components in a simple K8s environment. It will automatically pull from the Loqate charts repository.

helm diff is a plugin used by helmfile and will need to be installed to helm.
```helm plugin install https://github.com/databus23/helm-diff```

More information on helmfile as well as how to use it can be found here: https://github.com/helmfile/helmfile

An example helmfile can be downloaded from https://charts.loqate.com/helmfile.yaml

### Install
```helmfile apply```

### Uninstall
```helmfile delete```

## Config Values
Location of locally available GKR data to mount.
```yml
storage:
  path: /run/desktop/mnt/host/c/loqate/data
```

Setting license key & products for installmanager
```yml
- app:
  licenseKey: licensekey
  products: "KBCOMMON,DSVGBR"
```

Setting dataset for spatial-api
```yml
verify:
  dataset: row
```

# Quick Start
```bash
mkdir lqtcharts && cd lqtcharts/

wget https://charts.loqate.com/helmfile.yaml -O helmfile.yaml

export LICENSE_KEY="<API_KEY>"

helmfile apply
```

# Application Version Information 

SpatialAPIAppVersion: 0.1.10419 

MemberlistAppVersion: 0.1.0 

QueryCoordinatorAppVersion: 0.1.11845 

InstallManagerAppVersion: 0.1.11805 

