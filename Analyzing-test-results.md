On completion, DiskSpd will provide a description of the test and a set of results. By default, this will be a human-readable text summary which can also be explicitly specified as `-Rtext`. An XML summary can be requested with `-Rxml`. The XML summary also contains a summary of the system configuration (processor topology, run start time, DiskSpd version) and the full configuration of the test in an XML format. The XML summary can then be used to convert command line parameters to their XML equivalents and subsequently passed to the `-X` parameter.

DiskSpd provides per-thread per-target statistics on data read and written by each thread in terms of total bytes, bandwidth and IOPs. In addition, any requested NT Kernel Logger events and a per-processor CPU utilization summary are also provided.

There are two more advanced sets of statistics which DiskSpd can optionally collect:
* latency, specified with the `-L` parameter
* IOPs statistics, specified with the `-D` parameter

## Latency
Latency is gathered in per-thread per-target histograms at high precision. In text output, this results in per-thread per-target per-operation-type average latency along with a summary table of per percentile latencies as in the following example:

|   %-ile |  Read (ms) | Write (ms) | Total (ms) |
|---------|------------|------------|------------|
|     min |      0.086 |      0.199 |      0.086 |
|    25th |      0.351 |      0.371 |      0.362 |
|    50th |      5.214 |      0.978 |      1.128 |
|    75th |      7.454 |      1.434 |      7.014 |
|    90th |      8.412 |      7.671 |      8.092 |
|    95th |      8.513 |      8.393 |      8.455 |
|    99th |     17.406 |      9.024 |     16.098 |
| 3-nines |     34.938 |     25.804 |     34.360 |
| 4-nines |     38.058 |     35.514 |     38.058 |
| 5-nines |     38.058 |     35.514 |     38.058 |
| 6-nines |     38.058 |     35.514 |     38.058 |
| 7-nines |     38.058 |     35.514 |     38.058 |
| 8-nines |     38.058 |     35.514 |     38.058 |
|     max |     38.058 |     35.514 |     38.058 |

NOTE: The 'nines' refer to the number of nines: 3-nines is the 99.9th percentile and so forth.

When designing tests to characterize the high percentiles it is important to consider the number of operations which each represent. The data which produced this sample consisted of 6049 I/Os of which a nearly equal number were reads (2994) and writes (3055). In the case of the 99.9th percentile for writes, only 3055 * (1 - 0.999) = ~3 write operations are between its 25.804ms and the maximum 35.514ms. As a result, the 99.99th (4-nines) percentile and onward are the same as the maximum since there is not enough data to differentiate them.

As a result, storage performance and test length need to be considered together in order to acquire enough data to accurately state the higher-nines percentiles.

Latency is gathered internally at a per-thread per-target and this full resolution is available in the XML output form (`-Rxml`). 

Given the per-thread per-target resolution, the memory overhead of these histograms can become significant with higher thread and target counts. The interplay of `-t` and `-F` is important to consider. In the following examples, both describe 16 threads:
* `-t1 <target1> … <target16>`
* `-F16 <target1> … <target16>`

However, the second results in 16 times as many histograms being tracked. This memory overhead can in extreme circumstances affect results.

## IOPs statistics
The higher-order IOPs statistics are based on capturing I/O counts per interval which by default is one second. A different interval can be specified in units of milliseconds: `-D<milliseconds>`

Similar to the way in which latency statistics are gathered, DiskSpd captures this data per-thread per-target. These carry relatively low cost and should not add substantial overhead except in cases of very long runtimes. In those cases, a proportionally longer sampling interval may be appropriate.

In text output, this adds the standard deviation of the IOPs per-thread per-target per-operation-type over the entire measured test. This provides some insight into how significant performance variation was interval-to-interval during the test. However, this statistic should be used with care since it cannot describe the outliers or their frequency.

When using XML output (`-Rxml`), the full per-thread per-target per-operation-type vectors are included in the results. In addition to allowing the standard deviation to be put in context, in many cases these may eliminate the need for external measurements of I/O rates in generating visualizations.
