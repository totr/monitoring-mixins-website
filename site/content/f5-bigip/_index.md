---
title: f5-bigip
---

## Overview



{{< panel style="danger" >}}
Jsonnet source code is available at [github.com/grafana/jsonnet-libs](https://github.com/grafana/jsonnet-libs/tree/master/f5-bigip-mixin)
{{< /panel >}}

## Alerts

{{< panel style="warning" >}}
Complete list of pregenerated alerts is available [here](https://github.com/monitoring-mixins/website/blob/master/assets/f5-bigip/alerts.yaml).
{{< /panel >}}

### bigip-alerts

##### BigIPLowNodeAvailabilityStatus

{{< code lang="yaml" >}}
alert: BigIPLowNodeAvailabilityStatus
annotations:
  description: '{{ printf "%.0f" $value }} percent of available nodes, which is below
    the threshold of 95.'
  summary: Detecting a significant number of unavailable nodes which can causes potential
    downtime or degraded performance.
expr: |
  100 * (sum(bigip_node_status_availability_state) / clamp_min(count(bigip_node_status_availability_state), 1)) < 95
for: 5m
labels:
  severity: critical
{{< /code >}}
 
##### BigIPServerSideConnectionLimit

{{< code lang="yaml" >}}
alert: BigIPServerSideConnectionLimit
annotations:
  description: '{{ printf "%.0f" $value }} percent of the max number of connections
    in use on node {{$labels.node}}, which is above the threshold of 80 percent.'
  summary: Approaching the connection limit may lead to rejecting new connections,
    impacting availability.
expr: |
  max without(instance, job) (100 * bigip_node_serverside_cur_conns / clamp_min(bigip_node_serverside_max_conns, 1)) > 80
for: 5m
labels:
  severity: warning
{{< /code >}}
 
##### BigIPHighRequestRate

{{< code lang="yaml" >}}
alert: BigIPHighRequestRate
annotations:
  description: '{{ printf "%.0f" $value }} percent increase in requests on pool {{$labels.pool}},
    which is above the threshold of 150.'
  summary: An unexpected spike in requests might indicate an issue like a DDoS attack
    or unexpected high load.
expr: |
  max without(instance, job) (100 * rate(bigip_pool_tot_requests[10m]) / clamp_min(rate(bigip_pool_tot_requests[50m] offset 10m), 1)) > 150
for: 10m
labels:
  severity: warning
{{< /code >}}
 
##### BigIPHighConnectionQueueDepth

{{< code lang="yaml" >}}
alert: BigIPHighConnectionQueueDepth
annotations:
  description: '{{ printf "%.0f" $value }} percent increase in connection queue depth
    on node {{$labels.pool}}, which is above the threshold of 75.'
  summary: A sudden spike or sustained high queue depth may indicate a bottleneck
    in handling incoming connections.
expr: |
  max without(instance, job) (100 * rate(bigip_pool_connq_depth[5m])) / clamp_min(rate(bigip_pool_connq_depth[50m] offset 10m), 1) > 75
for: 5m
labels:
  severity: warning
{{< /code >}}
 
## Dashboards
Following dashboards are generated from mixins and hosted on github:


- [bigip-cluster-overview](https://github.com/monitoring-mixins/website/blob/master/assets/f5-bigip/dashboards/bigip-cluster-overview.json)
- [bigip-node-overview](https://github.com/monitoring-mixins/website/blob/master/assets/f5-bigip/dashboards/bigip-node-overview.json)
- [bigip-pool-overview](https://github.com/monitoring-mixins/website/blob/master/assets/f5-bigip/dashboards/bigip-pool-overview.json)
- [bigip-virtual-server-overview](https://github.com/monitoring-mixins/website/blob/master/assets/f5-bigip/dashboards/bigip-virtual-server-overview.json)