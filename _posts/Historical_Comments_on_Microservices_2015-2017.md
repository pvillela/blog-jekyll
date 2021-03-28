# Historical Comments on Microservices: 2015-2017

These are comments on microservices I made between 2015 and 2017.  Some of the content still holds, some of it I no longer agree with.

<!--more-->

### 2015-05-29 -- Advice on microservices

This article -- http://martinfowler.com/bliki/MicroservicePremium.html -- it provides sound advice on micro-services.  In particular, he says:

* *my primary guideline would be don't even consider microservices unless you have a system that's too complex to manage as a monolith. The majority of software systems should be built as a single monolithic application. Do pay attention to good modularity within that monolith, but don't try to separate it into separate services.*

I think this is precious advice and good food for thought.  The article links to another article -- http://paulhammant.com/2011/11/29/cookie-cutter-scaling/ -- on Cookie Cutter Scaling.  This is the scaling model used by large apps like Facebook.  We’re in good company :-).

### 2016-12-17 -- Size of microservices

I have found these articles interesting:

* https://dzone.com/articles/how-small-should-microservices-be?edition=255681&utm_source=Spotlight&utm_medium=email&utm_campaign=integration%202016-12-15
* https://dzone.com/articles/retrospective-on-microservice-boundaries?edition=255681&utm_source=Spotlight&utm_medium=email&utm_campaign=integration%202016-12-15 

The second article exemplifies some of the criteria of the first one.  But it also highlights another criterion which is not explicit in the first one -- temporal decoupling.  If two functions are executed over different time frames and/or are asynchronous with respect to each other then they may be candidates to live in separate deployment units.

### 2016-12-27 -- Excellent discussion of microservices adoption considerations

The book Spring Microsrvices, by Rajesh RV, has a great overview of microservices adoption considerations in chapter 3. Very complete. Even if I don't agree 100%, it is very good.

The book also includes a bunch of microservices KoolAid that I disagree with. See http://martinfowler.com/bliki/MicroservicePremium.html, http://martinfowler.com/articles/microservice-trade-offs.html. Scalability and performance tuning is one, well covered in the first Fowler artible. Another big fallacy is the notion of independent testing and deployment. When microservices depend on each other, they have to be tested together. Semantic changes (including certain bug fixes) in a microservice likely imply the need to also change and redeploy other microservices that depend on it. One more: the use of asynchronous processing, often conflated with microservices, is nothing new and is really independent of microservices.

### 2017-01-20: A blog on microservices

In my opinion, microservices are overhyped.  Many of the principles make sense, but people are embarking on microservices projects like lemmings.  The following articles provide good perspective: https://martinfowler.com/bliki/MicroservicePremium.html, https://martinfowler.com/articles/microservice-trade-offs.html.

Sam Newell's book (http://shop.oreilly.com/product/0636920033158.do) and YouTube presentations (e.g., https://www.youtube.com/watch?v=PFQnNFe27kU) are good sources of architectural advice on microservices, should you decide to go down that path.

Figuring out the right granularity for your microservices is a key challenge.  People will often end up with microservices that are too fine-grained.

Developing and deploying microservices involves many layers of architecture:

* Core application software (should be independent of whether you use microservices or not)
* Architecture software libraries and frameworks (mostly but not entirely independent of microservices)
* Middleware (certain varieties are preferred for microservices)
* Service discovery and load distribution (significantly impacted by decision to go with microservices)
* Monitoring / telemetry (significantly impacted by decision to go with microservices)
* Containers, container orchestration (independent of microservices decision, but number of containers greatly impacted)
* Capacity allocation (manual, autonomous elastic, etc.) (more moving parts with microservices)
* Infrastructure (IaaS, CaaS, FaaS, VMs, physical)

Different software vendors and open-source projects provide capabilities in one or more of these layers. 

There is no ONE software vendor that can give you all the layers (or even most of them) in a way that won't disappoint you and hold you hostage to their technology.  In the end, you need to have a skilled architect and team to make the right choices, compatible with your team's delivery and support abilities.  More often than not, people make choices that are beyond their ability to deliver.  And relying on a software vendor isn't the solution.  Working with a good SI is a reasonable option.  As you well know, finding one that won't disappoint you is another story :-).

### 2017-02-12 -- Concurrency control with microservices and event-sourcing

Concurrency control is a key issue that needs to be addressed when using event-sourcing:

* Optimistic locking so that end-users don't have commands executed based on stale information
* Serialization of commands so that an account with $100 can't process two debits of $75 each 

These are the basic requirements, which do not even include transactions spanning multiple aggregates if event-sourcing is partitioned by aggregate.  In fact, a good reason to partition microservices and event-sourcing by bounded context and not by aggregate is that you can provide transactionality across aggregates that way.

### 2017-03-25 -- Microservices fallacy

"The Microservices 2.0 Toolkit" book that I have recommended to some colleagues contains a very wrong statement which is commonly made in connection with microservices:

* *"Scaling monoliths often mean scaling the entire application thus producing very unbalanced utilization of resources. If we need more resources, we are forced to duplicate everything on a new server even if a bottleneck is one module. In that case, we often end up with a monolith replicated across multiple nodes with a load balancer on top. This setup is sub-optimum at best."*

In fact, the opposite is true.  By having identical software deployments, scaling is just a matter of adding identical servers/containers.  The utilization of hardware capacity is naturally optimized as the situation where one type of server is overloaded while others are under-utilized is avoided with a good load-balancing mechanism (assuming stateless services, no sticky connections).

The previous paragraph is true for request-response processing triggered by client calls when there is no fire-and-forget processing involved.  If there is fire-and-forget processing, then, instead of one monolith, the optimal deployment strategy for capacity utilization is to have two monoliths -- one for the synchronous (including non-blocking) processing of external client requests and one for the asynchronous fire-and-forget processing.  The fire-and-forget (event) processing is load-balanced using the competing consumer model.

There may be several good reasons to architect an application with microservices, but optimizing capacity utilization and facilitating performance tuning are not among them.

Furthermore, an easy way to make effective use of hardware capacity and optimize performance when deploying microservices is to mimic the cookie-cutter approach described below for monoliths, but with a microservices twist.  Define two kinds of machines: one where an instance of each microservice involved in synchronous (including non-blocking) processing of external client requests is deployed and another one where an instance of each asynchronous fire-and-forget (event-driven) microservice is deployed.  I'm assuming that each microservice instance is multi-theaded (e.g., written in Java, Scala, or Go, not Node.js) and CPU capacity is overcommitted across containers/microservices in each machine to avoid slack capacity.  The number of instances of each of the two kinds of machine needs to be determined through load analysis and testing, but there are only two kinds to worry about.

### 2017-04-03 -- Modular Development and Microservices

This docuument describes a DevOps approach to deal with custom shared modules/libraries and achieve some of the benefits that are sought with microservices.

**Goals that people are trying to achieve when thinking of microservices**

* Greater development agility
* Finer-grained deployments/changes to production
* They also think about things like easier, more independent testing, but that is typically an illusion (dependences among microservices can defeat that)
* They also think it is easier to scale, use capacity efficiently, and tune service performance -- this is a fallacy

**What people forget when thinking of adopting microservices**

* Greater operational complexity
* More complex to plan and use capacity efficiently, tune application
* Latency of remote calls among microservices that need to communicate synchronously

**To achieve low latencies, you may need to (at least in part) compromise on pure microservices and adopt a more coarse-grained deployment model, where modules communicate in-process**

**How to achieve the objectives below with a partially monolithic deployment model?**

* More agile development
* Finer-grained deployments/changes to production
* Fast communication among modules

**Break down your modules into units that correspond to what you would have if you were to do microservices**

* A service is a collection of functions or endpoints that are cohesive.  A service is not a single function or endpoint, normally.  Think of a service/microservice as a cohesive bundle of operations.
* Those would be the natural unit of development (project in source management).
* In JVM terms, each module would be packaged as a separate jar.  (The JVM is assumed for the remainder of this document, but the ideas are largely language-independent.)  Third-party shared libraries would be jars shared among modules.
* The challenge with typical monolithic development is that it is difficult to identify what has changed and the impact of the changes.  
* With microservices, you can build the microservice under consideration but you need interface/API definitions of the microservices it depends on in order to build it.  Similarly, with jar modules, there will be other modules that the module under consideration depends on, so the interface jars for the other modules would need to be available in order to build the jar for the module under consideration.  

**Build and deployment architecture**

* There are two kinds of modules: top-level modules (directly called by protocol adapters) and shared modules (called by other modules).  Top-level modules are equivalent to microservices.
* There should be one repo per module (same as for microservices)
* In the case of modules, the finest-grained unit of deployment is the top-level module (called by a protocol adapter).  That would make modules equivalent to microservices.  But top-level modules could be grouped into coarser-grained deployment units.
* An automated change impact analysis process is needed to re-generate the jars for all modules that directly or indirectly depend on a shared module.  As always, it is best to keep the (acyclic) dependence graphs as short as possible.
* In case deployment units consist of multiple modules, there needs to be a mapping of jars to deployment units and an automated process to package jars into deployment units.
* An automated process is needed to automatically deploy new containers/JVMs with updated jars and perform rolling restarts of the impacted JVMs (immutable deployments).
* Test cases should be segmented to ensure only use cases impacted by changed modules need to be tested/retested (same as for microservices)

**There is a spectrum between monolithic and microservices development and deployment**

* With the modular development approach outlined above, one could deploy each module independently as a separate microservice, or group all modules into a single deployment unit, or do something in-between.
* The choice of where to be in the spectrum between the two extremes should be informed by latency requirements, as well as tradeoffs involving runtime architecture, capacity management, monitoring, and DevOps complexity:

  | Consideration | Monolithic Modular  | In-Between  | Microservices |
  | ------------ | ------------ | ------------ | ------------ |
  | Latency  | probably better  | potentially better  | worse  |
  | Runtime architecture complexity  | better  | worse  | worse |
  | Capacity management complexity  | better  | potentially worse  | worse  |
  | Monitoring complexity  | better  | somewhat worse  | worse  |
  | DevOps complexity  | worse  | worse  | better  |

**Addendum: Batch reuse of online functions is a challenge**

* Need to address performance requirements.  Even if remote calling is acceptable, would probably need to have two deployments of the online functionality -- one for online access and one for batch access.
* Regardless of the model used, if changes are made to online functions used by batch then the online code will need to be deployed twice.
