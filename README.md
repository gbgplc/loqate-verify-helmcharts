# Verify: The Next Generation of Address Verification and Cleansing

Businesses of every size are increasingly managing the growth of, and demand for, cloud and SaaS technologies. Meanwhile customers expect their trusted partners to provide services that are hosted on the most capable, secure, scalable, and reliable platforms.

Verify uses cutting-edge products and services, and delivers them on a platform that offers the highest levels of security, scalability, and reliability. Not only that, but Verify also drives product innovation through the use of best-of-breed componentisation and modularisation, allowing for hands-free updates, simplified roll-out of feature enhancements, and on-demand scaling.

## Verify’s Capabilities

Verify has a proven track record in providing customers with global data coupled with our superior parsing and matching engine.

Here are a few key points regarding the services and functionality you have access to when using Verify:

- **The Global Knowledge Repository (GKR):** our proprietary main address database, which combines our Knowledge Base data and parsing rules with our location reference datasets for over 245 countries and territories
- **Global parsing engine:** our proprietary lexicon and context parsing rules created specifically for each country and territory around the world. The parsing engine standardises and cleanses addresses from unstructured and semi-structured data, automatically placing elements into the correct fields and eliminating erroneous non-address data
- **Address cleansing engine:** Verify is capable of processing millions of records per hour and beyond, and can validate both partial and full address inputs. It can provide an indication of the validity of each address value included with the output address, reducing costly errors
- **Transliteration:** Verify can transliterate words or letters from different global character sets into either native or Roman characters across 8 scripts: Cyrillic, Hellenic, Hebrew, Kanji, Simplified Chinese, Arabic, Thai, Hangul

_See [loqate.com](https://www.loqate.com/resources/support/cleanse-api/international-batch-cleanse/) for field descriptions and server options._

## What Verify Offers You

We recognise that our partners need solutions which enable you to manage your cloud estate seamlessly, using modern container orchestration to keep cost under control whilst scaling to meet demand.

With Verify we provide you with the same implementation we run, including out of the box Helm charts which allow you to deploy and run in your own cloud infrastructure with ease.

Observability is provided using Open Telemetry traces, stdout logging, and prometheus metrics, enabling you to gain insights into the Verify cluster.

### Security

For vulnerability disclosure or security findings, please contact support@loqate.com.

## Prerequisites

Please see below for details of the tools and requirements we recommend for any Verify installation.

### Docker Hub

A Docker Hub ID and password will be required for installation. You can create a free account at [https://hub.docker.com/]. Once created, please share your Docker Hub ID with your Loqate representative so you are given the ability to download the set of Verify containers.

### Tools

- Mandatory
  - Kubernetes [v1.23+]
  - Helm [v3.7.0]
- Recommended
  - Helmfile [v0.144.0]
  - Istio [v1.11.4]
  - Keda [v2.4]
  - Prometheus [v2.34.0]

_Verify has been tested using the version numbers stated above. Please avoid using earlier application versions than those stated._

### Reference Data Storage

The reference data is accessed using a Persistent Volume (PV); it is downloaded using the `installmanager` chart and accessed by the `spatial-api` chart. The provided yaml files mount a local volume and will need updating/replacing with the details of your PV. The `values.yaml` files for both charts have `storage` properties for configuring the PV.

You will need to create a data storage folder for `install manager`.

Currently, to store all the data you will need at least 250Gi of storage and downloading it will take several hours. If you're not using all datasets however, you will need less storage and the download will be quicker.

These charts will work with any RWX (ReadWriteMany) Persistent Volume, but faster storage will produce better response times.

_[Read more](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes) about k8s Persistent Volumes and their Access Modes._

### Routing

Routing is handled by either Istio or a Kubernetes Ingress - the default is Ingress.

If you want to use Ingress, you will need an Ingress controller. Note that the Ingress resource only _configures_ the controller, it does not install it.

_[Read more](https://kubernetes.io/docs/concepts/services-networking/ingress/#prerequisites) about the pre-requisites for getting Ingress to work._

As Verify takes advantage of new and developing technologies, best practices for routing are always evolving. For a detailed discussion on routing best practice, please get in touch with support@loqate.com to arrange a technical conversation.

### Scaling

Each of the `querycoordinator` and `spatial-api` components can be scaled with either HPA or KEDA. By default they both use HPA, but they can be set independently.

_[Read more](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) about HPA and [learn more](https://keda.sh/resources/) about KEDA._

As Verify takes advantage of new and developing technologies, best practices for managing the environment at scale are always evolving. For a detailed discussion on scaling best practice, please get in touch with support@loqate.com to arrange a technical conversation.

## How to Use This Guide

This guide is split up into the following sections:

- [**How a Verify Installation Works:**](#how-a-verify-installation-works) an overview of the components that make up a Verify installation
- [**Initial Setup:**](#initial-setup) some important environment variables to consider before setting anything up
- [**Quick Start Installation:**](#quick-start-installation) step by step instructions for getting up and running quickly with Verify
- [**Full Installation:**](#full-installation) further instructions for tailoring your installation
- [**Usage:**](#usage) details of how to test and use your Verify installation
- [**Example Requests:**](#request-and-response-format) a range of example requests and responses
- [**Troubleshooting:**](#troubleshooting) some suggestions for how to troubleshoot your installation

We recommend you complete the Quick Start Installation section before moving on to the Full Installation section.

## How a Verify Installation Works

By having a Spatial-API deployment for the most searched countries, plus a catch-all deployment for the rest of the world (ROW), you can provision and scale each deployment according to expected traffic.

See the `values.yaml` files for resourcing `spatialapi` and `querycoordinator`.  The commented out values in these files are able to serve requests for the whole world but are likely to change.

### Components

- **InstallManager:** downloads and installs the data on a Persistent Volume (PV)
- **Memberlist:** used to co-ordinate communication between components
- **Spatial-API:** searches and caches data for one country or all countries
- **QueryCoordinator:** forwards requests to the appropriate Spatial-API

## Initial Setup

Before you do anything, it's worth noting that you may need to change the default directory for the **data installation**. We'll explain how to change this path later in this section, but first we will look at the default value.

This path is used to configure the PV for the host filesystem, assuming Windows Docker Desktop:

- **LOQATE_NFS_SHARE**  is set to "/run/desktop/mnt/host/c/loqate/data"

**Claim Override:** the default creates a Persistent Volume (PV) and a Persistent Volume Claim (PVC) to use the host filesystem. If you already have a PV and PVC setup to use network storage you will use an override to connect to this pre-existing PVC. The environment variable name for this is CLAIM_OVERRIDE. You will see how it is set and used for different cases through this document.

Next we'll take you through how to make changes to those directories.

### Unix

- Create and go to the required directory. The lqtcharts directory can be wherever you want (you do not need the /opt/loqate folder on your local PC if you are using a claim override as the data will get downloaded to the remote PV):

```bash
mkdir -p /opt/loqate/lqtcharts
cd /opt/loqate/lqtcharts
```

**Unix settings**

Unix will almost certainly require the default path for data installation to be changed. To allow the helmfile to pick up your new path you need to change the environment variable for this path as follows:

```bash
export LOQATE_NFS_SHARE=<DATA FOLDER>
```

Example:

```bash
export LOQATE_NFS_SHARE=/opt/loqate/data
```

### Windows

- Create and go to the required directory:

```powershell
New-Item -Path "\loqate\lqtcharts" -ItemType "Directory"
Set-Location "\loqate\lqtcharts"
```

Depending on your setup you may need to change the default path for data download and installation. To allow the helmfile to pick up your new path you need to change the environment variable for this path as follows. (“Please note the $ sign below is part of the command for setting environment variable on powershell.”):

```powershell
$env:LOQATE_NFS_SHARE=<DATA FOLDER>
```

Example:

```powershell
$env:LOQATE_NFS_SHARE="/run/desktop/mnt/host/c/loqate/data"
```

## Quick Start Installation

To get you up and running with Verify as quickly as possible, we've provided this quick start method which uses [Helmfile](#helmfile) and, on Unix, the Helm plugin **helm-diff**. This will allow you to quickly get to a point where you can test your basic installation, before moving on to a more detailed setup afterwards.

- When you're ready to customise your installation of Verify in more detail, please see the [Helmfile](#helmfile) section below
- If you do not or cannot use Helmfile, see the section for [Helm](#helm) below

### Installing Helmfile

To begin with, you'll need to install Verify using Helmfile - we've provided the instructions for how to do this in both Unix and Windows.

Whether using Unix or Windows, first make sure you're in the directory that you created earlier (in the Initial Setup section)

**Unix:**

- Download the example helmfiles:

```bash
wget https://charts.loqate.com/helmfile-install.yaml -O helmfile-install.yaml
wget https://charts.loqate.com/helmfile.yaml -O helmfile.yaml
```

- Enter your license key and Docker Hub account information:

```bash
export LICENSE_KEY="<API KEY>"
export DOCKER_USERNAME="<DOCKER USERNAME>"
export DOCKER_PASSWORD="<DOCKER PASSWORD>"
```

- If you are using your own custom Persistent Volume Claim (PVC) you first need to set the following environment variable (if not, you can skip straight to the next step):

```bash
export CLAIM_OVERRIDE="<CLAIM NAME>"
```

- Ensure the data installer can write to the volume

Following good security practices, the installer pod does not run as the root user. This means the permissions of the volume and installer pod must align to ensure the pod can write to the mounted volume.

For production use, set the user and group of the installer to match the volume's ownership:

```bash
export POD_USER="<VOLUME USER ID>"
export POD_GROUP="<VOLUME GROUP ID>"
```

For local use, create the folder and make the volume writable:

```bash
mkdir /opt/loqate/data
chmod -R 777 /opt/loqate/data
```

- Install the data:

```bash
helmfile apply -f helmfile-install.yaml
```

- [Check the Progress of the Data Installation](#checking-the-progress-of-the-data-installation)

- Finally apply helmfile if it was successful:

```bash
helmfile apply
```

**Windows:**

- Download the example helmfiles:

```powershell
Invoke-WebRequest https://charts.loqate.com/helmfile-install.yaml -OutFile helmfile-install.yaml
Invoke-WebRequest https://charts.loqate.com/helmfile.yaml -OutFile helmfile.yaml
```

- Enter your license key and Docker Hub account information (“Please note the $ sign below is part of the command for setting environment variable on power shell.”):

```powershell
$env:LICENSE_KEY="<API KEY>"
$env:DOCKER_USERNAME="<DOCKER USERNAME>"
$env:DOCKER_PASSWORD="<DOCKER PASSWORD>"
```

- If you are using your own custom Persistent Volume Claim (PVC) you first need to set the following environment variable (if not, you can skip straight to the next step):

```powershell
$env:CLAIM_OVERRIDE="<CLAIM NAME>"
```

- Ensure the data installer can write to the volume

Following good security practices, the installer pod does not run as the root user. This means the permissions of the volume and installer pod must align to ensure the pod can write to the mounted volume.

For production use, set the user and group of the installer to match the volume's ownership:

```powershell
$env:POD_USER="<VOLUME USER ID>"
$env:POD_GROUP="<VOLUME GROUP ID>"
```

For local use, make the destination folder:

```powershell
New-Item -Path "c:/loqate/data/standard" -ItemType "Directory"
```

- Install the data:

```powershell
helmfile sync -f helmfile-install.yaml
```

- [Check the Progress of the Data Installation](#checking-the-progress-of-the-data-installation)

If there was a problem writing to the destination, make the folder writable:

```powershell
docker run --rm -it -v /run/desktop/mnt/host/c/loqate/data/standard:/data busybox chmod -R 777 /data
```

Then apply the install helmfile again.

```powershell
helmfile sync
```

Note: If you want to run the quick start again without a data download, see [Re-run quick start without data download](#re-run-quick-start-without-data-download).

### Download Just a Subset of the Allowed Datasets

The install process, by default, will download all datasets listed on your key. If you only want to download a subset of the available data, you can choose which datasets to download by setting the environment variable `LOQATE_PRODUCTS` to the required value, for example `"KBCOMMON,DSVGBR"`.

The datasets/products available to you are listed in your license email.  You should always include `KBCOMMON`.

**Unix:**

```bash
export LOQATE_PRODUCTS="KBCOMMON,DSVGBR"
```

**Windows:**

```powershell
$env:LOQATE_PRODUCTS="KBCOMMON,DSVGBR"
```

### Checking the Progress of the Data Installation

The next step is to check the progress of the data installation - to do this, first get the name of the installmanager pod:

``` bash
kubectl get pods -n loqate
```

Here's an example output:

``` bash
NAME                                READY   STATUS             RESTARTS      AGE
installmanager-787zf                1/1     Running            0             15s
querycoordinator-59d4dbfb8b-rt9cr   0/1     Running            0             14s
spatial-api-58fbdb8486-d52rc        0/1     Running            0             13s
```

Then get the logs of the pod:

``` bash
kubectl logs installmanager-787zf -n loqate --follow
```

Example output:

``` text
Native library STLPort failed to load, ignore this if not using solaris OS.
java.lang.UnsatisfiedLinkError: no stlport in java.library.path
Welcome to the Installation Manager - version 15.0.0
Running using local API version 2.42.1.16022-8876f6e

Reading /src/im/silent.txt for silent installation...

Contacting license server to validate the license key.
+++
Done validating license key.

Fetching information from server about the available data packs.

Contacting license server for information on available updates.
+++
Done fetching information from server regarding available data packs.
----------------------------------------
Space available : 118.0 GB
Space required for download : XXX.X GB
----------------------------------------
Downloading data packs.

...

Completed downloading data packs.
----------------------------------------
Space available : 62.8 GB
Space required for install : XXX.X GB
----------------------------------------
Beginning installing of data packs.

...

Completed installing the data packs.
Datapack installation was successfull.
XXX of XXX datapack(s) installed successfully.
Installation complete. Editing loqate.ini file now...
ReferenceDataCacheSize=7
Updating loqate.ini
Edit complete
```

The data installation has finished when you see the `Edit complete` line.

### Testing the Installation

You can test the Verify installation by sending requests through the system. We recommend you test both a GET request and a POST request, so we've included examples of both.

First open a port to receive requests (you may want to do this in a new shell because the command does not terminate):

``` bash
kubectl port-forward -n loqate svc/querycoordinator 8900:8900
```

Then send a version request (GET) to determine if the software and data was successfully installed.

**Unix:**

``` bash
curl -X GET http://localhost:8900/api/version
```

Successful response:

``` json
{"version":"2.44.0.16383-1f51bc7"}
```

**Windows:**

``` powershell
Invoke-WebRequest -Method GET http://localhost:8900/api/version
```

Successful response:

``` powershell
StatusCode        : 200
StatusDescription : OK
Content           : {"version":"2.44.0.16383-1f51bc7"}
RawContent        : HTTP/1.1 200 OK
                    Content-Length: 34
                    Content-Type: application/json; charset=utf-8
                    Date: Mon, 22 Aug 2022 11:10:08 GMT

                    {"version":"2.44.0.16383-1f51bc7"}
Forms             : {}
Headers           : {[Content-Length, 34], [Content-Type, application/json; charset=utf-8], [Date, Mon, 22 Aug 2022 11:10:08 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 34
```

Now send a Verify request (POST).

**Unix:**

``` bash
curl -X POST http://localhost:8900/verify -d '{"input":[{"Address1":"TheFoundation","Locality":"Chester","Country":"GB"}]}'
```

Successful output:

``` json
{"output":[{"AQI":"A","AVC":"P44-I44-P0-100","Address":"The Foundation\u003cBR\u003eHerons Way\u003cBR\u003eChester Business Park\u003cBR\u003eChester","Address1":"The Foundation","Address2":"Herons Way","Address3":"Chester Business Park","Address4":"Chester","AdministrativeArea":"Cheshire","Building":"The Foundation","Country":"GB","CountryName":"United Kingdom","DeliveryAddress":"The Foundation\u003cBR\u003eHerons Way\u003cBR\u003eChester Business Park","DeliveryAddress1":"The Foundation","DeliveryAddress2":"Herons Way","DeliveryAddress3":"Chester Business Park","DependentLocality":"Chester Business Park","HyphenClass":"B","ISO3166-2":"GB","ISO3166-3":"GBR","ISO3166-N":"826","Locality":"Chester","MatchRuleLabel":"1","Sequence":"1","Thoroughfare":"Herons Way"}]}
```

**Windows:**

``` powershell
Invoke-WebRequest http://localhost:8900/verify -Method POST -Body "{`"input`":[{`"Address1`":`"TheFoundation`",`"Locality`":`"Chester`",`"Country`":`"GB`"}]}"
```

Successful output:

``` powershell
StatusCode        : 200
StatusDescription : OK
Content           : {"output":[{"AQI":"A","AVC":"P44-I44-P0-100","Address":"The Foundation\u003cBR\u003eHerons Way\u003cBR\u003eChester Business Park\u003cBR\u003eChester","Address1":"The
                    Foundation","Address2":"Herons W...
RawContent        : HTTP/1.1 200 OK
                    Content-Length: 773
                    Content-Type: application/json; charset=utf-8
                    Date: Mon, 22 Aug 2022 11:40:03 GMT

                    {"output":[{"AQI":"A","AVC":"P44-I44-P0-100","Address":"The Foundation\u003c...
Forms             : {}
Headers           : {[Content-Length, 773], [Content-Type, application/json; charset=utf-8], [Date, Mon, 22 Aug 2022 11:40:03 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 773
```

If one or both of the tests are unsuccessful, first check the Troubleshooting section below for information about the most likely error.

If you can't find the error, check the logs for each pod.

**How to Check the Logs**

First get the pods:

```bash
$ kubectl get pods
NAME                                   READY   STATUS             RESTARTS      AGE
qc-querycoordinator-76c78c997c-qx2mb   1/1     Running            2 (18h ago)   18h
sa-spatial-api-79c4f874c8-rrb45        0/1     ImagePullBackOff   0             18h
```

Then run the following on the pod you wish to check the logs for:

```bash
kubectl logs <NAME>
```

If you still can't find or resolve the error, please contact support@loqate.com to arrange a further discussion.

Once you've successfully tested your quick start install, you can move onto the next section to further tailor your installation.

### Re-run Quick Start Without Data Download

You may find that you want to re-run the Quick Start process, but don't need to re-download all of the data.  To stop the data getting downloaded again, delete your current installation and remove the **installmanager** section of the helmfile.yaml.

- Remove the previous Helmfile install

```bash
helmfile purge
```

In helmfile.yaml:

- Delete the **installmanager** section
- In the **spatial-api** section comment out or remove the "- installmanager" line in the **needs** section as shown below:

``` yml
needs:
    #- installmanager
```

- Re-sync the Helmfile installation

Unix:

``` bash
helmfile apply
```

Windows:

``` powershell
helmfile sync
```

## Full Installation

With a Quick Start install completed, you can build on this by adding country-specific deployments and certified countries. We've included information on completing a full install using both Helmfile and Helm, allowing you to choose which option you would prefer.

- [Click here for the Helmfile installation instructions](#helmfile)
- [Click here for the Helm installation instructions](#helm)

Note: whether you are using Helm or Helmfile, please pay particular attention to the configuration settings below.

### Important Configuration Settings

#### Docker Images

The components `installmanager`, `spatial-api` and `querycoordinator` all have associated Docker images.  These Docker images are hosted in private repositories on Docker Hub, so you will need a Docker Hub account in order to be granted access.

If you want to pull specific images from docker hub for installmanager, spatial-api and querycoordinator use the following settings:

**Helmfile Unix**

For Unix helmfile set the following environment variables:

``` bash
export LOQATE_INSTALLMANAGER_VERSION="<IMAGE TAG>"
export LOQATE_QUERY_COORDINATOR_VERSION="<IMAGE TAG>"
export LOQATE_SPATIAL_API_VERSION="<IMAGE TAG>"
```

**Helmfile Windows**

For Windows helmfile set the following environment variables:

``` powershell
$env:LOQATE_INSTALLMANAGER_VERSION="<IMAGE TAG>"
$env:LOQATE_QUERY_COORDINATOR_VERSION="<IMAGE TAG>"
$env:LOQATE_SPATIAL_API_VERSION="<IMAGE TAG>"
```

**Helm**

For helm installations add one of the following to your command line:

``` bash
--set image.tag=<LOQATE INSTALLMANAGER IMAGE TAG>
--set image.tag=<LOQATE QUERY COORDINATOR IMAGE TAG>
--set image.tag=<LOQATE SPATIAL API IMAGE TAG>
```

#### Adding Country Specific Deployments

To get the best performance and flexible scaling, we recommend having spatial-api deployments dedicated to countries you anticipate will be serving large numbers of requests.

If you want to create a country specific deployment, you'll need to set the `verify.dataset` value to the ISO3166-2 code for that country. We've included an example below for a GB deployment, using the *default* data path. (This will mainly apply to Windows installations).

``` powershell
helm install -n loqate sa-gb loqate/spatial-api --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set app.memberlistService=memberlist.loqate.svc --set verify.dataset=gb
```

Here's an example using the changed path for data download and installation. Note the extra argument in the commands below set the path. As per [Initial Setup](#initial-setup):

``` bash
helm install -n loqate sa-gb loqate/spatial-api --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set storage.path=<DATA FOLDER> --set app.memberlistService=memberlist.loqate.svc --set verify.dataset=gb
```

#### Certified Datasets (CASS, SERP, AMAS)

To use any of the certified datasets, you will need to access extra libraries.  Given an appropriate license key, these will be downloaded and installed alongside the data, in sub-folder `lib64`.

> Please note that for legal reasons, if you are located outside of the USA you will not be able to download the certified US (i.e. CASS) data.

Here's an example of how (in Helm) to create an AU spatial-api deployment that can use the AMAS certified engine:

``` powershell
helm install -n loqate spatial-api-au loqate/spatial-api --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set app.memberlistService=memberlist.loqate.svc --set verify.dataset=au
```

For examples of how to change config values using Helmfile, see [this section below](#config-values).

#### Premium Datasets

There are currently three premium datasets: France, Ireland, and US.

You can have one deployment for all premium requests by setting `verify.dataset` value to `row` or have country specific deployments as described in [Adding Country Specific Deployments](#adding-country-specific-deployments)

The premium US dataset requires more resources than other datasets.  SpatialAPI will need at least these resources values:

```yaml
    resources:
      requests:
        cpu: 2000m
        memory: 5250Mi
      limits:
        cpu: 2200m
        memory: 5775Mi
```

See [helmfile-premium.yaml](https://charts.loqate.com/helmfile-premium.yaml) as an example.  It includes premium environment variables for the standard [config values](#config-values).

Unix:

``` bash
wget https://charts.loqate.com/helmfile-premium.yaml -O helmfile-premium.yaml
```

Windows:

``` powershell
Invoke-WebRequest https://charts.loqate.com/helmfile-premium.yaml -OutFile helmfile-premium.yaml
```

You'll need two Loqate license keys, one for the standard datasets and the other for the premium datasets.

Note: Certify datasets are considered `standard` therefore should be included in the standard license and installed in the `standard` folder.  Requests that include both the `certify` and `premium` options will have the `premium` option turned off internally.

Download each set of data using their own InstallManager job to different folders, `standard` and `premium`, by changing `storage.path`.  See [helmfile-install.yaml](https://charts.loqate.com/helmfile-install.yaml) for an example.

Create a Spatial API deployment for each dataset.  For those serving premium data, change the `verify.premium` value to `true` and set `storage.path` to the `premium` folder.  The standard deployments should keep `verify.premium` as `false` and set `storage.path` to the `standard` folder.

The new InstallManager job and Spatial API deployment will need new names, be sure to update the name of the Spatial API `needs` to match the new InstallManager name.  See [helmfile-premium.yaml](https://charts.loqate.com/helmfile-premium.yaml) as an example.

Requests for premium data sets need to contain the `premium` and `premiumCountries` options:

``` json
{
    "options": {
        "premium": "true",
        "premiumCountries": ["FR", "IE", "US"]
    },
    "input": [
        {
            "Address1": "59 Parklands Court",
            "Address2": "Dublin",
            "PostalCode": "D24 CA39",
            "Country": "IE"
        }
    ]
}
```

The `premiumCountries` option can be used to control access to the specific datasets in a re-seller situation.

### Helmfile

Helmfile can be used to easily install all components in a simple Kubernetes environment. It will automatically pull from the Loqate charts repository.

> Note: Helmfile uses the helm-diff plugin for the `helmfile apply` command, which you can install with the command below. This plugin does not work on Windows, so we use `helmfile sync` instead.

``` bash
helm plugin install https://github.com/databus23/helm-diff
```

To pull specific images from docker hub for installmanager, spatial-api and querycoordinator add to your command line as per [**Docker Images:**](#docker-images)

More information on Helmfile as well as how to use it can be found here: <https://github.com/helmfile/helmfile>

An example Helmfile can be downloaded from <https://charts.loqate.com/helmfile.yaml>

#### Install

The installation process for Helmfile is very simple, requiring the following commands.

Unix:

``` bash
helmfile apply
```

Windows:

``` powershell
helmfile sync
```

#### Uninstall

The command to uninstall Helmfile in either Unix or Windows is:

```helmfile purge```

#### Config Values

There are various values you can configure within the Helmfile - in this section we will provide some examples of what you can configure and how.

**Location of locally available GKR data to mount:**

This needs to be set the same for both installmanager and spatial-api (and also uses slightly different paths in Unix and Windows)

**Unix:**

```bash
export LOQATE_NFS_SHARE=<DATA FOLDER>
```

Example:

```bash
export LOQATE_NFS_SHARE=/opt/loqate/data
```

**Windows using Docker Desktop:**

```powershell
$env:LOQATE_NFS_SHARE=<DATA FOLDER>
```

Example:

```powershell
$env:LOQATE_NFS_SHARE="/run/desktop/mnt/host/c/loqate/data"
```

**Setting license key and products for installmanager:**

**Unix:**

```bash
export LICENSE_KEY=<API KEY>
```

**Windows:**

```powershell
$env:LICENSE_KEY=<API KEY>
```

To download a subset of the datasets on your license, use a comma separated list of the datasets as follows:

**Unix:**

```bash
export LOQATE_PRODUCTS=<PRODUCT LIST>
```

Example:

```bash
export LOQATE_PRODUCTS="KBCOMMON,DSVGBR"
```

**Windows:**

```powershell
$env:LOQATE_PRODUCTS=<PRODUCT LIST>
```

Example:

```powershell
$env:LOQATE_PRODUCTS="KBCOMMON,DSVGBR"
```

**Setting dataset for spatial-api:**

Here's how to set a specific dataset for spatial-api, using ROW (i.e. 'Rest of the World') as an example:

``` yml
  verify:
    dataset: "row"
```

To set a spatial-api for a specific _certified_ dataset (for example Australia - 'au'), you'll need to create a new spatial-api section in the yaml file.

Copy the current spatial-api and give it a unique name, then set the dataset to _au_ as shown below:

``` yml
  verify:
    dataset: "au"
```

For more information about certified data sets see the earlier [Certified Datasets (CASS, SERP, AMAS)](#certified-datasets-cass-serp-amas) section.

### Helm

Follow the instructions below to install Verify using Helm (you can use all of the instructions below in both Unix and Windows).

To pull specific images from docker hub for installmanager, spatial-api and querycoordinator add to your command line as per [**Docker Images:**](#docker-images)

Note: if you are using your own custom Persistent Volume Claim (PVC) you need to set the claim override for both the installmanager and spatial-api installs:

```bash
--set storage.claimOverride=<PVC NAME>
```

#### Add Repo

``` bash
helm repo add loqate https://charts.loqate.com
```

_See [Helm repo](https://helm.sh/docs/helm/helm_repo/) for command documentation._

#### Create Namespace

``` bash
kubectl create namespace loqate
```

#### Install Data

If you changed the default directories for the data download and installation, you will need to add the appropriate paths to some Helm commands. The lines to add are as follows:

``` bash
--set storage.path=<DATA FOLDER>
```

**Install All Data**

The command below uses the default data download and installation paths (this will mainly apply to Windows installations):

``` powershell
helm install -n loqate installmanager loqate/installmanager --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set app.licenseKey=<API KEY>
```

The following command uses the changed paths for data download and installation (note the extra arguments in the commands below set multiple paths, but you only need to add the ones you wish to overwrite. As per [Initial Setup](#initial-setup)):

``` bash
helm install -n loqate installmanager loqate/installmanager --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set app.licenseKey=<API KEY> --set storage.path=<DATA FOLDER>
```

Or, if you are using a PVC, you need to specify a claimOverride:

``` bash
helm install -n loqate installmanager loqate/installmanager --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set app.licenseKey=<API KEY> --set storage.claimOverride=<PVC NAME>
```

**Install Specific Datasets**

To install a subset of the datasets you have in the license key you can add --set app.products="KBCOMMON\\,DSVGBR" as below.

> Note the need to escape commas

The command below uses the default data path (this will mainly apply to Windows installations):

``` powershell
helm install -n loqate installmanager loqate/installmanager --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set app.licenseKey=<API KEY> --set app.products="KBCOMMON\,DSVGBR"
```

The following command uses the changed path for data installation. As per [Initial Setup](#initial-setup)):

``` bash
helm install -n loqate installmanager loqate/installmanager --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set app.licenseKey=<API KEY>--set storage.path=<DATA FOLDER> --set app.products="KBCOMMON\,DSVGBR"
```

It's important to make sure the download is fully completed before continuing. See the section on [Checking the Progress of the Data Installation](#checking-the-progress-of-the-data-installation) earlier for details of how to do this.

#### Install Charts

Install memberlist:

``` bash
helm install -n loqate memberlist loqate/memberlist
```

Install spatial-api and querycoordinator:

The command below uses the default data installation path (this will mainly apply to Windows installations):

``` powershell
helm install -n loqate spatial-api loqate/spatial-api --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set app.memberlistService=memberlist.loqate.svc
helm install -n loqate querycoordinator loqate/querycoordinator --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set app.memberlistService=memberlist.loqate.svc
```

The following command uses a changed path for data installation. As per [Initial Setup](#initial-setup)):

``` bash
helm install -n loqate spatial-api loqate/spatial-api --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set storage.path=<DATA FOLDER> --set app.memberlistService=memberlist.loqate.svc
helm install -n loqate querycoordinator loqate/querycoordinator --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set app.memberlistService=memberlist.loqate.svc
```

Alternatively, if you are using a PVC you need to specify the claimOverride for spatial-api:

``` bash
helm install -n loqate spatial-api loqate/spatial-api --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set storage.claimOverride=<PVC NAME> --set app.memberlistService=memberlist.loqate.svc
helm install -n loqate querycoordinator loqate/querycoordinator --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set app.memberlistService=memberlist.loqate.svc
```

Check the spatial-api and querycoordinator have started:

```bash
kubectl get pods -n loqate
```

Example output:

```bash
NAME                                  READY   STATUS    RESTARTS      AGE
querycoordinator-ffdc5cfbc-2hj7w   1/1     Running   0             14m
spatial-api-6dbfbb7f88-pqqgj       1/1     Running   1 (14m ago)   15m
```

Wait until there is 1/1 in the READY column for spatial-api and querycoordinator. After this wait an extra 1 minute.

The _memberlistService_ name is composed of `<MEMBERLIST.RELEASE NAME>-<MEMBERLIST.CHART NAME>.<NAMESPACE>.svc`. Changing any of these will require changing the set arguments to _spatial-api_ and _querycoordinator_.

_See [Helm Install](https://helm.sh/docs/helm/helm_install/) for command documentation._

#### Upgrade Chart

To pull specific images from docker hub for installmanager, spatial-api and querycoordinator add to your command line as per [**Docker Images:**](#docker-images)

``` bash
helm upgrade <RELEASE NAME> <CHART> --install
```

The example below is an upgrade when you get a new license key with a new data set added:

```bash
helm upgrade -n loqate installmanager loqate/installmanager --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set app.licenseKey=<API KEY>
```

_See [Helm Upgrade](https://helm.sh/docs/helm/helm_upgrade/) for command documentation._

#### Uninstall Chart

``` bash
helm uninstall -n loqate <RELEASE NAME>
```

#### Delete namespace

This removes all the Kubernetes components associated with the chart and deletes the release:

``` bash
kubectl delete namespace loqate
```

This deletes the kubernetes namespace that was created for the Helm release.

_See [Helm uninstall](https://helm.sh/docs/helm/helm_uninstall/) for command documentation._

### Full System Clean Up

If you ever need to do a full system clean up, here are the steps to take.

#### Helmfile-only Clean Up Commands

Perform the following:

``` bash
helmfile purge
```

#### Helmfile and Helm Clean Up Commands

**Delete the namespace:**

``` bash
kubectl delete namespace loqate
```

**Delete the Persistent Volumes:**

Get the persistent volumes:

``` bash
kubectl get pv
```

If there are any persistent volumes for installmanager or spatial-api you can delete them with:

``` bash
kubectl delete pv <NAME>
```

#### Check That the System Has Cleaned Up

``` bash
$ kubectl -n loqate get pods
No resources found in loqate namespace.

$ kubectl -n loqate get services
No resources found in loqate namespace.

$ kubectl -n loqate get deployments
No resources found in loqate namespace.

$ kubectl get pv
No resources found

$ kubectl -n loqate get pvc
No resources found in loqate namespace.
```

## Usage

### Functions

The `querycoordinator` chart contains templates for a Kubernetes ingress and an Istio gateway, but both are disabled by default. To test the installation, forward port 8900 of the `querycoordinator` service and use the following URLs.

| Function | Method | URL |
| -------- | ------ | --- |
| Verify   | POST   | <http://localhost:8900/verify> |
| Version  | GET    | <http://localhost:8900/api/version> |
| GKR info | GET    | <http://localhost:8900/api/gkrinfo> |
| GKR info (Premium data) | GET    | <http://localhost:8900/api/gkrinfo?premium=true> |

### Verify Request

#### Request Parameters

| Name | Description |
| ---- | --- |
| input | An array of addresses that you want to verify. For optimal processing, the country field should contain a valid ISO2 or ISO3 country code.  Without a country code, requests will be routed to the `ROW` deployment. Please see [How a Verify Installation Works](#how-a-verify-installation-works) and [Important Configuration Settings](#important-configuration-settings). |
| options | Used to specify various options and override processes. |

**Options** comprised of the following:

- **Geocode (boolean as string):** if you want geo-coordinates to be appended to your results (if available)
- **Processes (array of string):** Loqate Process to run. Valid Values: Verify, Search, Query*, ReverseGeocode.  Defaults to Verify when not supplied
- **Certify (boolean as string):** use certified dataset. AMAS (AU), CASS (US) or SERP (CA)
- **Enhance (boolean as string):** use enhanced datasets. Returns enhance fields if applicable
- **ServerOptions (object):** properties are OptionName and value is OptionValue

*Query is a debug tool to be used to support submission of support cases, and please note this tool is in maintenance mode with no planned future developments or bug fixes.

More information about [server options](https://support.loqate.com/server-options/).

#### Response Fields

| Name | Description |
| --- | --- |
| output | An array of matched records |

**Output** comprised of the following:

- **AVC:** Address verification code
- **AQI:** Address Quality Index
- **Address Elements:** the cleansed address elements
- **Latitude/Longitude:** the latitude and longitude of the address (if Geocode is used)

See this complete list of [field descriptions](https://support.loqate.com/documentation/fielddescrip/).

### Request and Response Format

We've provided a selection of the most useful examples below for both sample requests and returns. Click on the expanding sections to see the details of each.

<details>
	<summary>Request - Geocode as True (click to expand)</summary>

<pre>
{
    "Options": {
        "Geocode": "true"
    },
    "input": [
        {
            "Address1": "GB Group",
            "Address2": "The Foundation",
            "PostalCode": "CH4 9GB",
            "Country": "GB"
        }
    ]
}
</pre>

</details>

<details>
	<summary>Response - Geocode as True</summary>

<pre>
{
    "output": [
        {
            "AQI": "A",
            "AVC": "V44-I55-P6-100",
            "Address": "Gb Group&lt;BR&gt;The Foundation&lt;BR&gt;Herons Way&lt;BR&gt;Chester Business Park&lt;BR&gt;Chester&lt;BR&gt;CH4 9GB",
            "Address1": "Gb Group",
            "Address2": "The Foundation",
            "Address3": "Herons Way",
            "Address4": "Chester Business Park",
            "Address5": "Chester",
            "Address6": "CH4 9GB",
            "AdministrativeArea": "Cheshire",
            "Building": "The Foundation",
            "BuildingName": "The",
            "BuildingTrailingType": "Foundation",
            "BuildingType": "Foundation",
            "Country": "GB",
            "CountryName": "United Kingdom",
            "DeliveryAddress": "The Foundation&lt;BR&gt;Herons Way&lt;BR&gt;Chester Business Park",
            "DeliveryAddress1": "The Foundation",
            "DeliveryAddress2": "Herons Way",
            "DeliveryAddress3": "Chester Business Park",
            "DependentLocality": "Chester Business Park",
            "GeoAccuracy": "P4",
            "GeoDistance": "0.0",
            "HyphenClass": "B",
            "ISO3166-2": "GB",
            "ISO3166-3": "GBR",
            "ISO3166-N": "826",
            "Latitude": "53.162461",
            "Locality": "Chester",
            "Longitude": "-2.898728",
            "MatchRuleLabel": "C1ap",
            "Organization": "Gb Group",
            "OrganizationName": "Gb",
            "OrganizationTrailingType": "Group",
            "OrganizationType": "Group",
            "PostalCode": "CH4 9GB",
            "PostalCodePrimary": "CH4 9GB",
            "Sequence": "1",
            "Thoroughfare": "Herons Way"
        }
    ]
}
</pre>

</details>

<details>
	<summary>Request - Process Option as ‘Verify’</summary>

<pre>
{
    "options": {
        "Processes":["Verify"]
    },
    "input": [
        {
            "Address1": "7511 Oakvale Ct",
            "PostalCode": "95660",
            "Locality": "North Highlands",
            "AdministrativeArea": "CA",
            "Country": "US"
        }
    ]
}
</pre>

</details>

<details>
	<summary>Response - Process Option as ‘Verify’</summary>

<pre>
{
    "output": [
        {
            "AQI": "A",
            "AVC": "V44-I44-P7-100",
            "Address": "7511 Oakvale Ct&lt;BR&gt;North Highlands CA 95660-2733",
            "Address1": "7511 Oakvale Ct",
            "Address2": "North Highlands CA 95660-2733",
            "AdministrativeArea": "CA",
            "Country": "US",
            "CountryName": "United States",
            "DeliveryAddress": "7511 Oakvale Ct",
            "DeliveryAddress1": "7511 Oakvale Ct",
            "HyphenClass": "C",
            "ISO3166-2": "US",
            "ISO3166-3": "USA",
            "ISO3166-N": "840",
            "Locality": "North Highlands",
            "MatchRuleLabel": "Rlh",
            "PostalCode": "95660-2733",
            "PostalCodePrimary": "95660",
            "PostalCodeSecondary": "2733",
            "Premise": "7511",
            "PremiseNumber": "7511",
            "Sequence": "1",
            "SubAdministrativeArea": "Sacramento",
            "Thoroughfare": "Oakvale Ct"
        }
    ]
}
</pre>

</details>

<details>
	<summary>Request - Process Option as ‘Search’</summary>

<pre>
{
    "input": [
        {
            "Country": "JP",
            "PostalCode": "408-0307"
        }
    ],
    "options": {
        "Processes": ["Search"],
        "ServerOptions": {
            "MaxResults": 3
        }
    }
}
</pre>

</details>

<details>
	<summary>Response - Process Option as ‘Search’</summary>

<pre>
{
    "output": [
        [
            {
                "AQI": "A",
                "AVC": "A42-I44-P6-100",
                "Address": "1290 Mukawachoyanagisawa&lt;BR&gt;Hokuto-Shi Yamanashi 408-0307",
                "Address1": "1290 Mukawachoyanagisawa",
                "Address2": "Hokuto-Shi Yamanashi 408-0307",
                "AdministrativeArea": "Yamanashi",
                "Country": "JP",
                "CountryName": "Japan",
                "DeliveryAddress": "1290 Mukawachoyanagisawa",
                "DeliveryAddress1": "1290 Mukawachoyanagisawa",
                "ISO3166-2": "JP",
                "ISO3166-3": "JPN",
                "ISO3166-N": "392",
                "Locality": "Hokuto-Shi",
                "PostalCode": "408-0307",
                "PostalCodePrimary": "408-0307",
                "Premise": "1290",
                "PremiseNumber": "1290",
                "SearchLevel": "2",
                "SearchMethod": "Noparse",
                "SearchSimilarity": "100",
                "Sequence": "1",
                "Thoroughfare": "Mukawachoyanagisawa"
            },
            {
                "AQI": "A",
                "AVC": "A42-I44-P6-100",
                "Address": "1490 Mukawachoyanagisawa&lt;BR&gt;Hokuto-Shi Yamanashi 408-0307",
                "Address1": "1490 Mukawachoyanagisawa",
                "Address2": "Hokuto-Shi Yamanashi 408-0307",
                "AdministrativeArea": "Yamanashi",
                "Country": "JP",
                "CountryName": "Japan",
                "DeliveryAddress": "1490 Mukawachoyanagisawa",
                "DeliveryAddress1": "1490 Mukawachoyanagisawa",
                "ISO3166-2": "JP",
                "ISO3166-3": "JPN",
                "ISO3166-N": "392",
                "Locality": "Hokuto-Shi",
                "PostalCode": "408-0307",
                "PostalCodePrimary": "408-0307",
                "Premise": "1490",
                "PremiseNumber": "1490",
                "SearchLevel": "2",
                "SearchMethod": "Noparse",
                "SearchSimilarity": "100",
                "Sequence": "1",
                "Thoroughfare": "Mukawachoyanagisawa"
            },
            {
                "AQI": "A",
                "AVC": "A42-I44-P6-100",
                "Address": "1492 Mukawachoyanagisawa&lt;BR&gt;Hokuto-Shi Yamanashi 408-0307",
                "Address1": "1492 Mukawachoyanagisawa",
                "Address2": "Hokuto-Shi Yamanashi 408-0307",
                "AdministrativeArea": "Yamanashi",
                "Country": "JP",
                "CountryName": "Japan",
                "DeliveryAddress": "1492 Mukawachoyanagisawa",
                "DeliveryAddress1": "1492 Mukawachoyanagisawa",
                "ISO3166-2": "JP",
                "ISO3166-3": "JPN",
                "ISO3166-N": "392",
                "Locality": "Hokuto-Shi",
                "PostalCode": "408-0307",
                "PostalCodePrimary": "408-0307",
                "Premise": "1492",
                "PremiseNumber": "1492",
                "SearchLevel": "2",
                "SearchMethod": "Noparse",
                "SearchSimilarity": "100",
                "Sequence": "1",
                "Thoroughfare": "Mukawachoyanagisawa"
            }
        ]
    ]
}
</pre>

</details>

<details>
	<summary>Request - Process Option as ‘Query’</summary>

<pre>
{
    "Input": [
        {}
    ],
    "Options": {
        "Processes": ["Query"],
        "ServerOptions": {
            "OutputFields": "Locality",
            "QueryClause": "Distinct",
            "AliasPreference": "First",
            "RangefieldPreference": "Match"
        },
        "ProcessOptions": {
            "Table": "rd_AU_vfy",
            "QueryString": "(Locality LIKE \"Meadow Heights%*\") || (PostalCodePrimary LIKE \"3048%*\")",
            "OutputFields": "PremiseNumber, Thoroughfare,Locality, PostalCodePrimary",
            "QueryClause": "Distinct",
            "AliasPreference": "First",
            "RangefieldPreference": "Match"
        }
    }
}
</pre>

</details>

<details>
	<summary>Response - Process Option as ‘Query’</summary>

<pre>
{
    "output": [
        {
            "Locality": "Meadow Heights",
            "PostalCodePrimary": "3048",
            "Sequence": "1"
        },
        {
            "Locality": "Meadow Heights",
            "PostalCodePrimary": "3048",
            "PremiseNumber": "10",
            "Sequence": "1",
            "Thoroughfare": "Abelia Ct"
        },
        {
            "Locality": "Meadow Heights",
            "PostalCodePrimary": "3048",
            "PremiseNumber": "12",
            "Sequence": "1",
            "Thoroughfare": "Abelia Ct"
        },
        {
            "Locality": "Meadow Heights",
            "PostalCodePrimary": "3048",
            "PremiseNumber": "14",
            "Sequence": "1",
            "Thoroughfare": "Abelia Ct"
        },
        {
            "Locality": "Meadow Heights",
            "PostalCodePrimary": "3048",
            "PremiseNumber": "16",
            "Sequence": "1",
            "Thoroughfare": "Abelia Ct"
        },
        {
            "Locality": "Meadow Heights",
            "PostalCodePrimary": "3048",
            "PremiseNumber": "18",
            "Sequence": "1",
            "Thoroughfare": "Abelia Ct"
        },
        {
            "Locality": "Meadow Heights",
            "PostalCodePrimary": "3048",
            "PremiseNumber": "20",
            "Sequence": "1",
            "Thoroughfare": "Abelia Ct"
        },
        {
            "Locality": "Meadow Heights",
            "PostalCodePrimary": "3048",
            "PremiseNumber": "22",
            "Sequence": "1",
            "Thoroughfare": "Abelia Ct"
        },
        {
            "Locality": "Meadow Heights",
            "PostalCodePrimary": "3048",
            "PremiseNumber": "24",
            "Sequence": "1",
            "Thoroughfare": "Abelia Ct"
        },
        {
            "Locality": "Meadow Heights",
            "PostalCodePrimary": "3048",
            "PremiseNumber": "3",
            "Sequence": "1",
            "Thoroughfare": "Abelia Ct"
        }
    ]
}
</pre>

</details>

<details>
	<summary>Request - Process Option as ‘ReverseGeocode’</summary>

<pre>
{
    "options": {
        "Processes": ["ReverseGeocode"],
        "ServerOptions": {
            "MaxResults": "3"
        }
    },
    "input": [
        {
            "Latitude": "37.560210",
            "Longitude": "-122.28564",
            "Country": "US"
        }
    ]
}
</pre>

</details>

<details>
	<summary>Response - Process Option as ‘ReverseGeocode’</summary>

<pre>
{
    "output": [
        [
            {
                "Address": "999 Baker Way&lt;BR&gt;San Mateo Ca 94404",
                "Address1": "999 Baker Way",
                "Address2": "San Mateo Ca 94404",
                "AdministrativeArea": "Ca",
                "Country": "US",
                "CountryName": "United States",
                "DeliveryAddress": "999 Baker Way",
                "DeliveryAddress1": "999 Baker Way",
                "GeoDistance": "0.000000",
                "ISO3166-2": "US",
                "ISO3166-3": "USA",
                "ISO3166-N": "840",
                "Latitude": "37.560210",
                "Locality": "San Mateo",
                "Longitude": "-122.285640",
                "PostalCode": "94404",
                "PostalCodePrimary": "94404",
                "Premise": "999",
                "PremiseNumber": "999",
                "PremiseNumberRangeField": "999",
                "Sequence": "1",
                "Thoroughfare": "Baker Way"
            },
            {
                "Address": "2210 Bridgepointe Pkwy&lt;BR&gt;San Mateo Ca 94404",
                "Address1": "2210 Bridgepointe Pkwy",
                "Address2": "San Mateo Ca 94404",
                "AdministrativeArea": "Ca",
                "Country": "US",
                "CountryName": "United States",
                "DeliveryAddress": "2210 Bridgepointe Pkwy",
                "DeliveryAddress1": "2210 Bridgepointe Pkwy",
                "GeoDistance": "83.105411",
                "ISO3166-2": "US",
                "ISO3166-3": "USA",
                "ISO3166-N": "840",
                "Latitude": "37.559760",
                "Locality": "San Mateo",
                "Longitude": "-122.284150",
                "PostalCode": "94404",
                "PostalCodePrimary": "94404",
                "Premise": "2210",
                "PremiseNumber": "2210",
                "PremiseNumberRangeField": "2210",
                "Sequence": "1",
                "Thoroughfare": "Bridgepointe Pkwy"
            },
            {
                "Address": "1661 Fashion Island Blvd&lt;BR&gt;San Mateo Ca 94404",
                "Address1": "1661 Fashion Island Blvd",
                "Address2": "San Mateo Ca 94404",
                "AdministrativeArea": "Ca",
                "Country": "US",
                "CountryName": "United States",
                "DeliveryAddress": "1661 Fashion Island Blvd",
                "DeliveryAddress1": "1661 Fashion Island Blvd",
                "GeoDistance": "96.073047",
                "ISO3166-2": "US",
                "ISO3166-3": "USA",
                "ISO3166-N": "840",
                "Latitude": "37.559570",
                "Locality": "San Mateo",
                "Longitude": "-122.285320",
                "PostalCode": "94404",
                "PostalCodePrimary": "94404",
                "Premise": "1661",
                "PremiseNumber": "1661",
                "PremiseNumberRangeField": "1661",
                "Sequence": "1",
                "Thoroughfare": "Fashion Island Blvd"
            }
        ]
    ]
}
</pre>

</details>

<details>
	<summary>Request - Certify Option - Certified Data Set AMAS (AU)</summary>

<pre>
{
   "options": {
        "certify": "true"
    },
    "input": [
        {
            "Address1": "5 Highview Crescent",
            "Locality": "Modanville",
            "AdministrativeArea": "New South Wales",
            "Country": "AU"
        }
    ]
}
</pre>

</details>

<details>
	<summary>Response - Certify Option - Certified Data Set AMAS (AU)</summary>

<pre>
{
    "output": [
        {
            "AQI": "A",
            "AVC": "V44-I44-P3-100",
            "Address": "5 HIGHVIEW CRES&lt;BR&gt;MODANVILLE NSW 2480",
            "Address1": "5 HIGHVIEW CRES",
            "Address2": "MODANVILLE NSW 2480",
            "AdministrativeArea": "NSW",
            "Barcode": "1301013020020111200211313010021203313",
            "Building": "",
            "Country": "AU",
            "CountryName": "AUSTRALIA",
            "DPID": "96214624",
            "DeliveryAddress": "5 HIGHVIEW CRES",
            "DeliveryAddress1": "5 HIGHVIEW CRES",
            "ErrorCode": "213",
            "FloorNumber": "",
            "FloorType": "",
            "ISO3166-2": "AU",
            "ISO3166-3": "AUS",
            "ISO3166-N": "036",
            "Locality": "MODANVILLE",
            "LotNumber": "",
            "PostalCode": "2480",
            "PreSortZone": "18",
            "Premise": "5",
            "PrimaryAddressLine": "5 HIGHVIEW CRES",
            "PrimaryPremise": "5",
            "PrimaryPremiseSuffix": "",
            "PrintPostZone": "18",
            "SecondaryAddressLine": "",
            "SecondaryPremise": "",
            "SecondaryPremiseSuffix": "",
            "Sequence": "1",
            "SubBuildingFloor": "",
            "SubBuildingLeadingType": "",
            "SubBuildingNumber": "",
            "Thoroughfare": "HIGHVIEW CRES",
            "ThoroughfareName": "HIGHVIEW",
            "ThoroughfarePostDirection": "",
            "ThoroughfareTrailingType": "CRES"
        }
    ]
}
</pre>

</details>

<details>
	<summary>Request - Certify Option - Certified Data Set CASS (US)</summary>

<pre>
{
    "options": {
        "certify": "true"
    },
    "input": [
        {
            "Address1": "999 Baker Way STE 320",
            "Locality": "San Mateo",
            "AdministrativeArea": "California",
            "PostalCode": "94404",
            "Country": "US"
        }
    ]
}
</pre>

</details>

<details>
	<summary>Response - Certify Option - Certified Data Set CASS (US)</summary>

<pre>
{
    "output": [
        {
            "AQI": "A",
            "AVC": "V55-I55-P7-100",
            "Address": "999 BAKER WAY STE 320&lt;BR&gt;SAN MATEO CA 94404-1566",
            "Address1": "999 BAKER WAY STE 320",
            "Address2": "SAN MATEO CA 94404-1566",
            "AdministrativeArea": "CA",
            "AutoZoneIndicator": "D",
            "BusinessIndicator": " ",
            "CMRAIndicator": "N",
            "CarrierRoute": "C005",
            "CentralizedIndicator": "",
            "CheckDigit": "7",
            "CongressionalDistrict": "14",
            "Country": "US",
            "CountryName": "UNITED STATES",
            "CurbIndicator": "",
            "DPVConfirmedIndicator": "Y",
            "DPVFootnotes": "AABB",
            "DPVLACSIndicator": " ",
            "DefaultFlag": " ",
            "DeliveryAddress": "999 BAKER WAY STE 320",
            "DeliveryAddress1": "999 BAKER WAY STE 320",
            "DeliveryPointBarCode": "957",
            "DependentLocality": "",
            "DropCount": "",
            "DropSiteIndicator": " ",
            "EducationalIndicator": " ",
            "FIPSCountyCode": "081",
            "FalsePositiveIndicator": " ",
            "Finance": "056894",
            "Footnotes": "",
            "ISO3166-2": "US",
            "ISO3166-3": "USA",
            "ISO3166-N": "840",
            "LACSLinkCode": "",
            "LACSLinkIndicator": " ",
            "Locality": "SAN MATEO",
            "NDCBUIndicator": "",
            "NoStatIndicator": "N",
            "Organization": "",
            "OtherIndicator": "",
            "PMBNumber": "",
            "PMBType": "",
            "PostBoxNumber": "",
            "PostBoxType": "",
            "PostalCode": "94404-1566",
            "PostalCodePrimary": "94404",
            "PostalCodeSecondary": "1566",
            "PostalCodeSecondaryRangeHigh": "1566",
            "PostalCodeSecondaryRangeLow": "1566",
            "Premise": "999",
            "PremiseNumber": "999",
            "PrimaryAddressLine": "999 BAKER WAY STE 320",
            "PrimaryNumRangeCode": "O",
            "PrimaryNumRangeHigh": "0000000999",
            "PrimaryNumRangeLow": "0000000999",
            "RecordType": "H",
            "ResidentialDelivery": "N",
            "ReturnCode": "31",
            "SUITELinkFootnote": "",
            "SeasonalIndicator": " ",
            "SecondaryAddressLine": "SAN MATEO CA 94404-1566",
            "SecondaryNumRangeCode": "E",
            "SecondaryNumRangeHigh": "00000320",
            "SecondaryNumRangeLow": "00000320",
            "Sequence": "1",
            "SubAdministrativeArea": "SAN MATEO",
            "SubBuilding": "STE 320",
            "SubBuildingLeadingType": "STE",
            "SubBuildingNumber": "320",
            "Thoroughfare": "BAKER WAY",
            "ThoroughfareName": "BAKER",
            "ThoroughfarePostDirection": "",
            "ThoroughfarePreDirection": "",
            "ThoroughfareTrailingType": "WAY",
            "ThrowbackIndicator": "N",
            "VacantIndicator": "Y",
            "eLOTCode": "A",
            "eLOTNumber": "0074"
        }
    ]
}
</pre>

</details>

<details>
	<summary>Request - Certify Option - Certified Data Set SERP (CA)</summary>

<pre>
{
    "options": {
        "certify": "true"
    },
    "input": [
        {
            "Address1": "8 Charlotte St # 8, Toronto, Ontario, M5V 0K4",
            "Country": "CA"
        }
    ]
}
</pre>

</details>

<details>
	<summary>Response - Certify Option - Certified Data Set SERP (CA)</summary>

<pre>
{
    "output": [
        {
            "AQI": "C",
            "AVC": "V55-I55-P6-091",
            "AddInfo": "",
            "Address": "8-8 CHARLOTTE ST&lt;BR&gt;TORONTO ON  M5V 0K4",
            "Address1": "8-8 CHARLOTTE ST",
            "Address2": "TORONTO ON  M5V 0K4",
            "AdministrativeArea": "ON",
            "Country": "CA",
            "CountryName": "CANADA",
            "DeliveryAddress": "8-8 CHARLOTTE ST",
            "DeliveryAddress1": "8-8 CHARLOTTE ST",
            "EndExpiryDate": "2022-06-16",
            "ISO3166-2": "CA",
            "ISO3166-3": "CAN",
            "ISO3166-N": "124",
            "Locality": "TORONTO",
            "PostalCode": "M5V 0K4",
            "PostalCodePrimary": "M5V 0K4",
            "Premise": "8",
            "PremiseNumber": "8",
            "Result": "VALID",
            "Sequence": "1",
            "SerpStatusEx": "V",
            "StartExpiryDate": "2022-05-13",
            "SubBuilding": "8",
            "SubBuildingNumber": "8",
            "SubBuildingType": "-",
            "Thoroughfare": "CHARLOTTE ST",
            "ThoroughfareName": "CHARLOTTE",
            "ThoroughfareTrailingType": "ST",
            "ThoroughfareType": "ST"
        }
    ]
}
</pre>

</details>

<details>
	<summary>Request - Enhance Option to Return Enhanced Data Set</summary>

<pre>
{
    "options": {
        "Enhance": "true"
    },
    "input": [
        {
            "Address1": "805 Veterans Street RedwoodCity california USA",
            "PostalCode": "",
            "Locality": "",
            "AdministrativeArea": "",
            "Country": ""
        }
    ]
}
</pre>

</details>

<details>
	<summary>Response - Enhance Option to Return Enhanced Data Set</summary>

<pre>
{
    "output": [
        {
            "AQI": "C",
            "AVC": "V42-I44-P3-092",
            "Address": "805 Veterans Blvd&lt;BR&gt;Redwood City CA 94063-1734",
            "Address1": "805 Veterans Blvd",
            "Address2": "Redwood City CA 94063-1734",
            "AdministrativeArea": "CA",
            "AdministrativeAreaISO2": "US-CA",
            "BlockCode": "2018",
            "BlockGroupCode": "2",
            "CBSACode": "41860",
            "CBSAName": "San Francisco-Oakland-Berkeley, Ca Metro Area",
            "CensusClassCode": "C1",
            "CensusCode": "60102",
            "CensusIndicator": "Premise",
            "Country": "US",
            "CountryName": "United States",
            "CountyCode": "081",
            "DeliveryAddress": "805 Veterans Blvd",
            "DeliveryAddress1": "805 Veterans Blvd",
            "GNISFeatureID": "2410919",
            "HyphenClass": "C",
            "ISO3166-2": "US",
            "ISO3166-3": "USA",
            "ISO3166-N": "840",
            "Locality": "Redwood City",
            "MatchRuleLabel": "C2 d1 d1 d1 d1",
	    "MetroDivisionCode": "41884",
            "MetroDivisionName": "San Francisco-San Mateo-Redwood City, Ca",
            "OldMSACode": "7360",
            "OldMSAName": "San Francisco, Ca Pmsa",
            "PostalCode": "94063-1734",
            "PostalCodePrimary": "94063",
            "PostalCodeSecondary": "1734",
            "Premise": "805",
            "PremiseNumber": "805",
            "ResCom_Flag": "Com",
            "Sequence": "1",
            "SubAdministrativeArea": "San Mateo",
            "Thoroughfare": "Veterans Blvd",
            "ThoroughfareName": "Veterans",
            "ThoroughfareTrailingType": "St",
            "ThoroughfareType": "St",
            "TimeZone_DST": "-07:00",
            "TimeZone_Name": "Pacific Standard Time",
            "TimeZone_UTC": "-08:00"
            "TractCode": "610202"
        }
    ]
}
</pre>

</details>

<details>
	<summary>Request - Server Options</summary>

<pre>
{
    "Options": {
        "ServerOptions": {
            "OutputAddressFormat": "true",
            "OutputScript": "Native"
        }
    },
    "input": [
        {
            "Address": "1290 Mukawachoyanagisawa,Hokuto-Shi Yamanashi 408-0307",
            "Country": "JP"
        }
    ]
}
</pre>

</details>

<details>
	<summary>Response - Server Options</summary>

<pre>
{
    "output": [
        {
            "AQI": "A",
            "AVC": "V44-I44-P6-100",
            "Address": "４０８－０３０７&lt;BR&gt;山梨県北杜市武川町柳澤１２９０",
            "Address1": "４０８－０３０７",
            "Address2": "山梨県北杜市武川町柳澤１２９０",
            "AddressFormat": "PostalCode&lt;BR&gt;AdministrativeAreaLocalityThoroughfarePremise",
            "AdministrativeArea": "山梨県",
            "Country": "JP",
            "CountryName": "Japan",
            "DeliveryAddress": "武川町柳澤１２９０",
            "DeliveryAddress1": "武川町柳澤１２９０",
            "DeliveryAddressFormat": "ThoroughfarePremise",
            "HyphenClass": "C",
            "ISO3166-2": "JP",
            "ISO3166-3": "JPN",
            "ISO3166-N": "392",
            "Locality": "北杜市",
            "MatchRuleLabel": "V1a",
            "PostalCode": "４０８－０３０７",
            "PostalCodePrimary": "４０８－０３０７",
            "Premise": "１２９０",
            "PremiseNumber": "１２９０",
            "Sequence": "1",
            "Thoroughfare": "武川町柳澤"
        }
    ]
}
</pre>

</details>

<br />

## Using the AI Parser

### AI Data Storage

The AI Parser requires a Persistent Volume, ideally different from the main data storage. The data is downloaded using `installaidata` chart and accessed by the `ai-parse` and `tfserve` charts. The provided yaml files mount a local volume and will need updating/replacing with the details of your PV. The `values.yaml` files for both charts have `storage` properties for configuring the PV.

Currently, to store the global data set you will need at least 4Gi of storage.

These charts will work with any RWX (ReadWriteMany) Persistent Volume, but faster storage will produce better response times.

_[Read more](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes) about k8s Persistent Volumes and their Access Modes._

### AI Parser Components

- **Install AI Data:** installs the data models on a Persistent Volume (PV)
- **AI Parser:** prepares address data for TensorFlow and collates the results
- **TFServe:** TensorFlow server that uses the data models to identify address elements

### AI Parser Helmfile

The helmfile contains releases for using the AI Parser.  Not everyone will want to use the AI parser, so they are commented out by default.
If you want to use the AI Parser, it is recommended that you get the base Verify installation working first, then uncomment the AI Parser sections.

NOTE: This is now turned on by default

To enable the use of AI parsing you need to enable it by passing a value to Query Coordinator.  In `helmfile.yaml`, in the `querycoordinator` release, under `values:` add the following:

``` yml
        app:
          allow_aiparser: true
```

The helmfile uses environment variables to enable you to override the default storage options.  These are:

- LOQATE_AIPARSER_STORAGE_TYPE - use to define a PV, accepted values 'local' and 'nfs', default 'local'
- LOQATE_AIPARSER_STORAGE_PATH - use to define a PV, describes the path where the data is stored, default "/run/desktop/mnt/host/c/loqate/models_structured"
- LOQATE_AIPARSER_STORAGE_SERVER - use to define a PV, the IP address of your NFS server, default is empty
- LOQATE_AIPARSER_CLAIM_OVERRIDE - use to connect to a pre-existing PVC, default is empty

To use the default path, which is configured for use on Windows using docker-desktop, do not set the environment variables.

To use a different local path, i.e. a host path accessible from all pods:

**Unix:**

``` bash
export LOQATE_AIPARSER_STORAGE_PATH="<HOST PATH>"
```

**Windows:**

``` powershell
$env:LOQATE_AIPARSER_STORAGE_PATH="<HOST PATH>"
```

To use an NFS share and have the PV and PVC created for you:

**Unix:**

``` bash
export LOQATE_AIPARSER_STORAGE_TYPE="nfs"
export LOQATE_AIPARSER_STORAGE_SERVER="<NFS SERVER IP ADDRESS>"
export LOQATE_AIPARSER_STORAGE_PATH="<NFS PATH>"
```

**Windows:**

``` powershell
$env:LOQATE_AIPARSER_STORAGE_TYPE="nfs"
$env:LOQATE_AIPARSER_STORAGE_SERVER="<NFS SERVER IP ADDRESS>"
$env:LOQATE_AIPARSER_STORAGE_PATH="<NFS PATH>"
```

To use a PVC you have already created:

**Unix:**

``` bash
export LOQATE_AIPARSER_CLAIM_OVERRIDE="<CLAIM NAME>"
```

**Windows:**

``` powershell
$env:LOQATE_AIPARSER_CLAIM_OVERRIDE="<CLAIM NAME>"
```

Once the storage is configured, simply uncomment the AI Parser sections and apply/sync the helmfile as usual.

**Unix:**

``` bash
helmfile apply
```

**Windows:**

``` powershell
helmfile sync
```

### AI Parser Helm

#### Installing the Data Models

To enable the use of AI parsing you need to enable it by passing a value to Query Coordinator.  When installing querycoordinator set the `app.allow_aiparser` value to true:

``` shell
helm install -n loqate querycoordinator loqate/querycoordinator --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set app.memberlistService=memberlist.loqate.svc --set app.allow_aiparser=true
```

The helm charts use a number of values to enable you to override the default storage options.  These are:

- storage.type - use to define a PV, accepted values 'local' and 'nfs', default 'local'
- storage.path - use to define a PV, describes the path where the data is stored, default "/run/desktop/mnt/host/c/loqate/models_structured"
- storage.server - use to define a PV, the IP address of your NFS server, default is empty
- storage.claimOverride - use to connect to a pre-existing PVC, default is empty

To use the default path:

``` shell
helm install -n loqate installaidata loqate/installaidata --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD>
```

To use a different local path, i.e. a host path accessible from all pods:

``` shell
helm install -n loqate installaidata loqate/installaidata --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set storage.path=<DATA FOLDER>
```

You can provide details to an NFS share using the first three values, in which case the PV and PVC will be created for you:

``` shell
helm install -n loqate installaidata loqate/installaidata --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set storage.type=nfs --set storage.path=<NFS FOLDER> --set storage.server=<NFS IP ADDR>
```

Alternatively, you can create your own PV & PVC and use the name of the claim:

``` shell
helm install -n loqate installaidata loqate/installaidata --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set storage.claimOverride=<CLAIM NAME>
```

#### Installing the AI Parser Components

Both the `aiparser` and `tfserve` components need access to the data installed above.  Similarly, you need to specify the storage options used above.

To use the default paths:

``` shell
helm install -n loqate ai-parse loqate/ai-parse --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD>

helm install -n loqate tfserve loqate/tfserve --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD>
```

To use a different local path:

``` shell
helm install -n loqate ai-parse loqate/ai-parse --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set storage.path=<DATA FOLDER>

helm install -n loqate tfserve loqate/tfserve --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set storage.path=<DATA FOLDER>
```

To use an NFS share:

``` shell
helm install -n loqate ai-parse loqate/ai-parse --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set storage.type=nfs --set storage.path=<NFS FOLDER> --set storage.server=<NFS IP ADDR>

helm install -n loqate tfserve loqate/tfserve --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set storage.type=nfs --set storage.path=<NFS FOLDER> --set storage.server=<NFS IP ADDR>
```

To use an existing PVC:

``` shell
helm install -n loqate ai-parse loqate/ai-parse --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set storage.claimOverride=<CLAIM NAME>

helm install -n loqate tfserve loqate/tfserve --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set storage.claimOverride=<CLAIM NAME>
```

### AI Parser Requests

For requests to use AI Parsing, they need to have an extra option set:

<details>
	<summary>Request - AI Parse Option</summary>

<pre>
{
    "options": {
        "aiparse": "true"
    },
    "input": [
        {
            "Address": "Commander Naval Air Force Box 357051 Nasni San Diego CA 92135",
            "Country": "US"
        }
    ]
}
</pre>
</details>

<details>
	<summary>Response - AI Parse Option</summary>

<pre>
{
    "output": [
        {
            "AQI": "C",
            "AVC": "V55-I55-P7-092",
            "Address": "Air Commander Naval Nasni San<BR>Box 357051<BR>San Diego CA 92135-7051",
            "Address1": "Air Commander Naval Nasni San",
            "Address2": "Box 357051",
            "Address3": "San Diego CA 92135-7051",
            "AdministrativeArea": "CA",
            "BoxType": "Box",
            "Country": "US",
            "CountryName": "United States",
            "DeliveryAddress": "Box 357051",
            "DeliveryAddress1": "Box 357051",
            "HyphenClass": "C",
            "ISO3166-2": "US",
            "ISO3166-3": "USA",
            "ISO3166-N": "840",
            "ISO31662": "Ca",
            "ISO31663": "Can",
            "Locality": "San Diego",
            "MatchRuleLabel": "C1a",
            "Organization": "Air Commander Naval Nasni San",
            "OrganizationName": "Air Commander Naval Nasni San",
            "PostBox": "Box 357051",
            "PostBoxNumber": "357051",
            "PostBoxType": "Box",
            "PostalCode": "92135-7051",
            "PostalCodePrimary": "92135",
            "PostalCodeSecondary": "7051",
            "Sequence": "1",
            "SubAdministrativeArea": "San Diego"
        }
    ]
}
</pre>
</details>

<details>
	<summary>Response - Without AI Parse Option</summary>

<pre>
{
    "output": [
        {
            "AQI": "C",
            "AVC": "P22-I25-P6-100",
            "Address": "Commander Naval Air Force<BR>Nasni Box 357051<BR>San Diego CA 92135",
            "Address1": "Commander Naval Air Force",
            "Address2": "Nasni Box 357051",
            "Address3": "San Diego CA 92135",
            "AdministrativeArea": "CA",
            "BoxNumber": "357051",
            "BoxType": "Box",
            "Country": "US",
            "CountryName": "United States",
            "DeliveryAddress": "Nasni Box 357051",
            "DeliveryAddress1": "Nasni Box 357051",
            "HyphenClass": "C",
            "ISO3166-2": "US",
            "ISO3166-3": "USA",
            "ISO3166-N": "840",
            "Locality": "San Diego",
            "MatchRuleLabel": "C6",
            "Organization": "Commander Naval Air Force",
            "OrganizationName": "Commander Naval Air Force",
            "PostalCode": "92135",
            "PostalCodePrimary": "92135",
            "Premise": "Box 357051",
            "PremiseNumber": "Box 357051",
            "Sequence": "1",
            "SubAdministrativeArea": "San Diego",
            "Thoroughfare": "Nasni",
            "ThoroughfareName": "Nasni"
        }
    ]
}
</pre>
</details>

## Troubleshooting

In this section we've put together some suggestions for how to handle commonly-found errors or problems.

Check the logs of querycoordinator and spatial-api pods for the following:

### Errors with Memberlist

- “No private IP address found, and explicit IP not provided”
- “...(*Memberlist).UpdateStatus(0x0, 0x0, 0x0, 0x1)”

Solution:

Set the value `memberlist.useAddrRef` to `true` for both querycoordinator and spatial-api.

### Errors with Data

- “...is not a valid Global Knowledge Repository path”

Solutions:

Occasionally, not all the data is downloaded successfully or there is not enough room to unpack the data.

Make sure your install manager finished properly as per the [Checking the Progress of the Data Installation](#checking-the-progress-of-the-data-installation) section.

If the log says there is not enough space, then the storage size should be increased.

If the log does not contain the following lines, repeat the installation process with minimal data as outlined in [Download Just a Subset of the Allowed Datasets](#download-just-a-subset-of-the-allowed-datasets)

```text
Completed installing the data packs.
Datapack installation was successfull.
```

If the minimal install works, run the full install again.  It may take multiple runs to install the complete set of data.

### Errors with Requests

- “No spatialapi available”
- “Failed to process”

Solution:

- Find the pods names using:

```bash
kubectl get pods -n loqate
```

- If any of the pods are not in service (i.e. 0/1) then delete the pod as shown below. Wait 3 minutes. If the pods is in service test again. Try this a couple times if it does not work at first.

```bash
 kubectl delete pods <NAME> -n loqate
```

If all pods are in service (i.e. 1/1) but you are still getting the “No spatialapi available” or “Failed to process” then:

- Delete the spatial-api pod. Wait three minutes and test again
- If it does not work then do the same for the querycoordinator pod
- If you are still getting the errors then delete the spatial-api pod once more and wait three minutes then test again

If the above has still not fixed the issue then try the following:

- If any of the pods are not in service (i.e. 0/1) then delete the equivalent chart and reinstall as shown below

- To delete a release, first get a list of charts:

```bash
helm list -n loqate
```

- Then delete the release as follows:

```bash
helm delete -n loqate <NAME>
```

- Reinstall the chart as shown in the [Install Charts](#install-charts) or [Certified Datasets (CASS, SERP, AMAS)](#certified-datasets-cass-serp-amas) section.

- Check the pod is back in service using:

```bash
kubectl get pods -n loqate
```

- Wait three minutes then try your query again.

If you are still getting the error, delete each release and reinstall one at a time

- Test after the release comes back up, after waiting an extra three minutes. Do this in the following order:
  - First spatial-api, then querycoordinator.
  - You may have to repeat this a few times (i.e. spatial-api, querycoordinator, spatial-api …)

If the error has still not been resolved, please contact <support@loqate.com> to arrange a further discussion.

## TERMS AND CONDITIONS FOR USE

### 1. DEFINITIONS

- **“Licence”** shall mean the Terms and Conditions for use of the Loqate Verify Software as defined by Clauses 1 – 4 (inclusive) below.
- **“Customer Entity”** means any Customer Group Company that receives or benefits from the Loqate Verify Software provided by GBG.
- **“Datasets”** means an individual data service included or delivered as part of the Software and/or Service and selected by the Customer and referenced on the Order Form. Where applicable, this may incorporate Supplier Data or Supplier Technology or utilise information derived from Supplier Data or Supplier Technology.
- **“Service”** means the service provided by GBG, including any and all Datasets, provided to the Customer in accordance with an agreement together with any other ancillary services provided by GBG to a customer pursuant to an agreement.
- **“Software”** means the GBG product or solution, including any and all Datasets, provided to the Customer in accordance with an Agreement together with any other ancillary services provided by GBG to the Customer pursuant to an agreement.
- **“You”** shall mean an individual or Customer Entity exercising permissions granted by this Licence.

### 2. LICENCE

2.1 Subject to the terms and conditions of this Licence, GBG hereby grants to You a perpetual, worldwide, non-exclusive, no-charge, royalty-free, revocable licence to use the Loqate Verify Software provided always that such use of the Loqate Verify Software is with a GBG Service and / or Software only.

2.2 By using the  Loqate Verify Software You agree that:

2.2.1 You will not use or exploit the Intellectual Property Rights in the Loqate Verify Software or permit others to use or exploit the Intellectual Property Rights in the Loqate Verify Software outside of the terms of the Licence;

2.2.2 Your use of the Loqate Verify Software through any software, equipment, materials or services not provided by GBG will not infringe the rights of any third party.

### 3. DISCLAIMER OF WARRANTY

Unless required by applicable law or agreed to in writing, GBG provides the Verify Software on an "as is" basis. Any and all warranties, conditions and other terms relating to the Verify Software whether express or implied by law, custom or otherwise are, to the fullest extent permitted by law, excluded from the Licence. GBG shall not be responsible for the decisions that You make as a result of the information, Verify Software or data that GBG provides to the You. You are solely responsible for determining the appropriateness of using the Loqate Verify Software and assume any risk associated with Your exercise of permissions under this Licence.

### 4. LIMITATION OF LIABILITY

In no event shall GBG be liable to You , whether such liability arises in contract, tort (including, without limitation, negligence) misrepresentation or otherwise for damages, including any direct, indirect, special, incidental, or consequential damages whatsoever arising as a result of this Licence or out of the use or inability to use the Loqate Verify Software (including but not limited to damages for loss of goodwill, work stoppage, computer failure or malfunction, or any and all other commercial damages or losses), even if GBG has been advised of the possibility of such damages.

# Chart and Application Version Information 

Secret Chart Version: 0.1.0

Secret App Version: 1.16.0

SpatialAPI Chart Version: 0.5.3

SpatialAPI App Version: 0.1.97547

Memberlist Chart Version: 0.1.0

Memberlist App Version: 0.1.0

QueryCoordinator Chart Version: 0.4.0

QueryCoordinator App Version: 0.1.97544

InstallManager Chart Version: 0.2.7

InstallManager App Version: 0.1.22799

InstallAIData Chart Version: 0.0.5

InstallAIData App Version: 0.1.37868

