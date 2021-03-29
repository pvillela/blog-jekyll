---
title:  "Containers, Kubernetes, and Service Mesh in a Nutshell"
author: PV
date:   2021-03-28 13:56:26 -0400
categories: architecture software-engineering programming
---

This is a very brief distillation of the concepts around containers, Kubernetes, and service mesh.  The reader is assumed to have passing familiarity with these three terms.

<!--more-->

### Containers

Containers are a virtualization technology that enables processes to run in isolation from each other on the same host.  Containers are lighter-weight than virtual machines and enable more efficient utilization of computing capacity.  All containers running on a host share the host's operating system kernel, but each container chooses its own individual libraries and respective versions (which need to be compatible with the kernel version).  Container hosts are usually (but not always) virtual machines.

There are several concepts associated with containers that need to be understood.

***Container image*** is a binary file that contains the code (as a filesystem bundle, analogous to a binary tar file) that runs in a container.  There are different container image formats, most notably the Docker format which was adopted by the [Open Container Initiative (OCI)](https://opencontainers.org/about/overview/) as its standard format.

***Container instance*** is a process that runs a container image in isolation from other processes.

***Container runtime*** is the process that launches container instances and controls their lifecycle.  A container runtime has an API to support the management of container instances and images.  Examples include [containerd](https://containerd.io/) and [Docker Engine](https://www.docker.com/products/container-runtime).  Docker Engine actually wraps containerd and exposes the Docker Engine API to Docker clients.  There are other container runtimes, like [CRI-O](https://cri-o.io/).

***Container runtime client*** is a command line interface or other kind of application that interacts with the container runtime to manage containers, images, and their lifecycles.  An example is the Docker command line interface (included in Docker Engine).

***Image creation and management tools*** support the creation of container images.  Docker's [Dockerfile](https://docs.docker.com/engine/reference/builder/) and associated tools is an example, but there are others like [BuildKit](https://docs.docker.com/develop/develop-images/build_enhancements/), [Buildah](https://buildah.io/), and [Kaniko](https://github.com/GoogleContainerTools/kaniko).

***Image registry*** is a repository that stores container images with versioning.  [Docker Hub](https://docs.docker.com/docker-hub/) is a well-known public registry.  Enterprises maintain their private image registries using [Docker Registry Server](https://docs.docker.com/registry/deploying/), [JFrog](https://jfrog.com/container-registry/), [Nexus](https://help.sonatype.com/repomanager3/formats/docker-registry), [Harbour](https://goharbor.io/), products from cloud providers, and others.

### Kubernetes

[Kubernetes](https://kubernetes.io/) (abbreviated as K8s) is a container orchestration engine.  K8s is the *de facto* standard for container orchestration,  Based on the [Borg](https://kubernetes.io/blog/2015/04/borg-predecessor-to-kubernetes/) technology donated by Google, K8s is developed and maintained by the [Cloud Native Computing Foundation (CNCF)](https://www.cncf.io/), which is part of the [Linux Foundation](https://www.linuxfoundation.org/).

***Container orchestration*** is defined as the capability to define, deploy, and operate a compute cluster consisting of multiple virtual machines or physical servers to launch containers and manage their lifecycle.

It is important to note that the execution unit orchestrated by Kubernetes is a *pod*, not a container.  A ***pod*** consists of one or more containers that are deployed and managed as a unit by K8s.  Often, a pod has a single container.  In some cases, however, there are containers that closely depend on each other and should be part of the same pod.  This is the case when using a service mesh.  

Just as a container is the runtime manifestation of a container image, a pod is the runtime manifestation of a *pod spec*.  A ***pod spec*** defines, among other things, the image(s) for the container(s) in the pod.

Kubernetes's capabilities include:
* Providing the abstractions of *deployment* and *service* to facilitate the launching and management of multiple instances of the same pod.
* Managing the number of instances for each pod.
* Constraining the computation resources allocated to each pod.
* Allocating work (load balancing) across pods in the same service.
* Automatically restarting pods instances when they crash.
* Providing configuration facilities for pods.
* Providing data persistence facilities for pods.
* Providing the *namespace* abstraction to group related pods (e.g., part of the same application) and isolate them from pods in other namespaces.

### Service Mesh

Service mesh is a technology that augments service orchestration by providing the ability to monitor and mediate interactions between pods.  This can be accomplished by adding so-called *sidecar* containers to pods.  A ***sidecar*** is a container that intercepts traffic to and from the main container in the pod.  

Through sidecars and virtual networking, the service mesh can add capabilities such as:
* Monitoring of interactions among services.
* Tracing of interactions among services.
* Service decorators such as timeout, retry, and circuit-breaker.
* Authentication and encryption of service interactions with [mTLS](https://wott.io/blog/tutorials/2019/09/09/what-is-mtls).
* Routing rules.

In addition to providing system administration and operations benefits, service meshes also help to simplify application code by externalizing technical concerns. 

Service mesh architectures are typically described in terms of a *data plane* and a *control/management plane*.  The ***data plane*** is responsible for intercepting the data traffic to/from containers and acting upon such traffic (sending monitoring/trace messages, encrypting, routing, etc.).  The ***control/management plane*** is responsible for supporting the system administrator by providing configuration facilities and collecting/displaying system state and management information sent by the data plane.

[Istio](https://istio.io/) and [Linkerd](https://linkerd.io/) are popular service mesh implementations.  [Open Service Mesh](https://openservicemesh.io/) is an up-and-coming CNCF sandbox project.

### References and further reading

* [Cloud Native Computing Foundation (CNCF)](https://www.cncf.io/)
* [A Practical Introduction to Container Terminology](https://developers.redhat.com/blog/2018/02/22/container-terminology-practical-introduction/)
* [Open Container Initiative](https://opencontainers.org/about/overview/)
* [Docker vs CRI-O vs Containerd](https://computingforgeeks.com/docker-vs-cri-o-vs-containerd/)
* [Docker and OCI Runtimes](https://medium.com/@avijitsarkar123/docker-and-oci-runtimes-a9c23a5646d6)
* [A Comprehensive Container Runtime Comparison](https://www.capitalone.com/tech/cloud/container-runtime/)
* [Kubernetes](https://kubernetes.io/)
* [Kubernetes container runtimes](https://kubernetes.io/docs/setup/production-environment/container-runtimes/)
* [Istio](https://istio.io/)
* [Linkerd](https://linkerd.io/)
* [Open Service Mesh](https://openservicemesh.io/)
* [Service Mesh Comparison](https://servicemesh.es/)
* [The Enterprise Path to Service Mesh Architectures, 2nd Edition](https://learning.oreilly.com/library/view/the-enterprise-path/9781492089353/)

