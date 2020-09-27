# Observability

Collection of Articles on Observability and my opinion.

I have started learning and implementing Observability after I changed my role as a full-time DevOps engineer. As there are 1000s of posts, articles, a whitepaper on this topic. I am trying to consolidate them here for later reference or a starting pointing for a noobie like me!

***Everything in ITALICS along with BOLD is my views which I believe either by reading a blog/watching a talk/or personal experience***

# Topics
 - [What is Observability?](#what-is-observability)
   - [Three Pillar of Observability](#three-pillar-of-observability)
   - [Is the three pillars enough?](#is-the-three-pillars-enough)
 - [Logs](#logs)
 - [Metrics](#metrics)
 - [PULL vs PUSH of Metrics](#pull-vs-push-of-metrics)
 - [PUSH for Events or Metrics](#push-for-events-or-metrics)
 - [What metrics to collect?](#what-metrics-to-collect)
 - [Logs vs Metrics](#logs-vs-metrics)
 - [BlackBox vs WhiteBox Monitoring](#blackbox-vs-whitebox-monitoring)
 - [References](#references)

# What is Observability?

Ability to fully understand your systems

**GOAL**: Gain visibility into the Application behavior

**A must watch if new to this topic**: [Keynote: ...What Does the Future Hold for Observability? - Tom Wilkie & Frederic Branczyk](https://www.youtube.com/watch?v=MkSdvPdS1oA)

## Three Pillar of Observability
 1. Logs
 2. Metrics
 3. Distributed Tracing

### Is the three pillars enough?
If I have all the 3 pillars in place, am I good? What do I do next?

**Correlation among the pillars!**

Observability Superpower: Correlation: https://www.openshift.com/blog/observability-superpower-correlation

***Without correlation among pillars of Observability, it doesn't make sense!***

## Logs
LIES MY PARENTS TOLD ME (ABOUT LOGS)
https://www.honeycomb.io/blog/lies-my-parents-told-me-about-logs/)
> Logs are expensive
>
> Too much operational cost

  Logs and Metrics
  https://medium.com/@copyconstruct/logs-and-metrics-6d34d3026e38)
> By far, the biggest drawbacks of logs is how operationally and monetarily expensive they are to process and store.
>
> There’s ELK in the open-source space, but no one I know likes or wants to operate ELK.

***Try to keep as much information in metrics instead of logs***

## Metrics
The start of Metrics with Events: **statsd**
https://codeascraft.com/2011/02/15/measure-anything-measure-everything/
>StatsD is a simple NodeJS daemon (and by “simple” I really mean simple — NodeJS makes event-based systems like this ridiculously easy to write) that listens for messages on a UDP port
>
> In general, we tend to measure at three levels: network, machine, and application. Application metrics are usually the hardest, yet most important, of the three.

***Collect only those metrics which is either graphed(over Grafana) or used in Alerting rules***

 ## PULL vs PUSH of Metrics
 https://prometheus.io/docs/introduction/faq/#why-do-you-pull-rather-than-push

> **Why do you pull rather than push?**
>
>Pulling over HTTP offers a number of advantages:
>-   You can run your monitoring on your laptop when developing changes.
>-   You can more easily tell if a target is down.
>-   You can manually go to a target and inspect its health with a web browser.
>
>Overall, we believe that pulling is slightly better than pushing, but it should not be considered a major point when considering a monitoring system.

Twitter changed PULL to PUSH
https://blog.twitter.com/engineering/en_us/a/2016/observability-at-twitter-technical-overview-part-ii.html

> **Pull” vs “push” in metrics collection:** At the time of our previous  [blog post](https://blog.twitter.com/2013/observability-at-twitter), all our metrics were collected by “pulling” from our collection agents. We discovered two main issues:
> - There is no easy way to differentiate service failures from collection agent failures. Service response time out and missed collection requests are both manifested as empty time series.
> - There is a lack of service quality insulation in our collection pipeline. It is very difficult to set an optimal collection time out for various services. A long collection time from one single service can cause a delay for other services that share the same collection agent.
>
>In light of these issues, we switched our collection model from “pull” to “push” and increased our service isolation. Our collection agent on each host only collects metrics from services running on that specific host. Additionally, each collection agent sends separate collection status tracking metrics in addition to the metrics emitted by the services.

***I personally feel PULL is better than PUSH with Service Discovery in place!***

##  PUSH for Events or Metrics
https://www.robustperception.io/which-kind-of-push-events-or-metrics

> The primary issue with pushing events is that the volume of data is proportional to the amount of processing your application is doing.
>
> If you have twice the requests, you're going to have twice the events to handle. As you're communicating over the network to the process of collecting these events (and often across machines), this can get problematic in terms of both CPU usage and network traffic.
>
>**UPD**: Pushing events is often done via UDP, which is unreliable and it is expected you'll lose a few packets.
>
>**TCP**: When there's too many events to process you must either have enough RAM to queue them up, or drop some events on the floor as UDP does. The data volume is the fundamental issue here, rather than the exact implementation.
>
>**Metrics by contrast have the same resource usage to push them no matter how busy your system is.**

 ## What metrics to collect?

 - **The USE method: How happy your servers are?**
 - **The RED method: How happy your customers are?**
 - **The four Golden Signals: RED + Saturation(How full your services are)?**

1. http://www.brendangregg.com/usemethod.html
2. https://grafana.com/blog/2018/08/02/the-red-method-how-to-instrument-your-services/
3. https://grafana.com/files/grafanacon_eu_2018/Tom_Wilkie_GrafanaCon_EU_2018.pdf
4. The RED Method: How To Instrument Your Services [B] - Tom Wilkie, Kausal: https://www.youtube.com/watch?v=TJLpYXbnfQ4

***I highly recommend watching the above-mentioned video***
## Logs vs Metrics
https://medium.com/@copyconstruct/logs-and-metrics-6d34d3026e38
>By and large, the biggest advantage of metrics based monitoring over logs is the fact that unlike log generation and storage, **metrics transfer and storage has a constant overhead**. Unlike logs, the cost of metrics doesn’t increase in lockstep with user traffic or any other system activity that could result in a sharp uptick in Observability data.
>
>What this means is that with metrics, an increase in traffic to an application will not incur a significant increase in disk utilization, processing complexity, speed of visualization, and operational costs the way logs do. Metrics _storage_ increases with the number of _time series_ being captured (when more hosts/containers are spun up, or when new services get added or when existing services are instrumented more), but unlike statsd clients that send a UDP packet _every time_ a metric is recorded to the statsd daemon (resulting in a _directly proportional_ increase in the number of metrics being submitted to statsd compared to the traffic being reported on!), client libraries of systems like Prometheus aggregate time-series samples _in-process_ and submit them to the Prometheus server upon a successful scrape (which happens once every few seconds and can be configured).
>
>Metrics, once collected, is also more malleable to mathematical and statistical transformations such as sampling, aggregation, summarization, and correlation, which make it better suited for monitoring and profiling purposes. Metrics are also better suited to trigger _alerts_, since running queries against an in-memory time-series database is far more efficient than running a query against a distributed system like ELK and then aggregating the results before deciding if an alert needs to be triggered. Of course, systems like Facebook’s Scuba strictly query only in-memory data, but the operational overhead of running a Scuba-esque system, even if it were open-source, isn’t something worth the trouble for most.

## BlackBox vs WhiteBox Monitoring
BlackBox tells what is wrong!

Whitebox monitoring tells why is it wrong


## References
- Monitoring and Observability(https://medium.com/@copyconstruct/monitoring-and-observability-8417d1952e1c)
-  Logs and Metrics(https://medium.com/@copyconstruct/logs-and-metrics-6d34d3026e38)
- Black Box vs. White Box Monitoring: What You Need To Know(https://devops.com/black-box-vs-white-box-monitoring-what-you-need-to-know/)
