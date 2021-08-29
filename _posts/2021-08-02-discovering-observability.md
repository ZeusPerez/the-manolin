---
title: Discovering observability
published: true
---

Since I've started working as a developer, I've always had metrics in the systems I was operating, both system and business metrics. Every time a problem needed to be solved, there were dashboards to look at and try to find the cause. The dashboards were not able to tell me 100% and quickly what was the problem, but ...(it's ok), debugging has always been difficult and you have to research and find evidence or in many cases make a lot of assumptions that explained what happened. In most of the diagnoses of an issue, I had a theory that made sense rather than evidence.

About a year ago, I had the luck of joining a team that has, what I consider today, good practices in observability. I've moved from coarse-grain metrics to fine-grain ones (of course with overview/aggregated dashboards which are equivalent to the first one). Some of the main differences between these two ways of observability are:

### Black box vs both, black and white
With black box metrics, you know how your service is working, the response times, the load it handles, etc., but you will only see all the details if you add white box metrics. I think this could be a good simile: If you go to the doctor, he can give you a diagnosis by doing an exploration, but if you want to confirm that diagnosis and find out exactly what is happening to you, some extra information is needed, like blood tests or X-ray photograph, scans. Using white-box metrics you get this detailed information. With this detailed information, you will know where your service is taking more time, the internal errors, fence a problem, etc.

### On-demand or by default
If you are adding metrics only when you are missing something or when you need more information, you will be always behind in terms of visibility and monitoring. On the other hand, if you think about adding metrics as part of each task like developers do with testing for example, and you make an analysis about what to measure and how to visualize the new code you are adding when you are defining or developing, you will be able to have all the information you need beforehand and it could add another layer of validation showing that the changes you made are doing what is expected.

### Normalized metrics and dashboards
If your team or company has a style guide or the way of adding observability is very well defined, you will be able to find the information you need although you were not the one adding it or it belongs to another team service and vice-versa. Especially when you are in a hurry, an incident for example, but it is always helpful that you are easily able to find the right dashboard and before you open it you know what information will be inside that dashboard. This is very team-subjective, but this is an example of the information that a service dashboard should provide:
- Systems metrics: CPU,  memory, threads, objects, number of logs, etc.

- All the dependencies of the service with [RED metrics](https://www.weave.works/blog/the-red-method-key-metrics-for-microservices-architecture/): other services, third parties, DB, Caches, ...

- Black box metrics: incoming traffic with RED metrics by endpoint or action and aggregated

- Some business-related metrics. For example, if you are operating a payments service maybe it is useful to have the number for transactions by type and success.

Also, it could be a good idea to have a dashboard or section with your SLAs, SLOs and SLIs. This is a good way of sharing them with the rest of the company, letting them know what to expect when they consume one of the services that your team owns and you will easily be able to detect if any agreement is being broken.

Besides this service dashboard, it is always a good idea to have a dashboard with golden metrics close related to the nature of your business. If you have an e-commerce, for example, you will want to see some global metrics like sessions, users, orders, ships and so on.

I'm talking about the point of view of the software engineer. If you are a systems engineer or you work with the infrastructure, you'll probably need different metrics. Maybe [the USE method](https://www.brendangregg.com/usemethod.html) can be helpful if it is the case.

### Alerting
If you want to make fine-grain alerting, you will need fine-grain metrics. The observability is a requirement for the alerting (always talking about automatic alerting). If one of your third party providers is failing and there is nothing actionable you can do, you wouldn't want an alert that wakes you up in the middle of the night, right? In order to have this level of detail in the alerting and being able to exclude this kind of scenario, or include some others that are very specific, you will have very high control of your metrics that will require an elaborated observability system.

<br/><br/>
Having all this and being able to evolve your observability sounds great, but it is not easy and requires resources and discipline. There are some things that help to reach a satisfactory level of observability:

- **Have a proper observability system.** You need a system for exporting metrics and visualizing them and they have a cost, so you will need support from the company in order to be able to destinate resources for this. You can make it on-site, with open source tools, but you will need dedicated people or a team in order to build it and maintain it. The alternative is using some third-party tool that is able to provide you with all the features that you need.


- **Tools.** Adding metrics should be as easy as possible for the developers. With a very few lines (the fewer the better), someone should be able to add ad-hoc metrics so you will need some kind of tooling for this, which usually are in a library format. Also, having automatic metrics is a bonus here. Each time you add a server, some metrics by default could be added if you use the proper tooling. The RED metrics are the usual candidates for this. However, when adding a metric becomes difficult, laziness or hurries will appear and the developers would care less about this leading to a lack of observability.

- **Team awareness.** If the team agrees to have a guideline for this and everybody adds and maintains observability, this becomes very easy for the member of the team and new joiners. The inertia of the team drag for good and for bad (in this case for good). If you have a good observability culture, you will have the metrics in mind during the development and the reviews, and they will be, as I mentioned above, like having tests on your code.

This is a very personal opinion about my latest learnings in the observability world, but as it always happens in software, this could be improved a lot and it is very specific to your use case (it always depends). Hope these thoughts could be helpful to someone! 👋
