# NGV Metrics

There are a lot of metrics that are automatically output by the prometheus package, and there are duplicate or deprecated
metrics too.  Those are not mentioned here.

## Query Coordinator

### Cluster Size (QC)

```text
# HELP ngv_qc_clustersize The size of memberlist cluster
# TYPE ngv_qc_clustersize gauge
ngv_qc_clustersize 2
```

This shows how many pods each Query Coordinator (QC) is connected to via memberlist,
see <https://pkg.go.dev/github.com/hashicorp/memberlist#Memberlist.NumMembers>

When deploying, not every QC will agree on this number as the pods 'gossip' to each other.  If everything is fine, they will
settle on a single number after a couple of minutes.  If they continue not to agree it could indicate a network or memberlist problem.

For more details see <https://github.com/hashicorp/memberlist?tab=readme-ov-file#protocol>

### Health Score (QC)

```text
# HELP ngv_qc_healthscore The 'health' of the node (lower better)
# TYPE ngv_qc_healthscore gauge
ngv_qc_healthscore 0
```

This is the health of the pod as determined by memberlist.  Lower is better, zero is totally healthy.
See <https://pkg.go.dev/github.com/hashicorp/memberlist#Memberlist.GetHealthScore>

### Address Count (QC)

```text
# HELP ngv_qc_address_count The number of address
# TYPE ngv_qc_address_count counter
ngv_qc_address_count{country="GB",error="false"} 1
ngv_qc_address_count{country="GB",error="true"} 0
```

This counts the number of addresses, not requests.

The country tag is set from the request. If the country could not be determined, the country will be "--".

The error tag shows if there was a problem verifying an address.

Each request can have multiple addresses inside and they don't need to be for the same country.

In order to take advantage of parallel processing, requests with multiple addresses may be split into multiple requests.
The request will be divided by country with a maximum of 50 addresses per sub-request.  The maximum can be configured with the
app.maxBatchSize value.

### Attempt Count (QC)

```text
# HELP ngv_qc_attempt_count Number of attempts to send a request to SpatialAPI
# TYPE ngv_qc_attempt_count counter
ngv_qc_attempt_count{country="GB",dataset="ROW",premium="false",success="true"} 1
```

This measures how many requests (not addresses) are sent from QC to SA.

The country tag is set from the request when possible, otherwise it will be "--".

The dataset and premium tags indicate which pod it tried to send the request to.

We use this to scale the number of SA, using KEDA.  It tends to be a better measure than request count.

### Request Counter (QC)

```text
# HELP request_counter Number of HTTP requests processed, by status code and method.
# TYPE request_counter counter
request_counter{code="200",method="GET",url="/"} 186
```

Standard request counter broken down by response code, method and url.

The methods and urls are:

- POST /verify
- GET /api/version
- GET /api/gkrinfo
- GET / (healthcheck)

### Request Duration (QC)

```text
# HELP request_duration_sec Request latencies in seconds.
# TYPE request_duration_sec histogram
request_duration_sec_bucket{code="200",method="GET",url="/",le="10"} 186
request_duration_sec_bucket{code="200",method="GET",url="/",le="20"} 186
request_duration_sec_bucket{code="200",method="GET",url="/",le="30"} 186
request_duration_sec_bucket{code="200",method="GET",url="/",le="+Inf"} 186
request_duration_sec_sum{code="200",method="GET",url="/"} 0.0016830209999999996
request_duration_sec_count{code="200",method="GET",url="/"} 186
```

Standard request duration histogram broken down by response code, method, and url.

The methods are the same as above.

## Spatial API

### Cluster Size (SA)

```text
# HELP ngv_clustersize The size of memberlist cluster
# TYPE ngv_clustersize gauge
ngv_clustersize 2
```

This shows how many pods each Spatial API (SA) is connected to via memberlist <https://github.com/hashicorp/memberlist>.

When deploying, not every SA will agree on this number as the pods 'gossip' to each other.  If everything is fine, they will
settle on a single number after a couple of minutes.  If they continue not to agree it could indicate a memberlist problem.

### Health Score (SA)

```text
# HELP ngv_healthscore The 'health' of the node (lower better)
# TYPE ngv_healthscore gauge
ngv_healthscore 0
```

This is the health of the pod as determined by memberlist.  Lower is better, zero is totally healthy.
See <https://pkg.go.dev/github.com/hashicorp/memberlist#Memberlist.GetHealthScore>

### Workers Used (SA)

```text
# HELP ngv_sa_workers_used The percentage of worker threads in use
# TYPE ngv_sa_workers_used gauge
ngv_sa_workers_used{country="row",premium="false"} 0
```

This metric is a percentage of the workers used.

Spatial API uses a pool of workers to service requests.  By default the number of workers is automatically
set by the software but this value (app.workerCount) can be overridden.

We use this to scale the number of SA, using KEDA.  We found it to be a better measure than CPU usage.

### Request Counter (SA)

```text
# HELP request_counter Number of HTTP requests processed, by status code and method.
# TYPE request_counter counter
request_counter{code="200",country_code="",method="GET",url="/"} 578
```

Standard request counter broken down by response code, method and url.

The methods and urls are:

- POST /address/verify
- GET /api/version
- GET /api/gkrinfo
- GET / (healthcheck)

### Request Duration (SA)

```text
# HELP request_duration_sec Request latencies in seconds.
# TYPE request_duration_sec histogram
request_duration_sec_bucket{code="200",country_code="",method="GET",url="/",le="10"} 578
request_duration_sec_bucket{code="200",country_code="",method="GET",url="/",le="20"} 578
request_duration_sec_bucket{code="200",country_code="",method="GET",url="/",le="30"} 578
request_duration_sec_bucket{code="200",country_code="",method="GET",url="/",le="+Inf"} 578
request_duration_sec_sum{code="200",country_code="",method="GET",url="/"} 31.46084387299999
request_duration_sec_count{code="200",country_code="",method="GET",url="/"} 578
```

Standard request duration histogram broken down by response code, method, and url.

The methods are the same as above.
