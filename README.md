# Loqate Verify

- [Loqate Verify](#loqate-verify)
  - [The Next Generation of Address Verification & Cleansing](#the-next-generation-of-address-verification--cleansing)
    - [A Quick Overview of Verify’s Capabilities](#a-quick-overview-of-verifys-capabilities)
    - [What Next Generation Verify offers you](#what-next-generation-verify-offers-you)
    - [Security](#security)
  - [Prerequisites](#prerequisites)
    - [Reference Data Storage](#reference-data-storage)
    - [Routing](#routing)
    - [Scaling](#scaling)
  - [How it Works](#how-it-works)
    - [Components](#components)
      - [InstallManager](#installmanager)
      - [Memberlist](#memberlist)
      - [Spatial-API](#spatial-api)
      - [QueryCoordinator](#querycoordinator)
  - [Quick Start](#quick-start)
    - [Download just a subset of the allowed datasets](#download-just-a-subset-of-the-allowed-datasets)
    - [Checking Progress of Data Installation](#checking-progress-of-data-installation)
    - [Testing the Installation](#testing-the-installation)
  - [Helmfile](#helmfile)
    - [Install](#install)
    - [Uninstall](#uninstall)
    - [Config Values](#config-values)
  - [Helm](#helm)
    - [Docker Images](#docker-images)
    - [Add Repo](#add-repo)
    - [Create namespace](#create-namespace)
    - [Install Data](#install-data)
    - [Install Charts](#install-charts)
    - [Upgrade Chart](#upgrade-chart)
    - [Uninstall Chart](#uninstall-chart)
    - [Delete namespace](#delete-namespace)
  - [Important Configuration Settings](#important-configuration-settings)
    - [Adding Country Specific Deployments](#adding-country-specific-deployments)
    - [Certified Datasets (CASS, SERP, AMAS)](#certified-datasets-cass-serp-amas)
  - [Usage](#usage)
    - [Functions](#functions)
    - [Verify Request](#verify-request)
      - [Request Parameters](#request-parameters)
      - [Response Fields](#response-fields)
    - [Request and Response Format](#request-and-response-format)
      - [Request - Geocode as true](#request---geocode-as-true)
      - [Response - Geocode as true](#response---geocode-as-true)
      - [Request - Process option as ‘Verify’](#request---process-option-as-verify)
      - [Response - Process option as ‘Verify’](#response---process-option-as-verify)
      - [Request - Process option as ‘Search’](#request---process-option-as-search)
      - [Response - Process option as ‘Search’](#response---process-option-as-search)
      - [Request - Process option as ‘ReverseGeocode’](#request---process-option-as-reversegeocode)
      - [Response - Process option as ‘ReverseGeocode’](#response---process-option-as-reversegeocode)
      - [Request - Certify option - Certified data set AMAS (AU)](#request---certify-option---certified-data-set-amas-au)
      - [Response - Certify option - Certified data set AMAS (AU)](#response---certify-option---certified-data-set-amas-au)
      - [Request - Certify option - Certified data set CASS (US)](#request---certify-option---certified-data-set-cass-us)
      - [Response - Certify option - Certified data set CASS (US)](#response---certify-option---certified-data-set-cass-us)
      - [Request - Certify option - Certified data set SERP (CA)](#request---certify-option---certified-data-set-serp-ca)
      - [Response - Certify option - Certified data set SERP (CA)](#response---certify-option---certified-data-set-serp-ca)
      - [Request - Enhance option to return enhanced data set](#request---enhance-option-to-return-enhanced-data-set)
      - [Response - Enhance option to return enhanced data set](#response---enhance-option-to-return-enhanced-data-set)
      - [Request - Server Options](#request---server-options)
      - [Response - Server Options](#response---server-options)
  - [Trouble Shooting](#trouble-shooting)
    - [Re-run quick start without data download](#re-run-quick-start-without-data-download)
    - [Full system cleanup](#full-system-cleanup)
      - [Helmfile only cleanup commands](#helmfile-only-cleanup-commands)
    - [Helmfile and Helm cleanup commands](#helmfile-and-helm-cleanup-commands)
      - [Delete the namespace](#delete-the-namespace)
      - [Delete the persistent volumes](#delete-the-persistent-volumes)
    - [Then Check that the system has cleaned up](#then-check-that-the-system-has-cleaned-up)
  - [TERMS AND CONDITIONS FOR USE](#terms-and-conditions-for-use)
    - [1. DEFINITIONS](#1-definitions)
    - [2. LICENCE](#2-licence)
    - [3. DISCLAIMER OF WARRANTY](#3-disclaimer-of-warranty)
    - [4. LIMITATION OF LIABILITY](#4-limitation-of-liability)

[Jump to quickstart Installation](#quick-start)

## The Next Generation of Address Verification & Cleansing

Combining the trusted power of Loqate’s leading Global Address Knowledge Base and simple to use APIs with modern web-scale architectural approaches; Next Gen Verify is the enterprise ready solution for global address parsing, standardising, cleansing, and enhancing.

### A Quick Overview of Verify’s Capabilities

The Loqate solution will parse, standardise, verify, cleanse and format address data via a single, easy to integrate API for over 245 countries and territories. Our solution has a proven track record in providing customers with global data coupled powered by our superior parsing and matching engine.

- The Global Knowledge Repository (GKR) is Loqate’s proprietary main database which combines our Knowledge Base data and parsing rules with our location reference datasets for over 245 countries and territories.

- Loqate’s Global Parsing Engine consists of proprietary lexicon and context parsing rules created specifically for each country and territory around the world. The parsing engine standardises and cleanses addresses from unstructured and semi-structured data, automatically placing elements into the correct fields and eliminating erroneous non-address data.

- Our address cleansing engine is capable of processing millions of records per hour and beyond. You’ll see an indication of the validity of each address value included in the input address, reducing costly errors. This solution can validate both partial and full address inputs.

- Loqate’s solution transliterates words or letters from different global character sets into either native or Roman characters across 8 scripts including: Cyrillic, Hellenic, Hebrew, Kanji, Simplified Chinese, Arabic, Thai, Hangul.

_See [loqate.com](https://www.loqate.com/resources/support/cleanse-api/international-batch-cleanse/) for field descriptions and server options._

### What Next Generation Verify offers you

We recognise that our partners need solutions which enable them to manage their cloud estate seamlessly, using modern container orchestration to keep cost under control whilst scaling to meet demand.

With Next Generation Verify (NGV) we provide you with the same implementation we run. Providing out of the box helm charts which allow you to deploy and run in your own cloud infrastructure with ease.

Observability is provided using Open Telemetry traces, stdout logging, and prometheus metrics, enabling you to gain insights into the NGV cluster.

### Security

For vulnerability disclosure or security findings, please contact your account representative.

## Prerequisites

- Mandatory
  - Kubernetes [v1.19+]
  - Helm [v3.7.0]
- Recommended
  - Helmfile [v0.144.0]
  - Istio [v1.11.4]
  - Keda [v2.4]
  - Prometheus [v2.34.0]

_Verify has been tested using the version numbers stated above. Please avoid using earlier application versions than those stated._

### Reference Data Storage

The reference data is accessed using a Persistent Volume (PV).  It is downloaded using the `installmanager` chart and accessed by the `spatial-api` chart.  The provided yaml files mount a local volume and will need updating/replacing with the details of your PV.  The `values.yaml` files for both charts have `storage` properties for configuring the PV.

Currently, to store all the data you will need at least 250Gi of storage and downloading it will take several hours.

These charts will work with any RWX (ReadWriteMany) Persistent Volume, but faster storage will produce better response times.

_[Read more](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes) about k8s Persistent Volumes and their Access Modes_

### Routing

Is handled by either Istio or a Kubernetes Ingress.  The default is Ingress.

If you want to use Ingress, you will need an Ingress controller.  The ingress resource only configures the controller, it does not install it.

_[Read more](https://kubernetes.io/docs/concepts/services-networking/ingress/#prerequisites) about the pre-requisites for getting Ingress to work._

### Scaling

Each of the querycoordinator and spatial-api components can be scaled with either HPA or KEDA.  By default they both use HPA but they can be set independently.

_[Read more](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) about HPA and [Learn more](https://keda.sh/resources/) about KEDA_

## How it Works

By having a Spatial-API deployment for the most searched countries, plus a catch-all deployment for the rest of the world (ROW), you can provision and scale each deployment according to expected traffic.

See the `values.yaml` files for resourcing spatialapi and querycoordinator.  The commented out values in these files are able to serve requests for the whole world but are likely to change.

### Components

#### InstallManager

Downloads and installs the data on a Persistent Volume (PV).

#### Memberlist

Used to co-ordinate communication between components

#### Spatial-API

Searches and caches data for one country or all countries

#### QueryCoordinator

Forwards requests to the appropriate Spatial-API

## Quick Start

This quick start uses [helmfile](#helmfile)
 and, on Unix, the helm plugin _helm-diff_.  If you do not or cannot use helmfile, see the section for [helm](#helm).

Note: Whether you are using helm or helmfile, please pay particular attention to the [important configuration settings](#important-configuration-settings).

Unix:

``` bash
mkdir lqtcharts && cd lqtcharts/

wget https://charts.loqate.com/helmfile.yaml -O helmfile.yaml

export LICENSE_KEY="<API_KEY>"
export DOCKER_USERNAME="docker_username"
export DOCKER_PASSWORD="docker_password"

helmfile apply
```

Windows:

``` powershell
mkdir lqtcharts
cd lqtcharts

Invoke-WebRequest https://charts.loqate.com/helmfile.yaml -OutFile helmfile.yaml

$env:LICENSE_KEY="<API_KEY>"
$env:DOCKER_USERNAME="docker_username"
$env:DOCKER_PASSWORD="docker_password"

helmfile sync
```

Note: If you want to run the quick start again without a data download. See [Re-run quick start without data download](#re-run-quick-start-without-data-download).

### Download just a subset of the allowed datasets

If you have a license that allows lots of datasets but you only want to download a subset of them.
In the helmfile under the app section shown below. Add 'products: "KBCOMMON,DSVGBR"' as shown. This will add the datasets KBCOMMON and DSVGBR:

``` yml
name: installmanager
    values:
      - image:
        app:
          products: "KBCOMMON,DSVGBR"
``` 

### Checking Progress of Data Installation

To check the progress of the data installation, first get the name of the installmanager pod:

``` bash
kubectl get pods -n loqate
```

Example output:

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

The NGV installation can be tested by sending requests through the system.  First, open a port to receive requests.  You may want to do this in a new shell because the command does not terminate.

``` bash
kubectl port-forward -n loqate svc/querycoordinator 8900:8900
```

A version request will determine if the software and data was successfully installed:

Unix:

``` bash
curl -X GET http://localhost:8900/api/version
```

Successful response:

``` json
{"version":"2.44.0.16383-1f51bc7"}
```

Windows:

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

Although the version request is sufficient to prove the installation worked, here is a verify request for confirmation.

Unix:

``` bash
curl -X POST http://localhost:8900/verify -d '{"input":[{"Address1":"TheFoundation","Locality":"Chester","Country":"GB"}]}'
```

Successful output:

``` json
{"output":[{"AQI":"A","AVC":"P44-I44-P0-100","Address":"The Foundation\u003cBR\u003eHerons Way\u003cBR\u003eChester Business Park\u003cBR\u003eChester","Address1":"The Foundation","Address2":"Herons 
Way","Address3":"Chester Business Park","Address4":"Chester","AdministrativeArea":"Cheshire","Building":"The Foundation","Country":"GB","CountryName":"United Kingdom","DeliveryAddress":"The Foundation\u003cBR\u003eHerons Way\u003cBR\u003eChester Business Park","DeliveryAddress1":"The Foundation","DeliveryAddress2":"Herons Way","DeliveryAddress3":"Chester Business Park","DependentLocality":"Chester Business Park","HyphenClass":"B","ISO3166-2":"GB","ISO3166-3":"GBR","ISO3166-N":"826","Locality":"Chester","MatchRuleLabel":"1","Sequence":"1","Thoroughfare":"Herons Way"}]}
```

Windows:

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

## Helmfile

Helmfile can be used to easily install all components in a simple K8s environment. It will automatically pull from the Loqate charts repository.

helm diff is a plugin used by helmfile and will need to be installed to helm.  This appears to not work on Windows.

```helm plugin install https://github.com/databus23/helm-diff```

More information on helmfile as well as how to use it can be found here: <https://github.com/helmfile/helmfile>

An example helmfile can be downloaded from <https://charts.loqate.com/helmfile.yaml>

### Install

Unix:

``` bash
helmfile apply
```

Windows:

``` powershell
helmfile sync
```

### Uninstall

```helmfile delete```

### Config Values

Location of locally available GKR data to mount.  This needs to be set the same for both installmanager and spatial-api.

Unix:

``` yml
storage:
  path: /opt/loqate/data
```

Windows using docker desktop:

``` yml
storage:
  path: /run/desktop/mnt/host/c/loqate/data
```

Setting license key & products for installmanager

``` yml
- app:
  licenseKey: {{ requiredEnv "LICENSE_KEY" | quote }}
  products: "ALL"
```

To download a subset of the datasets on your license. Use a comma separated list of the datasets as follows:

``` yml
- app:
  licenseKey: {{ requiredEnv "LICENSE_KEY" | quote }}
  products: "KBCOMMON,DSVGBR"
```

Also see [Certified Datasets (CASS, SERP, AMAS)](#certified-datasets-cass-serp-amas)

Setting dataset for spatial-api

``` yml
verify:
  dataset: "row"
```

For more information, see [Important Configuration Settings](#important-configuration-settings)

## Helm

### Docker Images

The components installmanager, spatial-api, and querycoordinator have associated docker images.  These docker images are hosted in private repositories on docker hub, so you will need a docker hub account for us to grant access to the repositories.

### Add Repo

``` bash
helm repo add loqate https://charts.loqate.com
```

_See [helm repo](https://helm.sh/docs/helm/helm_repo/) for command documentation._

### Create namespace

``` bash
kubectl create namespace loqate
```

### Install Data

``` bash
helm install -n loqate im loqate/installmanager --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set app.licenseKey=<LICENSE KEY>
```

To install a subset of the datasets you have in the license key you can add  --set app.products="KBCOMMON,DSVGBR" as follows:

``` bash
helm install -n loqate im loqate/installmanager --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set app.licenseKey=<LICENSE KEY> --set app.products="KBCOMMON,DSVGBR" 
```

Ensure the download is fully completed before continuing, see [Checking Progress of Data Installation](#checking-progress-of-data-installation).

### Install Charts

``` bash
helm install -n loqate ml loqate/memberlist
helm install -n loqate sa loqate/spatial-api --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set app.memberlistService=ml-memberlist.loqate.svc
helm install -n loqate qc loqate/querycoordinator --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set app.memberlistService=ml-memberlist.loqate.svc
```

The _memberlistService_ name is composed of `<MEMBERLIST.RELEASE_NAME>-<MEMBERLIST.CHART_NAME>.<NAMESPACE>.svc`, changing any of these will require changing the set arguments to _spatial-api_ and _querycoordinator_.

_See [helm install](https://helm.sh/docs/helm/helm_install/) for command documentation._

### Upgrade Chart

``` bash
helm upgrade <RELEASE_NAME> <CHART> --install
```

The example below is an upgrade when you get a new license key with a new data set added.

``` bash
helm upgrade -n loqate im loqate/installmanager --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set app.licenseKey=<LICENSE KEY>
```

_See [helm upgrade](https://helm.sh/docs/helm/helm_upgrade/) for command documentation._

### Uninstall Chart

``` bash
helm uninstall -n loqate <RELEASE_NAME>
```

### Delete namespace

This removes all the Kubernetes components associated with the chart and deletes the release.

``` bash
kubectl delete namespace loqate
```

This deletes the kubernetes namespace that was created for the Helm release.

_See [helm uninstall](https://helm.sh/docs/helm/helm_uninstall/) for command documentation._

## Important Configuration Settings

### Adding Country Specific Deployments

To get the best performance and flexible scaling, we recommend having spatial-api deployments dedicated to countries you anticipate will be serving a large number of requests.

To create a country specific deployment, set the `verify.dataset` value to the ISO3166-2 code for that country.  For example, a GB deployment is created with:

``` bash
helm install -n loqate sa-gb loqate/spatial-api --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set app.memberlistService=ml-memberlist.loqate.svc --set verify.dataset=gb
```

### Certified Datasets (CASS, SERP, AMAS)

To use any of the certified datasets, extra libraries are required.  Given an appropriate license key, these will be downloaded and installed alongside the data, in sub-folder `lib64`.  For spatial-api deployments to know where these libraries are, you will need to set the `app.libraryPath` value accordingly.  The default value is `/lib64`, which needs to remain in the path list.

Example, to create a US deployment that can use the CASS certified engine, given that data is stored at `/data/`:

``` bash
helm install -n loqate sa-us loqate/spatial-api --set imageCredentials.username=<DOCKERHUB USERNAME> --set imageCredentials.password=<DOCKERHUB PASSWORD> --set app.memberlistService=ml-memberlist.loqate.svc --set verify.dataset=us --set app.libraryPath="/lib64:/data/lib64"
```

## Usage

### Functions

The querycoordinator chart contains templates for a kubernetes ingress and an istio gateway but both are disabled by default. To test the installation, forward port 8900 of the querycoordinator service and use the following urls.

| Function | Method | URL |
| -------- | ------ | --- |
| Verify   | POST   | <http://localhost:8900/verify> |
| Version  | GET    | <http://localhost:8900/api/version> |
| GKR info | GET    | <http://localhost:8900/api/gkrinfo> |

### Verify Request

#### Request Parameters

| Name | Description |
| ---- | --- |
| input | An array of addresses that you want to verify. For optimal processing, the country field should contain a valid ISO2 or ISO3 country code.  Without a country code, requests will be routed to the `row` deployment, please see [How it Works](#how-it-works) and [Important Configuration Settings](#important-configuration-settings). |
| options | Used to specify various options and override processes. |

**Options** comprised of the following:

1. Geocode (boolean as string) - If you want geo-coordinates to be appended to your results (if available)
2. Processes (array of string) - Loqate Process to run. Valid Values: Verify, Search, ReverseGeocode.  Defaults to Verify when not supplied.
3. Certify (boolean as string) - Use certified dataset. AMAS (AU), CASS (US) or SERP (CA).
4. Enhance (boolean as string) - Use enhanced datasets. Returns enhance fields if applicable.
5. ServerOptions (object) - Properties are OptionName and value is OptionValue.

More information about [server options](https://support.loqate.com/server-options/).

#### Response Fields

| Name | Description |
| --- | --- |
| output | An array of matched records |

**Output** comprised of the following:

1. AVC – Address verification code
2. AQI – Address Quality Index
3. Address Elements – The cleansed address elements
4. Latitude/Longitude – The latitude and longitude of the address(if Geocode is used)

See this complete list of [field descriptions](https://support.loqate.com/documentation/fielddescrip/).

### Request and Response Format

#### Request - Geocode as true

``` json
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
```

#### Response - Geocode as true

``` json
{
    "output": [
        {
            "AQI": "A",
            "AVC": "V44-I55-P6-100",
            "Address": "Gb Group<BR>The Foundation<BR>Herons Way<BR>Chester Business Park<BR>Chester<BR>CH4 9GB",
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
            "DeliveryAddress": "The Foundation<BR>Herons Way<BR>Chester Business Park",
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
```

#### Request - Process option as ‘Verify’

``` json
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
```

#### Response - Process option as ‘Verify’

``` json
{
    "output": [
        {
            "AQI": "A",
            "AVC": "V44-I44-P7-100",
            "Address": "7511 Oakvale Ct<BR>North Highlands CA 95660-2733",
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
```

#### Request - Process option as ‘Search’

``` json
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
```

#### Response - Process option as ‘Search’

``` json
{
    "output": [
        [
            {
                "AQI": "A",
                "AVC": "A42-I44-P6-100",
                "Address": "1290 Mukawachoyanagisawa<BR>Hokuto-Shi Yamanashi 408-0307",
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
                "Address": "1490 Mukawachoyanagisawa<BR>Hokuto-Shi Yamanashi 408-0307",
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
                "Address": "1492 Mukawachoyanagisawa<BR>Hokuto-Shi Yamanashi 408-0307",
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
```

#### Request - Process option as ‘ReverseGeocode’

``` json
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
```

#### Response - Process option as ‘ReverseGeocode’

``` json
{
    "output": [
        [
            {
                "Address": "999 Baker Way<BR>San Mateo Ca 94404",
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
                "Address": "2210 Bridgepointe Pkwy<BR>San Mateo Ca 94404",
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
                "Address": "1661 Fashion Island Blvd<BR>San Mateo Ca 94404",
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
```

#### Request - Certify option - Certified data set AMAS (AU)

``` json
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
```

#### Response - Certify option - Certified data set AMAS (AU)

``` json
{
    "output": [
        {
            "AQI": "A",
            "AVC": "V44-I44-P3-100",
            "Address": "5 HIGHVIEW CRES<BR>MODANVILLE NSW 2480",
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
```

#### Request - Certify option - Certified data set CASS (US)

``` json
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
```

#### Response - Certify option - Certified data set CASS (US)

``` json
{
    "output": [
        {
            "AQI": "A",
            "AVC": "V55-I55-P7-100",
            "Address": "999 BAKER WAY STE 320<BR>SAN MATEO CA 94404-1566",
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
```

#### Request - Certify option - Certified data set SERP (CA)

``` json
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
```

#### Response - Certify option - Certified data set SERP (CA)

``` json
{
    "output": [
        {
            "AQI": "C",
            "AVC": "V55-I55-P6-091",
            "AddInfo": "",
            "Address": "8-8 CHARLOTTE ST<BR>TORONTO ON  M5V 0K4",
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
```

#### Request - Enhance option to return enhanced data set

``` json
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
```

#### Response - Enhance option to return enhanced data set

``` json
{
    "output": [
        {
            "AQI": "C",
            "AVC": "V42-I44-P3-092",
            "Address": "805 Veterans Blvd<BR>Redwood City CA 94063-1734",
            "Address1": "805 Veterans Blvd",
            "Address2": "Redwood City CA 94063-1734",
            "AdministrativeArea": "CA",
            "AdministrativeAreaISO2": "US-CA",
            "CAMEO_CAT": "9C",
            "CAMEO_GRP": "9",
            "CAMEO_INT": "53",
            "CBSAMetropolitanStatisticalArea": "San Francisco-Oakland-Berkeley, Ca=41860",
            "CensusClassCode": "C1",
            "CensusCode": "60102",
            "CensusIndicator": "Locality",
            "Country": "US",
            "CountryName": "United States",
            "DeliveryAddress": "805 Veterans Blvd",
            "DeliveryAddress1": "805 Veterans Blvd",
            "GNISFeatureID": "2410919",
            "HyphenClass": "C",
            "ISO3166-2": "US",
            "ISO3166-3": "USA",
            "ISO3166-N": "840",
            "Locality": "Redwood City",
            "MatchRuleLabel": "C2 1 1 1 1",
            "MetropolitanDivision": "San Francisco-San Mateo-Redwood City, Ca=41884",
            "PostalCode": "94063-1734",
            "PostalCodePrimary": "94063",
            "PostalCodeSecondary": "1734",
            "Premise": "805",
            "PremiseNumber": "805",
            "Sequence": "1",
            "SubAdministrativeArea": "San Mateo",
            "Thoroughfare": "Veterans Blvd",
            "ThoroughfareName": "Veterans",
            "ThoroughfareTrailingType": "St",
            "ThoroughfareType": "St",
            "TimeZone_DST": "-07:00",
            "TimeZone_Name": "Pacific Standard Time",
            "TimeZone_UTC": "-08:00"
        }
    ]
}
```

#### Request - Server Options

``` json
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
```

#### Response - Server Options

``` json
{
    "output": [
        {
            "AQI": "A",
            "AVC": "V44-I44-P6-100",
            "Address": "４０８－０３０７<BR>山梨県北杜市武川町柳澤１２９０",
            "Address1": "４０８－０３０７",
            "Address2": "山梨県北杜市武川町柳澤１２９０",
            "AddressFormat": "PostalCode<BR>AdministrativeAreaLocalityThoroughfarePremise",
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
```

## Trouble Shooting

### Re-run quick start without data download

To stop the data getting downloaded again open the helmfile.yaml and comment out:

1. The installmanager section.
1. Also in the querycoordinator and spatial-api section comment out the "- installmanager" line in the needs section as shown below:

``` yml
needs:
    #- installmanager
```

Delete the helmfile install

```helmfile delete```

Re-sync the helmfile installation

Unix:

``` bash
helmfile apply
```

Windows:  

``` powershell
helmfile sync
```

### Full system cleanup

#### Helmfile only cleanup commands

Perform the following:

``` bash
helmfile delete -n loqate
```

### Helmfile and Helm cleanup commands

#### Delete the namespace

``` bash
kubectl delete namespace loqate
```

#### Delete the persistent volumes

Get the persistent volumes:

``` bash
kubectl get pv
```

If there are any persistent volumes for installmanager or spatial-api. Delete them with:

``` bash
kubectl delete pv <NAME>
```

### Then Check that the system has cleaned up

``` bash
$ kubectl get pods
No resources found in loqate namespace.

$ kubectl get services -n loqate
No resources found in loqate namespace.

$ kubectl get deployments
No resources found in loqate namespace.

$ kubectl get pv
No resources found

$ kubectl get pvc
No resources found in loqate namespace.
```

## TERMS AND CONDITIONS FOR USE

### 1. DEFINITIONS

- **“Licence”** shall mean the Terms and Conditions for use of the Loqate NGV Software as defined by Clauses 1 – 4 (inclusive) below.
- **“Customer Entity”** means any Customer Group Company that receives or benefits from the Loqate NGV Software provided by GBG.
- **“Datasets”** means an individual data service included or delivered as part of the Software and/or Service and selected by the Customer and referenced on the Order Form. Where applicable, this may incorporate Supplier Data or Supplier Technology or utilise information derived from Supplier Data or Supplier Technology.
- **“Service”** means the service provided by GBG, including any and all Datasets, provided to the Customer in accordance with an agreement together with any other ancillary services provided by GBG to a customer pursuant to an agreement.
- **“Software”** means the GBG product or solution, including any and all Datasets, provided to the Customer in accordance with an Agreement together with any other ancillary services provided by GBG to the Customer pursuant to an agreement.
- **“You”** shall mean an individual or Customer Entity exercising permissions granted by this Licence.

### 2. LICENCE

2.1 Subject to the terms and conditions of this Licence, GBG hereby grants to You a perpetual, worldwide, non-exclusive, no-charge, royalty-free, revocable licence to use the Loqate NGV Software provided always that such use of the Loqate NGV Software is with a GBG Service and / or Software only.

2.2 By using the  Loqate NGV Software You agree that:

2.2.1 You will not use or exploit the Intellectual Property Rights in the Loqate NGV Software or permit others to use or exploit the Intellectual Property Rights in the Loqate NGV Software outside of the terms of the Licence;  

2.2.2 Your use of the Loqate NGV Software through any software, equipment, materials or services not provided by GBG will not infringe the rights of any third party.

### 3. DISCLAIMER OF WARRANTY

Unless required by applicable law or agreed to in writing, GBG provides the NGV Software on an "as is" basis. Any and all warranties, conditions and other terms relating to the NGV Software whether express or implied by law, custom or otherwise are, to the fullest extent permitted by law, excluded from the Licence. GBG shall not be responsible for the decisions that You make as a result of the information, NGV Software or data that GBG provides to the You. You are solely responsible for determining the appropriateness of using the Loqate NGV Software and assume any risk associated with Your exercise of permissions under this Licence.

### 4. LIMITATION OF LIABILITY

In no event shall GBG be liable to You , whether such liability arises in contract, tort (including, without limitation, negligence) misrepresentation or otherwise for damages, including any direct, indirect, special, incidental, or consequential damages whatsoever arising as a result of this Licence or out of the use or inability to use the Loqate NGV Software (including but not limited to damages for loss of goodwill, work stoppage, computer failure or malfunction, or any and all other commercial damages or losses), even if GBG has been advised of the possibility of such damages.

# Application Version Information 

SecretAppVersion: 1.16.0 


SpatialAPIAppVersion: 0.1.23512 

MemberlistAppVersion: 0.1.0 

QueryCoordinatorAppVersion: 0.1.23669 

InstallManagerAppVersion: 0.1.22799 

