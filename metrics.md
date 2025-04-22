# NGV Metrics

The prometheus package automatically outputs a range of useful metrics regarding the Query Coordinator and Spatial API. Here we will cover the main metrics (excluding any which are duplicate or deprecated), broken down by QC and SA.

Click the links below if you want to go straight to a particular metric.

- [NGV Metrics](#ngv-metrics)
  - [Query Coordinator](#query-coordinator)
    - [Cluster Size (QC)](#cluster-size-qc)
    - [Health Score (QC)](#health-score-qc)
    - [Address Count (QC)](#address-count-qc)
    - [Attempt Count (QC)](#attempt-count-qc)
    - [Request Counter (QC)](#request-counter-qc)
    - [Request Duration (QC)](#request-duration-qc)
  - [Spatial API](#spatial-api)
    - [Cluster Size (SA)](#cluster-size-sa)
    - [Health Score (SA)](#health-score-sa)
    - [Workers Used (SA)](#workers-used-sa)
    - [Request Counter (SA)](#request-counter-sa)
    - [Request Duration (SA)](#request-duration-sa)

## Query Coordinator

### Cluster Size (QC)

The number of pods each Query Coordinator (QC) is connected to via memberlist. See [this definition](https://pkg.go.dev/github.com/hashicorp/memberlist#Memberlist.NumMembers) for more details.

    # HELP ngv_qc_clustersize The size of memberlist cluster
    # TYPE ngv_qc_clustersize gauge
    ngv_qc_clustersize 2

When deploying, not every QC will agree on this number as the pods 'gossip' to each other. If everything is fine, they will
settle on a single number after a couple of minutes. If they continue not to agree it could indicate a network or memberlist problem.

For more details see <https://github.com/hashicorp/memberlist?tab=readme-ov-file#protocol>

### Health Score (QC)

The health of the pod as determined by memberlist - lower is better, zero is totally healthy. See [this definition](https://pkg.go.dev/github.com/hashicorp/memberlist#Memberlist.GetHealthScore) for more details.

    # HELP ngv_qc_healthscore The 'health' of the node (lower better)
    # TYPE ngv_qc_healthscore gauge
    ngv_qc_healthscore 0

### Address Count (QC)

A count of the number of addresses (not requests).

    # HELP ngv_qc_address_count The number of address
    # TYPE ngv_qc_address_count counter
    ngv_qc_address_count{country="GB",error="false"} 1
    ngv_qc_address_count{country="GB",error="true"} 0

The `country` tag is set from the request. If the country could not be determined, this will be "--".

The `error` tag shows if there was a problem verifying an address.

Each request can have multiple addresses inside, and they don't need to be for the same country.

In order to take advantage of parallel processing, requests with multiple addresses may be split into multiple requests.
The request will be divided by country with a maximum of 50 addresses per sub-request. The maximum can be configured with the
`app.maxBatchSize` value.

### Attempt Count (QC)

A measure of how many requests (not addresses) are sent from QC to SA.

    # HELP ngv_qc_attempt_count Number of attempts to send a request to SpatialAPI
    # TYPE ngv_qc_attempt_count counter
    ngv_qc_attempt_count{country="GB",dataset="ROW",premium="false",success="true"} 1

The `country` tag is set from the request when possible, otherwise it will be "--".

The `dataset` and `premium` tags indicate which pod it tried to send the request to.

We use this to scale the number of SA, using KEDA. It tends to be a better measure than request count.

### Request Counter (QC)

A standard request counter broken down by `response code`, `method`, and `url`.

    # HELP request_counter Number of HTTP requests processed, by status code and method.
    # TYPE request_counter counter
    request_counter{code="200",method="GET",url="/"} 186

The methods and urls are:

- POST /verify
- GET /api/version
- GET /api/gkrinfo
- GET / (healthcheck)

### Request Duration (QC)

A standard request duration histogram broken down by `response code`, `method`, and `url`.

    # HELP request_duration_sec Request latencies in seconds.
    # TYPE request_duration_sec histogram
    request_duration_sec_bucket{code="200",method="GET",url="/",le="10"} 186
    request_duration_sec_bucket{code="200",method="GET",url="/",le="20"} 186
    request_duration_sec_bucket{code="200",method="GET",url="/",le="30"} 186
    request_duration_sec_bucket{code="200",method="GET",url="/",le="+Inf"} 186
    request_duration_sec_sum{code="200",method="GET",url="/"} 0.0016830209999999996
    request_duration_sec_count{code="200",method="GET",url="/"} 186

The methods are the same as for Request Counter.

## Spatial API

### Cluster Size (SA)

The number of pods each Spatial API (SA) is connected to via memberlist. See [this definition](https://github.com/hashicorp/memberlist) for more details.

    # HELP ngv_clustersize The size of memberlist cluster
    # TYPE ngv_clustersize gauge
    ngv_clustersize 2

When deploying, not every SA will agree on this number as the pods 'gossip' to each other.  If everything is fine, they will
settle on a single number after a couple of minutes. If they continue not to agree it could indicate a memberlist problem.

### Health Score (SA)

The health of the pod as determined by memberlist.  Lower is better, zero is totally healthy. See [this definition](https://pkg.go.dev/github.com/hashicorp/memberlist#Memberlist.GetHealthScore) for more details.

    # HELP ngv_healthscore The 'health' of the node (lower better)
    # TYPE ngv_healthscore gauge
    ngv_healthscore 0

### Workers Used (SA)

A percentage of the workers used.

    # HELP ngv_sa_workers_used The percentage of worker threads in use
    # TYPE ngv_sa_workers_used gauge
    ngv_sa_workers_used{country="row",premium="false"} 0

Spatial API uses a pool of workers to service requests. By default the number of workers is automatically
set by the software, but this value (`app.workerCount`) can be overridden.

We use this to scale the number of SA, using KEDA. We found it to be a better measure than CPU usage.

### Request Counter (SA)

A standard request counter broken down by `response code`, `method` and `url`.

    # HELP request_counter Number of HTTP requests processed, by status code and method.
    # TYPE request_counter counter
    request_counter{code="200",country_code="",method="GET",url="/"} 578

The methods and urls are:

- POST /address/verify
- GET /api/version
- GET /api/gkrinfo
- GET / (healthcheck)

### Request Duration (SA)

A standard request duration histogram broken down by `response code`, `method`, and `url`.

    # HELP request_duration_sec Request latencies in seconds.
    # TYPE request_duration_sec histogram
    request_duration_sec_bucket{code="200",country_code="",method="GET",url="/",le="10"} 578
    request_duration_sec_bucket{code="200",country_code="",method="GET",url="/",le="20"} 578
    request_duration_sec_bucket{code="200",country_code="",method="GET",url="/",le="30"} 578
    request_duration_sec_bucket{code="200",country_code="",method="GET",url="/",le="+Inf"} 578
    request_duration_sec_sum{code="200",country_code="",method="GET",url="/"} 31.46084387299999
    request_duration_sec_count{code="200",country_code="",method="GET",url="/"} 578

The methods are the same as for Request Counter.
