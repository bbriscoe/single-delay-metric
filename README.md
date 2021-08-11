### Discussion paper

# A Single Common Delay Metric for the Communications Industry

## Introduction

A real-time application invariably discards any packet that arrives after the deadline to play-out from its de-jitter buffer. This applies for any real-time application, whether streamed video, interactive media or online gaming. For this broad class of applications a median delay metric is a distraction---waiting for the median delay before play-out would discard half the packets. To characterize the delay experienced by an application it would be more useful to quote the delay of a high percentile of packets. But which percentile? The 99th? The 99.99th? The 98th? The answer is application- and implementation-dependent, because it depends on how much discard can effectively be concealed (1%, 0.01% or 2% in the above examples, assuming no other losses). Nonetheless, it would be useful to settle on a single industry-wide percentile to characterize delay, even if it isn't perfect in every case.

This brief discussion paper aims to start a debate on whether a percentile is the best single delay metric and, if so, which percentile the industry should converge on.

Note that the question addressed here is how to characterize a varying delay metric. That is orthogonal to the questions of what delay to measure and where to measure it. For instance, whether delay is measured in the application, at the transport layer or just at the bottleneck queue depends on the topic of interest and is an orthogonal question to the focus of this paper; how to characterize variability most succinctly and usefully in *any* of these cases.

## Don't we need two metrics?

In systems that aim for a certain delay, it has been common to quote mean delay and jitter. The distribution of delay is usually asymmetric, mostly clustered around the lower end, but with a long tail of higher delays. Unfortunately jitter is insensitive to the shape of this tail, because it is dominated by the *average* variability in the bulk of the traffic around the mean. However, it doesn't matter how little or how much variability there is in all the traffic that arrives before the play-out time. It only matters how much traffic arrives too late. The size of all the lower-than-average delay should not be allowed to counterbalance a long tail of above-average delay. 

The argument for a single percentile delay metric is strongest for real-time applications, including real-time media [[Bouch00](#Bouch00)], [[Yim11](#Yim11)] and online games. But a delay metric is also important for non-real-time applications, e.g. web and transactional traffic generally (e.g. RPC). Here, average delay is indeed important. But still, the user's perception is dominated by the small proportion of longer delays [[Wilson11](#Wilson11)].

Average packet delay would be the best metric if delay at the application level only depended on each packet in isolation. But it it rarely does. Typically interactions are dependent on each other [[Wischik08](#Wischik08)], so higher delay packets hold back progress of the whole flow of logic. This is clearly the case for isochronous media (real-time) and for ordered delivery. But, even where a connection consists of multiple streams or objects that have no explicit sequencing, the interdependencies will typically still be present in the application logic. For instance, a waterfall diagram shows how the loading of objects within a web page depends on others. Thus, a high percentile delay metric will invariably be more useful, or at least as useful, as mean or median delay. 

Arguments can be made for more than one delay metric to better characterize the delay distribution. But, if we can settle on a single metric, we should, for the sake of simplicity---simplicity for users and the regulators that represent them, and simplicity in measurement techniques and equipment. A single metric would not preclude anyone quoting other delay metrics, as long as they also quoted the one metric that everyone else quoted.

## Which percentile?

The factors that influence the choice of percentile are:

* The degree of late packet discard that can be efficiently concealed by real-time media coding (both that which is typical today and that which could be typical in future).
* The lag before results can be produced.
  For instance, to measure 99th percentile delay requires of the order of 1,000 packets minimum (an order of magnitude greater than 1/(1 - 0.99) = 100). In contrast 99.999th percentile requires 1,000,000 packets. At a packet rate of say 10k packet/s, they would take respectively 100 ms or 100 s.
  * The latter would be impractical to display live, while the former makes it possible to display the 99th percentile nearly immediately after a flow has started (see for instance the open source GUI we provide to display the distribution of delays for comparison between congestion controllers and AQMs [[Bond16](#Bond16)], [[l4sdemo](https://github.com/L4STeam/l4sdemo)]).
  * Similarly, for research or product testing where a large matrix of tests has to be conducted, it would be far more practical if each test run took 100 ms, rather than 100 s.
  * To calculate a high percentile requires a significant number of bins to hold the data. This can make a high percentile prohibitively expensive to maintain, e.g. on cost-reduced consumer-grade network equipment.
* {ToDo: others?}

As a strawman, we propose **the 99th percentile (P99)** as a lowest common denominator delay metric for the communications industry. We believe this is a workable compromise between the above somewhat conflicting requirements, but the purpose of proposing a specific number is to provoke debate, not close it off. 

## The 'Benchmark Effect'

As soon as a single metric is settled on, researchers, product engineers, etc. tend to shape their work around the benchmark. A percentile metric seems robust against such perverse incentives, because it seems hard to contrive performance results that fall off a cliff just beyond a certain percentile. Nonetheless, even if there were a benchmark effect, it would be harmless if the percentile chosen for the benchmark realistically reflected the needs of most applications.

{ToDo: Other arguments?}

## How to articulate a percentile to the public?

P99 is expressed as a delay or queuing delay, and therefore has a number of undesirable features for an industry-wide metric:

* Larger is not better.
  * It might be possible to invert the metric [[RPM21](#RPM21)], but rounds per minute carries an implication that it is only for repetitive tasks, which would limit the scope of the metric
* It is measured in time units (ms) that seem too small to matter, and which are not common currency for a lay person
  * This might also be addressed by inverting the metric
* It's not immediately obvious why the particular percentile has been chosen.
  * It would need some indication that it was an industry-standard metric, perhaps IETF-P99.
* A percentile is not the easiest of metrics to explain.
  * One has to say something like "the delay of 99% of packets was below x ms". 
  * Nonetheless, people are used to looking up the meaning of metrics on the web; 
  * And anyway people can still use a numeric metric for comparison without understanding its inherent meaning.

## How to reach consensus?

The IETF seems like an appropriate body to define such a metric, across countries, across standards bodies, and across industry sectors. IPPM would seem to be the appropriate WG.

Is there interest in taking this forward?

## References

<a name="Bouch00"></a>[Bouch00] Bouch, Anna & Sasse, M. Angela, "[The case for predictable network service](https://discovery.ucl.ac.uk/id/eprint/20139/)," In Proc. Multimedia Computing and Networking Conference (MMCN'2000), 188--195 (2000)

<a name="Bond16"></a>[Bond16] Bondarenko, Olga; De Schepper, Koen; Tsang, Ing-Jyh; Briscoe, Bob; Petlund, Andreas & Griwodz, Carsten, "[Ultra-Low Delay for All: Live Experience, Live Analysis](https://dl.acm.org/doi/10.1145/2910017.2910633)," In Proc. ACM Multimedia Systems; Demo Session, ACM, 33:1--4 (2016)

<a name="l4sdemo"></a>[l4sdemo] [Code repository](https://github.com/L4STeam/l4sdemo); [Video of GUI](https://riteproject.eu/dctth/#1511dispatchwg-gui

<a name="RPM21"></a>[RPM21] Stuart Cheshire & Vidhi Goel, "[Reduce network delays for your app](https://developer.apple.com/videos/play/wwdc2021/10239/)" Apple Worldwide Developer Conference'21 (Jun 2021)

<a name="Wilson11"></a>[Wilson11] Wilson, Christo; Ballani, Hitesh; Karagiannis, Thomas & Rowtron, Ant, "[Better Never than Late: Meeting Deadlines in Datacenter Networks](https://dl.acm.org/doi/10.1145/2018436.2018443)," Proc. ACM SIGCOMM'11, Computer Communication Review 41(4):50–-61 (Aug 2011)

<a name="Wischik08"></a>[Wischik08] Wischik, Damian "Short Messages," Philosophical Transactions of the Royal Society, 366(1872):1941--1953 (2008)

<a name="Yim11">[Yim11] Yim, Changhoon & Bovik, Alan C., "[Evaluation of Temporal Variation of Video Quality in Packet Loss Networks](https://doi.org/10.1016/j.image.2010.11.002)," Image Commun., Elsevier Science, 26(1):24-38, (Jan 2011).

