## Horizontal Pod Autoscaler Monitoring

## Introduction
Currently the options to monitor Horizontal Pod Autoscaler(HPA) are limited to
logs, generic controller manager logs, HPA controller events and HPA status
conditions. None of this ways alone can be used as an indication of the overall
HPA health over time. This document describes a set of metrics (in Prometheus
format) that will be added to Horizontal Pod Autoscaler controller to allow
better monitoring of its health.

## Current state 

### Controller metrics

The Controller Manager exports general metrics on the controllers it runs, among
others on HPA Controller. These metrics give general idea on the controller 
work, they are stats on workqueue (length, latency) and request processing. 
These are exposed on kube-controller-manager's metrics endpoint.

| Metric Name | Metric type | Labels | Description |
| ----------- | ----------- | ------ | ----------- |
| horizontalpodautoscaler_adds | Counter |  | Total number of adds handled by workqueue: horizontalpodautoscaler |
| horizontalpodautoscaler_depth | Gauge |  | Current depth of workqueue: horizontalpodautoscaler |
| horizontalpodautoscaler_queue_latency | Summary |  | How long an item stays in workqueuehorizontalpodautoscaler before being requested. |
| horizontalpodautoscaler_retries | Counter |  | Total number of retries handled by workqueue: horizontalpodautoscaler |
| horizontalpodautoscaler_work_duration | Summary |  | How long processing an item from workqueuehorizontalpodautoscaler takes. |

### HPA Object Status

HPA objects contain in their status information about scaling. These are very
useful in debugging issues with single HPA configuration (i.e. HPA object 
selector is not pointing to the right Deployment or Deployment has reached the 
upper scaling limit) but can also signify general problems with the setup 
(can't fetch any meric from metric source). 

The status contains conditions that describe the current state of scaling. 
Design: https://git.k8s.io/community/contributors/design-proposals/autoscaling/hpa-status-conditions.md 

The status contains also following important information:
* Last scale time
* CurrentReplicas
* DesiredReplicas

