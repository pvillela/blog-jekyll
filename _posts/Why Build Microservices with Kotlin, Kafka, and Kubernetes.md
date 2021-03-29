# Why Build Microservices with Kotlin, Kafka, and Kubernetes

Why I chose Kotlin, Kafka, and Kubernetes for a mission-critical, high-throughput, low-latency application.

<!--more-->

### Background

I was the chief architect and software engineering lead for a large system that went live recently.  This mission-critical system, built for a major transportation authority, has high throughput and stringent latency requirements.  The system was written in Kotlin, uses Kafka, and runs on Kubernetes (AKS) on Azure.

### Why did we choose Kotlin, Kafka, and Kubernetes?

That is a valid question and it has been asked multiple times.  We considered other stacks but landed on this one for the reasons outlined below.

It was given as a requirement/constraint that the system would be deployed on Azure.

### Stateless services

A key initial decision was whether to use stateless or stateful services.  Stateful services can be a good choice for systems that need to maintain sessions with clients, but this was not our case -- our clients had to maintain their state locally and only needed stateless services.  As stateless services are easier to build and operate on most platforms (the main exception being the Erlang/Elixir ecosystem), the decision was straightforward.

### Kafka

Kafka was an easy choice.  The architecture needed to be event-driven for performance, scalability, and resilience.  We also wanted to leverage an event log to enable independent specialized views of the data/state (CQRS and a data lake).  Kafka is the gold standard for event logs.

Our decision to use Azure Event Hubs with the Kafka API was based on ease of operation and support.  But it was key that the application services use the Kafka client API so that we could rely on the guarantees provided by Kafka and we could easily migrate to a different Kafka implementation -- there was no cloud provider lock-in for this aspect of the architecture.

### Kotlin <a name="kotlin"></a>

We could have built the system in C#, Java, TypeScript, or Go.  Key criteria were:

1. Expressiveness of the language.
2. Strong support for a function-oriented architecture.  (There are several important advantages associated with a function-oriented architecture, as discussed in another POST.)
3. Developer productivity.
4. Ease of learning for strong resources familiar with the JVM ecosystem.
5. High performance, with non-blocking processing and parallelism.
6. Fully-featured and efficient API to Kafka.

Kotlin meets all of these criteria.  None of the non-JVM languages fully meet criteria 4 and 6.  Otherwise, TypeScript and Go do fairly well against this list and could have been solid choices.  C# fails criterion 2, which was critical.  Java fails all criteria except for 4 and 6.

### Kubernetes

There was some discussion about whether to use containers or a FAAS (functions as a service).  There was also consideration of OpenShift vs. Kubernetes.  

The decision was based on the criteria below:

1. Need for control of the execution environment to minimize uncertainty in latency-critical processing.
2. Standards-based execution environment.
3. Ability to leverage a managed service.
4. Avoidance of cloud-provider lock-in.

Kubernetes on AKS meets all of these criteria.  OpenShift does not fully meet criterion 2.  Azure Functions does not meet any of the above criteria.

Although we did not use Azure Functions, I believe FAAS is the future and, eventually, it will be possible to substantially meet the above criteria with a FAAS offering.  It is important to note that the use of a function-oriented architecture (see [Kotlin](#kotlin) section above) makes it quite easy to migrate the services from Kubernetes to a FAAS in the future if warranted.
