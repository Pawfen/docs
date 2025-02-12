---
editable: false

__system: {"dislikeVariants":["No answer to my question","Recomendations didn't help","The content doesn't match title","Other"]}
---

# Pricing for {{ ml-platform-name }}

## What goes into the cost of using {{ ml-platform-name }} {#rules}

When using the {{ ml-platform-name}} service, you pay for the actual use of computing resources: the calculation time is charged per second.

If you do not perform computational operations in the project, the service usage time {{ml-platform-name }} is not charged. However, if you perform calculations using {{ dataproc-name}} clusters, they are charged separately. For more information, see [using clusters {{dataproc-name }}](#data-proc).

### Pricing unit {#unit}

A billing measure is a one billing unit. The number of billing units spent on calculation depends on:
* capacity of computing resources used;
* time spent on the calculation.
    
    The calculation time is rounded up to an integer number of seconds.

The cost of one billing unit is the cost of using 1 CPU core for 1 second. The number of units depends on [computing resource configuration](concepts/configurations.md).

Configuration | Units per second
 ----- | ---- 
  c1.4 | 4 
  c1.8 | 8 
  c1.80 | 80 
  g1.1 | 72 
  g1.4 | 288 


### Example of cost calculation {#price-example}

Example of cost calculation:
- **Computing resource:** configuration c1.8 with 8 CPUs and 1 GPU.
- **Operation execution time:** 1,400 MS (rounded up to an integer number of seconds).

Cost calculation:

> 72 × 2 = 144 units per calculation
> 144 × 0.000009 = $0.001296
>
> Total: $0.001296

Where:
* 72 — number of units per configuration g1.1.
* 2 — rounded up 1,400 MS.
* $0.000009 — cost of 1 unit.

### Using {{ dataproc-name }} clusters {#data-proc}

The cost of using integration with the {{ dataproc-name}} service takes into account:
* Configuration c1.4 computing resources {{ml-platform-name }}.

    These resources are created for integration with the {{ dataproc-name}} cluster and are charged while the cluster is being calculated.
* The entire lifetime of the cluster {{ dataproc-name }} according to [pricing rules {{ dataproc-full-name }}](../data-proc/pricing.md).

Learn more about [integration with {{ dataproc-name }}](concepts/data-proc.md).

### Storing data in the project {#storage}

Data storage inside {{ ml-platform-name }} is not charged within the established [quotas and limits](concepts/limits.md).
If you need to store large amounts of data that exceed the specified limits, use the {{ objstorage-full-name }} service. In this case, data storage will be charged according to [pricing rules {{ objstorage-name }}](../storage/pricing.md).

## Pricing {#prices}




{% include [usd-unit-and-resource.md](../_pricing/datasphere/usd-unit-and-resource.md) %}

### Performing background operations {#async}

More about [background operations](concepts/async.md).



### Outgoing traffic {#prices-traffic}




{% include notitle [usd-egress-traffic.md](../_pricing/usd-egress-traffic.md) %}