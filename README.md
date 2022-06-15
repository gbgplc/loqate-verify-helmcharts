# Loqate Verify

- [Loqate Verify](#loqate-verify)
  - [The Next Generation of Address Verification & Cleansing](#the-next-generation-of-address-verification--cleansing)
    - [A Quick Overview of Verify’s Capabilities](#a-quick-overview-of-verifys-capabilities)
    - [What Next Generation Verify offers you](#what-next-generation-verify-offers-you)
  - [Prerequisites](#prerequisites)
    - [Data Storage](#data-storage)
    - [Routing](#routing)
    - [Scaling](#scaling)
    - [Recommended](#recommended)
  - [Quick Start](#quick-start)
  - [How it Works](#how-it-works)
    - [Components](#components)
      - [InstallManager](#installmanager)
      - [Memberlist](#memberlist)
      - [Spatial-API](#spatial-api)
      - [QueryCoordinator](#querycoordinator)
  - [Individual Component Installation](#individual-component-installation)
    - [Add Repo](#add-repo)
    - [Install Data](#install-data)
    - [Install Charts](#install-charts)
    - [Uninstall Chart](#uninstall-chart)
    - [Upgrading Chart](#upgrading-chart)
    - [Adding the Loqate chart repo](#adding-the-loqate-chart-repo)
  - [Helmfile](#helmfile)
    - [Install](#install)
    - [Uninstall](#uninstall)
    - [Config Values](#config-values)
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

[Jump to quickstart Installation](#quick-start)

## The Next Generation of Address Verification & Cleansing

Combining the trusted power of Loqate’s leading Global Address Knowledge Base and simple to use APIs with modern web-scale architectural approaches; Next Gen Verify is the enterprise ready solution for global address parsing, standardising, cleansing, and enhancing.

_[Sign up](https://account.loqate.com/register/) for a free trial account!_

### A Quick Overview of Verify’s Capabilities

The Loqate solution will parse, standardise, verify, cleanse and format address data via a single, easy to integrate API for over 245 countries and territories. Our solution has a proven track record in providing customers with global data coupled powered by our superior parsing and matching engine.

- The Global Knowledge Repository (GKR) is Loqate’s proprietary main database which combines our Knowledge Base data and parsing rules with our location reference datasets for over 245 countries and territories.

- Loqate’s Global Parsing Engine consists of proprietary lexicon and context parsing rules created specifically for each country and territory around the world. The parsing engine standardises and cleanses addresses from unstructured and semi-structured data, automatically placing elements into the correct fields and eliminating erroneous non-address data.

- Our address cleansing engine is capable of processing millions of records per hour and beyond. You’ll see an indication of the validity of each address value included in the input address, reducing costly errors. This solution can validate both partial and full address inputs.

- Loqate’s solution transliterates words or letters from different global character sets into either native or Roman characters across 8 scripts including: Cyrillic, Hellenic, Hebrew, Kanji, Simplified Chinese, Arabic, Thai, Hangul.

_See [loqate.com](https://www.loqate.com/resources/support/cleanse-api/international-batch-cleanse/) for detailed API documentation._

### What Next Generation Verify offers you

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

## Quick Start

```bash
mkdir lqtcharts && cd lqtcharts/

wget https://charts.loqate.com/helmfile.yaml -O helmfile.yaml

export LICENSE_KEY="<API_KEY>"

helmfile apply
```

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

## Individual Component Installation

### Add Repo

``` bash
helm repo add loqate https://charts.loqate.com
```

_See [helm repo](https://helm.sh/docs/helm/helm_repo/) for command documentation._

### Install Data

``` bash
helm install --create-namespace -n verify im loqate/installmanager --set app.licenseKey=<LICENSE KEY> --set app.products=<PRODUCTS TO INSTALL>
```

Ensure the download is fully completed before continuing.

### Install Charts

``` bash
helm install -n verify ml loqate/memberlist
helm install -n verify sa loqate/spatial-api --set app.memberlistService=ml-memberlist.verify.svc
helm install -n verify qc loqate/querycoordinator --set app.memberlistService=ml-memberlist.verify.svc
```

The _memberlistService_ name is composed of `<MEMBERLIST.RELEASE_NAME>-<MEMBERLIST.CHART_NAME>.<NAMESPACE>.svc`, changing any of these will require changing the set arguments to _spatial-api_ and _querycoordinator_.

_See [helm install](https://helm.sh/docs/helm/helm_install/) for command documentation._

### Uninstall Chart

``` bash
helm uninstall -n verify <RELEASE_NAME>
```

This removes all the Kubernetes components associated with the chart and deletes the release.

``` bash
kubectl delete namespace verify
```

This deletes the kubernetes namespace that was created for the Helm release.

_See [helm uninstall](https://helm.sh/docs/helm/helm_uninstall/) for command documentation._

### Upgrading Chart

```bash
helm upgrade verify <CHART> --install
```

_See [helm upgrade](https://helm.sh/docs/helm/helm_upgrade/) for command documentation._

### Adding the Loqate chart repo

```bash
helm repo add loqate https://charts.loqate.com
```

## Helmfile

Helmfile can be used to easily install all components in a simple K8s environment. It will automatically pull from the Loqate charts repository.

helm diff is a plugin used by helmfile and will need to be installed to helm.
```helm plugin install https://github.com/databus23/helm-diff```

More information on helmfile as well as how to use it can be found here: <https://github.com/helmfile/helmfile>

An example helmfile can be downloaded from <https://charts.loqate.com/helmfile.yaml>

[Getting started with helmfile](#helmfile)

### Install

```helmfile apply```

### Uninstall

```helmfile delete```

### Config Values

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

## Usage

### Functions

| Function | URL |
| -------- | --- |
| Verify | <http://localhost:8900/verify> |
| Version | <http://localhost:8900/api/version> |
| GKR info | <http://localhost:8900/api/gkrinfo> |

### Verify Request

#### Request Parameters

| Name | Description |
| ---- | --- |
| input | An array of addresses that you want to verify. The country field must contain a valid ISO2 or ISO3 country code. |
| options | Used to specify various options and override processes.

**Option** comprised of the following:

1. Geocode (boolean as string) - If you want geo-coordinates to be appended to your results (if available)
2. Processes (array of string) - Loqate Process to run. Valid Values: Verify, Search, ReverseGeocode
3. Certify (boolean as string) - Use certified data set. AMAS (AU), CASS (US) or SERP (CA).
4. Enhance (boolean as string) - Use enhanced dataset. Returns enhance fields if applicable.
5. ServerOptions (object) - Properties are OptionName and value is OptionValue.

#### Response Fields

| Name | Description |
| --- | --- |
| output | An array of matches records |

**Output** comprised of the following:

1. AVC – Address verification code
2. AQI – Address Quality Index
3. Address Elements – The cleansed address elements
4. Latitude/Longitude – The latitude and longitude of the address(if Geocode is used)

### Request and Response Format

#### Request - Geocode as true

```json
{
   
   "Options": {
        "Geocode": "true"
    },
    "input": [
        {
            "Address1": "13 coppice place",
            "Address2": "winchester",
            "PostalCode": "RG24 7JU",
            "Country": "GB"
        }
    ]
}
```

#### Response - Geocode as true

```json
{
    "output": [
        {
            "AQI": "E",
            "AVC": "V44-I44-P6-060",
            "Address": "13 Belle Vue Road<BR>Old Basing<BR>Basingstoke<BR>RG24 7JU",
            "Address1": "13 Belle Vue Road",
            "Address2": "Old Basing",
            "Address3": "Basingstoke",
            "Address4": "RG24 7JU",
            "AdministrativeArea": "Hampshire",
            "Country": "GB",
            "CountryName": "United Kingdom",
            "DeliveryAddress": "13 Belle Vue Road<BR>Old Basing",
            "DeliveryAddress1": "13 Belle Vue Road",
            "DeliveryAddress2": "Old Basing",
            "DependentLocality": "Old Basing",
            "GeoAccuracy": "P4",
            "GeoDistance": "0.0",
            "HyphenClass": "B",
            "ISO3166-2": "GB",
            "ISO3166-3": "GBR",
            "ISO3166-N": "826",
            "Latitude": "51.269483",
            "Locality": "Basingstoke",
            "Longitude": "-1.040478",
            "MatchRuleLabel": "C1ap",
            "PostalCode": "RG24 7JU",
            "PostalCodePrimary": "RG24 7JU",
            "Premise": "13",
            "PremiseNumber": "13",
            "Sequence": "1",
            "Thoroughfare": "Belle Vue Road",
            "ThoroughfareName": "Coppice",
            "ThoroughfareTrailingType": "Place",
            "ThoroughfareType": "Place"
        }
    ]
}
```

#### Request - Process option as ‘Verify’

```json
{
    "options": {
        "Processes":["Verify"]
    },
    "input": [{
            "Address1": "7511 Oakvale Ct",
            "PostalCode": "95660",
            "Locality": "North Highlands",
            "AdministrativeArea": "CA",
            "Country": "US"
    }]
}

```

#### Response - Process option as ‘Verify’

```json
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

```json
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

```json
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

```json
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

```json
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

```json
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

```json
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

```json
{
    "options": {
        "certify": "true"
    },
    "input": [
      {             
       "Address": "999 Baker Way STE 320",
       "Locality": "San Mateo",
       "AdministrativeArea": "California",
       "PostalCode": "94404",
       "Country": "US"
      }
    ]
}
```

#### Response - Certify option - Certified data set CASS (US)

```json
{
    "output": [
        {
            "AQI": "C",
            "AVC": "P22-I22-P6-100",
            "Address": "SAN MATEO CA 94404",
            "Address1": "SAN MATEO CA 94404",
            "AdministrativeArea": "CA",
            "AutoZoneIndicator": "",
            "BusinessIndicator": " ",
            "CMRAIndicator": " ",
            "CarrierRoute": "",
            "CentralizedIndicator": "",
            "CheckDigit": "",
            "CongressionalDistrict": "",
            "Country": "US",
            "CountryName": "UNITED STATES",
            "CurbIndicator": "",
            "DPVConfirmedIndicator": " ",
            "DPVFootnotes": "A1M1",
            "DPVLACSIndicator": " ",
            "DefaultFlag": " ",
            "DeliveryPointBarCode": "",
            "DependentLocality": "",
            "DropCount": "",
            "DropSiteIndicator": " ",
            "EducationalIndicator": " ",
            "FIPSCountyCode": "",
            "FalsePositiveIndicator": " ",
            "Finance": "000000",
            "Footnotes": "F#",
            "ISO3166-2": "US",
            "ISO3166-3": "USA",
            "ISO3166-N": "840",
            "LACSLinkCode": "",
            "LACSLinkIndicator": " ",
            "Locality": "SAN MATEO",
            "NDCBUIndicator": "",
            "NoStatIndicator": " ",
            "Organization": "",
            "OtherIndicator": "",
            "PMBNumber": "",
            "PMBType": "",
            "PostalCode": "94404",
            "PostalCodePrimary": "94404",
            "PostalCodeSecondary": "",
            "PostalCodeSecondaryRangeHigh": "0000",
            "PostalCodeSecondaryRangeLow": "0000",
            "PrimaryAddressLine": "",
            "PrimaryNumRangeCode": " ",
            "PrimaryNumRangeHigh": "",
            "PrimaryNumRangeLow": "",
            "RecordType": " ",
            "ResidentialDelivery": " ",
            "ReturnCode": "21",
            "SUITELinkFootnote": "",
            "SeasonalIndicator": " ",
            "SecondaryAddressLine": "SAN MATEO",
            "SecondaryNumRangeCode": " ",
            "SecondaryNumRangeHigh": "",
            "SecondaryNumRangeLow": "",
            "Sequence": "1",
            "SubAdministrativeArea": "SAN MATEO",
            "ThrowbackIndicator": " ",
            "VacantIndicator": " ",
            "eLOTCode": "",
            "eLOTNumber": ""
        }
    ]
}
```

#### Request - Certify option - Certified data set SERP (CA)

```json
{
    "options": {
        "certify": "true"
    },
    "input": [
        {
            "Address1": "55 Adelaide St E",
            "Locality": "Toronto",
            "PostalCode": "M5C 1K6",
            "AdministrativeArea": "ON",
            "Country": "CA"
        }
    ]
}
```

#### Response - Certify option - Certified data set SERP (CA)

```json
{
    "options": {
        "certify": "true"
    },
    "input": [
        {
            "Address1": "55 Adelaide St E",
            "Locality": "Toronto",
            "PostalCode": "M5C 1K6",
            "AdministrativeArea": "ON",
            "Country": "CA"
        }
    ]
}
```

#### Request - Enhance option to return enhanced data set

```json
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

```json
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

```json
{
    "Options": {
        "ServerOptions": {
            "OutputAddressFormat": "true",
            "OutputScript": "Native"
        }
    },
    "input": [
        {
            "Address": "",
            "Address1": "ley 2732",
            "Country": "MX",
            "AdministrativeArea": "Madrid",
            "Locality": "guadalajara",
            "PostalCode": "44680"
        }
    ]
}
```

#### Response - Server Options

```json
{
    "output": [
        {
            "AQI": "E",
            "AVC": "P22-I24-P6-073",
            "Address": "Ley 2732<BR>Fracc Circunvalación Vallarta<BR>44680 Guadalajara JAL",
            "Address1": "Ley 2732",
            "Address2": "Fracc Circunvalación Vallarta",
            "Address3": "44680 Guadalajara JAL",
            "AddressFormat": "Thoroughfare Premise<BR>DependentLocality<BR>PostalCode Locality AdministrativeArea",
            "AdministrativeArea": "JAL",
            "Country": "MX",
            "CountryName": "Mexico",
            "DeliveryAddress": "Ley 2732<BR>Fracc Circunvalación Vallarta",
            "DeliveryAddress1": "Ley 2732",
            "DeliveryAddress2": "Fracc Circunvalación Vallarta",
            "DeliveryAddressFormat": "Thoroughfare Premise<BR>DependentLocality",
            "DependentLocality": "Fracc Circunvalación Vallarta",
            "HyphenClass": "A",
            "ISO3166-2": "MX",
            "ISO3166-3": "MEX",
            "ISO3166-N": "484",
            "Locality": "Guadalajara",
            "MatchRuleLabel": "C2",
            "PostalCode": "44680",
            "PostalCodePrimary": "44680",
            "Premise": "2732",
            "PremiseNumber": "2732",
            "Sequence": "1",
            "SubAdministrativeArea": "Guadalajara",
            "Thoroughfare": "Ley"
        }  
    ]
}
```

# Application Version Information 

SpatialAPIAppVersion: 0.1.10419 

MemberlistAppVersion: 0.1.0 

QueryCoordinatorAppVersion: 0.1.11845 

InstallManagerAppVersion: 0.1.11805 

