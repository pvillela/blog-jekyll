Function-Oriented Architecture
==============================

This report collects my thoughts on function-oriented architecture, a concept that has been shaped primarily by two very large and successful systems I architected over the past six years.  This report is a work-in-progress that evolves as I write down my ideas and experiences on this topic, and as I continue to get feedback from other software architects and developers, especially those who worked with me on the aforementioned projects.




## Motivation and requirements

This report describes a way to organize the internal structure of service executables to meet these key objectives:

* Easy to learn and adopt for the design and development of business services/applications.
* Promote modularity, high module cohesiveness, and low module coupling -- key characteristics of well-architected systems.
* Facilitate the design of services with non-blocking and parallel processing.
* No *"magic"* and no dependence on complex runtime architectures or frameworks.

We will see that this architecture approach yields the following additional benefits:

* Simplifies the transition and mapping from business problem to code solution.  Uses a simple vocabulary to express use case realisation and service design in a way that makes sense to business analysts (BAs), developers, and architects.
* Provides a sound and clear basis for effort estimates.
* Provides a natural structure for code.
* Enhances development productivity and maintainability.
* Enhances software quality and testability.
* Facilitates the assignment of work to developers and their ability to work concurrently.
* Is programming language agnostic.



## Scope

This report is about the layering and modularization of services, whether microservices or otherwise.

Generally speaking, a service executable runs in a context such as: 

![BROKEN_LINK](/wp-content/uploads/2021/FOA_Images/Service_Context.png)

This is just an illustrative possibility.  For purposes of this discussion, it doesn't matter how the service is called -- only that the service is called somehow and that it calls other "*things*" in its environment such as other services, databases, event hubs, etc.

For our purposes, it also doesn't matter whether the service executes in a container, as a function on the cloud (FAAS -- functions as a service), or on bare metal.

This report focuses only on the internal software structure of a service executable.  The internal structure proposed in this report applies equally well in all of the above cases.




## Background and approach

This report describes a way to design and structure applications that is inspired by *functional decomposition*.

[Functional decomposition](https://en.wikipedia.org/wiki/Functional_decomposition) is a technique or approach that has long been used in systems engineering and business process analysis.  It was widely used in software engineering before the advent of object-orientation but it was condemned by object-oriented purists and became unpopular.

Interestingly, in the past few years, the object-oriented programming paradigm has been increasingly challenged by the functional programming paradigm.  This challenge has taken the form of the rising popularity of languages that support the functional paradigm, the steady incorporation of functional programming features in traditional object-oriented languages, the advent of Functions As a Service (FAAS) from cloud providers, and the wide adoption of service-oriented architectures.

It is quite plain that the essence of a service is really just a function that transforms requests into responses and (usually) produces some side-effects (e.g., updates a database) as a result of its execution.  So, it is not surprising that a function-oriented way of structuring software can actually be simpler and more effective than trying to cling to the precepts espoused by object-oriented purists.

Although the architecture approach described here defines a software structure based on functions, it is **not** dependent on functional programming.  While the software modules are functions, each module can be implemented using imperative and/or object-oriented and/or functional programming idioms, as may be most suitable with the chosen programming language and the skills of the development team.  To be clear, there is no need for developers to learn/know functional programming to apply this architecture approach.

This function-oriented architecture approach has been used effectively for the delivery of multiple large-scale, high-performance systems, using different programming languages.

It is worth noting that the kinds of modular decomposition discussed here are important for large applications/services involving complex business functionality.  In the case of simple services with just basic CRUD (create, read, update, delete) functionality, the decision of how the service is decomposed into modules (if it is decomposed at all) is not terribly consequential.



## General architecture principles

We will recap a key architecture principle that will direct and inform our approach, and mention some additional related principles.

### Modularity with high module cohesion and low module coupling

In order for a large system to be constructed effectively and efficiently, it should be structured with the following goals and characteristics:

1. It is essential that the system structure help make the system understandable.  The harder it is to understand a system, the harder it is to construct and maintain it.
2. To help make the system understandable, it should to be decomposed into parts, sub-parts, sub-sub-parts, etc.  This kind of hierarchical decomposition is inherent in how human beings tame complexity and solve problems.  Functional decomposition, mentioned earlier, is a way to approach this.
3. For the sake of understandability, it should be possible to understand portions of the system without having to understand everything at once.  Let's loosely use the term *module* to refer to a part or sub-part of the system.  Thus, one should be able to reason about a module in isolation or just have to consider a few other modules that it directly relates to or interacts with.
4. As the system is designed and construction progresses, understanding of requirements evolves and changes may be required.  Also, inevitably, changes to the system will be required after it is constructed, to address changing business needs and remaining defects.  Therefore, relatively small and localised changes to a module should not have a ripple effect and  impact several other modules.  

In particular, to meet objectives 3 and 4 above, a system should be made up of modules that individually have high cohesion and that have low coupling among each other.  [Cohesion](https://en.wikipedia.org/wiki/Cohesion_(computer_science)) means that the parts/elements of the module have a clear common purpose and hang together, rather than being a loose collection of capabilities.  [Coupling](https://en.wikipedia.org/wiki/Coupling_(computer_programming)) between modules means that changes to one impacts the other.

These are the most important goals and characteristics for the structure of a system.

### Other related architecture principles

There are many additional architecture principles that can be useful in guiding how a system is architected.  While a broader discussion of architecture principles is beyond the scope of this report, a few related ones will be briefly mentioned:

* [Single responsibility principle](https://en.wikipedia.org/wiki/Single-responsibility_principle) or [separation of concerns](https://en.wikipedia.org/wiki/Separation_of_concerns) -- These are closely related to the principle of high module cohesion.  They basically say that a module should be responsible for doing only one thing.  For example, do not mix business functionality and database I/O in one module and do not mix two unrelated business functions in one module.
* SOLID -- This stands for **S**ingle Responsibility Principle, **O**pen/Closed Principle, **L**iskov Substitution Principle, **I**nterface Segregation Principle, **D**ependency Inversion.  This extends the single responsibility principle with other important principles for object-oriented architecture and design.  See [SOLID Design Principles Explained](https://stackify.com/solid-design-principles/), [SOLID Principles of Object Oriented and Agile Design](https://www.youtube.com/watch?v=TMuno5RZNeE), [Solid Relevance](https://blog.cleancoder.com/uncle-bob/2020/10/18/Solid-Relevance.html).
*   [The Twelve-Factor App](https://12factor.net/) model -- 

### Applicability

Adherence to the principles of high module cohesion and low module coupling is time-proven to promote development productivity, understandability, and maintainability for large, complex systems.  

There is, however, some overhead associated with applying these principles rigorously in all cases.  For a simple, CRUD-based application, it may be more productive to design and implement a solution whose modules are highly coupled and uncohesive, yet the solution could still be understandable and maintainable due to its simplicity.  This exception only applies to truly simple applications.  For systems with any appreciable degree of complexity, adherence to the principle of high module cohesion and low module coupling is a key for success.



## Relationship to object-oriented software structure

Object-orientation is a powerful programming paradigm and is great for structuring certain kinds of technical software, like graphical user interfaces and architecture frameworks.  However, its effectiveness as an approach for business software architecture is debatable.  Regardless, object-oriented programming is deeply entrenched in contemporary IT and most popular programming languages support the object-oriented paradigm in some shape or form.

Without delving deeper into the debate about the effectiveness of object-orientation for business software architecture, suffice it to mention that the Spring framework, arguably the most popular software framework for Java (which is object-oriented and one of the most popular enterprise programming languages), promotes a non-object-oriented way to structure code.  Roughly speaking, with Spring, a web application is structured as a set of domain classes, controllers, business services, and repository (data access) classes.  Other than the domain classes, these classes are instantiated as singletons that are, essentially, bundles of functions.  The state in the singleton objects is just used to configure and wire the functions of interest.  The domain classes often have little functionality and are little more than glorified data structures, with the majority of the business logic residing in the business service classes.

Hard-core object-oriented proponents argue that domain objects should be *rich* in functionality (as opposed to *anemic*) but, in practice, anemic objects are often used in Spring-based applications because many people find it simpler and cleaner that way.

The function-oriented architecture approach described in this report goes beyond what was described in the preceding paragraphs and uses anemic domain objects (or structs) and individual functions rather than classes that represent bundles of functions.  As will be shown, it lays out components that more naturally align with the functional requirements and design, and avoids the obfuscation arising from objects that represent bundles of functions.




## Relationship to layered service architectures

There are different ways in which service-oriented application architectures are organised and described.  

When architecting services (micro or otherwise), a three-layer approach is often advocated (as in the previous section):
1.  Controller layer
2.  Business logic layer
3.  Data access layer

![BROKEN_LINK](/wp-content/uploads/2021/FOA_Images/Layered_Architecture.png)

With this layering, the flow of control is:
1. Client calls controller
2. Controller calls business layer
3. Business layer calls data access layer

This layering helps to separate concerns, but layered software can have undesirable compile-time dependencies, e.g. the business logic can become dependent on data access logic.  In languages like Java or C#, it is standard practice to avoid such compile time dependencies with the use of interfaces and dependency injection (a.k.a. dependency inversion or inversion of control), possibly using a framework like Spring with Java.

While the use of interfaces and dependency inversion can prevent undesirable compile-time dependencies, interfaces can still introduce a semantic dependence.  If a business logic class has a dependence on a data access interface, the business logic doesn't depend on the *implementation* of the data access logic but it still has knowledge of and a dependence on the *name* of the interface and the names of the abstract data access operations insofar as there are calls to such operations embedded in the business logic.

As will be shown later, the function-oriented architecture not only avoids undesirable compile-time dependencies but it also minimizes semantic dependencies.




## Relationship to *Clean Architecture*

The *Clean Architecture*, articulated by Robert C. Martin in his [blog](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html) and further elaborated in his [book](https://www.oreilly.com/library/view/clean-architecture-a/9780134494272/), is a well-known and effective way to structure software, promoting the key goals of modularity with high module cohesiveness and low module coupling.

![The Clean Architecture](https://blog.cleancoder.com/uncle-bob/images/2012-08-13-the-clean-architecture/CleanArchitecture.jpg)
*<div style="text-align: center; font-size: 80%;">
From Robert C. Martin's <a href="https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html">The Clean Architecture Blog</a></div>*

The function-oriented architectural style proposed here is consistent with the Clean Architecture principles.  By refining and adding some prescription to portions of the Clean Architecture, the Function-Oriented Architecture provides a simple way to structure software in adherence with the Clean Architecture.

See [Mapping to Clean Architecture](#mapping-to-clean-architecture) for details.




## Module meta-model (stereotypes)

The key idea is that, when decomposing software into modules, even though there may be hundreds of modules in an application, there should only be a small number of **kinds** of module (the stereotypes) that characterize distinct areas of concern, and each module should be of one and only one of these kinds.

The stereotype concept defined in the [Unified Modeling Language (UML) specification](https://www.omg.org/spec/UML/2.5) (see also https://www.uml-diagrams.org/stereotype.html) roughly aligns with the meaning we give it here.  However, our approach is more informal and focused on software modularization.

### Definitions

We will start with some definitions.  Detailed examples will be presented later.

For the purposes of this report, a ***module*** is defined as a unit of code that contains a cohesive portion of the application.  This is typically a source code file.  This is not to be confused with the definition of module used in languages like Java or Kotlin.

A ***module meta-model*** or ***module stereotype model*** is a specification of the different kinds of module that may be be defined for an application, the roles/responsibilities of each kind of module, and the allowed relationships and interactions between different kinds of module.  

A ***stereotype*** is any of the allowed kinds of module in a module meta-model.

A module M is an ***instance of*** of stereotype S if and only if M conforms to the specification of S in the module meta-model.

Stereotype S1 is said to ***depend on*** stereotype S2 if and only if instances of S1 are allowed to depend on instances of S2.  In our diagrams, this relationship is denoted by an open arrow.  Thus, the *depends on* relationship between two stereotypes is defined in terms of the *depends on* relationship between the instances of the two stereotypes.

Stereotype S1 is said to be a ***subkind of*** stereotype S2 (or S2 is a ***superkind of*** S1) to express the fact that all the roles and responsibilities of S2, as well as the *depends on* relationships from S2 to other stereotypes, are inherited by S1.  Thus, for modules written in class-based languages, this relationship does NOT imply any subtype relationship between the instances of S1 and S2.


### Object-oriented origins

The idea of stereotypes was independently introduced by Rebecca Wirfs-Brock, who coined the term in her 1993 Object Magazine article “Stereotyping: A Technique for Characterizing Objects and their Interactions” and in Ivar Jacobson's writings, including his 1992 book "Object Oriented Software Engineering: A Use Case Driven Approach".

As defined by these sources, the stereotypes define a meta-model for the decomposition of the system into a small number of different kinds of classes.  Such a meta-model not only defines the areas of concern that any specific class should focus on, but it also constrains how classes that belong to different stereotypes may connect or interact with each other.

Although these ideas targeted object-oriented software design, the general concept applies more generally to any kind of system.

### Simple object-oriented service stereotype model

This section describes a simplified traditional object-oriented web service stereotype model.  While a full object-oriented module stereotype model might include additional and more nuanced stereotypes, the purpose of this section is just to illustrate the module stereotype concept, in a familiar object-oriented setting, as an introduction to the next section on the function-oriented stereotype model.

<img style="display: block; margin: auto;" alt="BROKEN_LINK" src="/wp-content/uploads/2021/FOA_Images/OO_Stereotypes.png">

This kind of meta-model can be used to say that, no matter how many controllers, business services, data access objects, information objects, data transfer objects, and domain objects a service/application may have, if it complies with the meta-model then:
* The allowed kinds of module (the stereotypes) are depicted as boxes in the diagram.
* A Controller is responsible for mediating between the request listener and the service logic; it should not do anything else.  A controller may use a Data Transfer Object and call a Business Service, but not depend on any other stereotype.
* A Business Service is responsible for aggregating all the functionality associated with one or more service endpoints; it should not do anything else.  A business service may depend on information objects or call data access objects, but no other stereotypes.
* A Data Access Object is responsible for calls to the database; it should not do anything else.  A data access object may depend on information objects but may not depend/call on any other stereotype.
* An Information Object is responsible for holding data and core business logic; it should not do anything else.  An information object may only depend on other information objects and may not depend/call on any other stereotype. 
* A Domain Object is a subkind of Information Object that represents a business domain entity.
* A Data Transfer Object is a subkind of Information Object that is used to transfer data between application components.

<a name="core-stereotypes"></a>

### Function-Oriented Architecture module stereotype model

The function-oriented module stereotype model, depicted below, can be seen to have some similarities to the above simple object-oriented example, but there are also significant differences.

<img style="display: block; margin: auto;" alt="BROKEN_LINK" src="/wp-content/uploads/2021/FOA_Images/Core_Stereotypes-black.png">

The function-oriented architecture module meta-model defines the following stereotypes, responsibilities, and constraints:
* The allowed core kinds of module (core stereotypes) are depicted as boxes with solid black borders in the above diagram.  (Additional supporting stereotypes are discussed later in this report.)
* A ***Function*** stereotype represents a function that may take inputs, return outputs, and/or produce side-effects.  An instance of a *Function* stereotype is not necessarily a simple function, but a module that, when configured and/or wired, can produce a function.  As depicted in the diagram, a module that conforms to the *Function* stereotype implements a function interface.  Depending on the programming language and specific requirements, a *Function* module may take the form of a class, an interface, a trait, a prototype, a higher-order function, or a simple function.  The functions corresponding to *Function* stereotype instances have special meaning because they comprise the essence of the decomposition of the service/application into modules.  However, a service/application typically contains many other functions which are helper functions or sub-functions of the module functions.
* An ***Adapter*** is responsible for mediating between the request listener and the service logic; it should not do anything else.  An adapter may call a *Service Flow* and use instances of *Business Domain Data* and *Platform-Specific Data*, but it may not depend on any other stereotype.
* ***Service Flow*** is a subkind of *Flow*. An instance of this stereotype is responsible for providing the implementation of a service endpoint.  By convention, the names of modules implementing this stereotype have the suffix "***Sfl***".
* ***Flow*** is a subkind of *Function*.  An instance of this stereotype is responsible for orchestrating functions and should not do anything else. In particular, a *Flow* should not contain business logic or directly perform any input/output.  The functions orchestrated by a *Flow* are expected to be implemented by instances of the stereotypes that are subkinds of the *Function* stereotype, but *Flow* has no dependence on those stereotypes.  A *Flow* module may only depend on general function interfaces and on instances of the *Business Domain Data* stereotype.  By convention, the names of modules implementing this stereotype have the suffix "***Fl***".
* A ***Business Data*** module defines a data structure that contains business domain data and, possibly, convenience methods and validation methods.  A *Business Data* module may depend on other *Business Data* modules but it may not depend on any other stereotypes.  There are different varieties of *Business Data* modules:
  * A ***Business Entity*** module represents a core entity of the business domain.  By convention, the names of modules implementing this have no suffix as these are the nouns for the core concepts in the domain.
  * A ***Business Transfer Data***  module defines a data structure that contains business data and is used to transfer data between application components.  Examples include:
    * ***Function Input***, which defines a data structure used as the input to a function.  By convention, the names of modules implementing this variety have the suffix "***In***".
    * ***Function Output***, which defines a data structure used as the output of a function.  By convention, the names of modules implementing this variety have the suffix "***Out***".
    * ***Event***, which defines a data structure for an event to be published or consumed.  By convention, the names of modules implementing this variety have the suffix "***Evt***".

  We are not designating the different varieties of *Business Data* as subkinds because often there can be dependencies among them, with any of these varieties potentially containing any of the others.
* ***Business Function*** is a subkind of *Function*.  An instance of this stereotype is responsible for performing pure business logic; it should not do anything else.  In particular, a *Business Function* should involve no I/O or side-effects.  A *Business Function* module may only depend on *Business Domain Data* and *Supporting Function* modules.  By convention, the names of modules implementing this stereotype have the suffix "***Bf***".
* ***Data Access Function*** is a subkind of *Function*.  An instance of this stereotype is responsible for performing database I/O; it should not do anything else.  In particular, it should not contain any business logic.  A *Data Access Function* module may only depend on *Business Domain Data*, *Platform-Specific Data*, and *Platform-Specific Framework* modules.  By convention, the names of modules implementing this stereotype have the suffix "***Daf***".
* ***Service Client*** is a subkind of *Function*.  An instance of this stereotype is responsible for calling a service endpoint (internal or external); it should not do anything else.  In particular, it should not contain any business logic.  A *Service Client* module may only depend on *Business Domain Data*, *Platform-Specific Data*, and *Platform-Specific Framework* modules.  By convention, the names of modules implementing this stereotype have the suffix "***Sc***".
* ***Event Publisher*** is a subkind of *Function*.  An instance of this stereotype is responsible for publishing an event; it should not do anything else.  In particular, it should not contain any business logic.  An *Event Publisher* module may only depend on *Business Domain Data*, *Platform-Specific Data*, and *Platform-Specific Framework* modules.  By convention, the names of modules implementing this stereotype have the suffix "***Ep***". 
* A ***Supporting Function*** module is responsible for containing business logic functions that are reusable across multiple *Business Function* modules; it should not do anything else.  The functions in this kind of module must be pure, i.e., without any side-effects.  In particular, this kind of module may not perform any I/O.  A *Supporting Function* module may only depend on *Business Domain Data* modules.  By convention, the names of modules implementing this stereotype and the names of functions exported by this kind of module have the suffix "***Sup***".
* A ***Platform-Specific Data*** module implements a data structure that contains platform-specific data and, possibly, convenience methods and validation methods.  Such a data structure is used for data transfer to/from platform services, e.g., a database, a queue, an event hub, or a remote service.  This kind of module may depend on *Business Data* modules and other *Platform-Specific Data* modules, but it may not depend on any other stereotypes.

A key guiding principle in this model is that each of the subkinds of the *Function* stereotype does only one kind of thing.  

Another key principle is that only *Flow* (including *Service Flow*) modules are allowed to call other subkinds of the *Function* stereotype.  However, those calls are always through a general function interface, so there is no dependence between the flow and the modules that implement the called functions.

In particular, *Business Function*s, *Data Access Function*s, *Service Client*s, and *Event Publisher*s are not allowed to call each other.

Adherence to these principles enables module decoupling, an important feature of this function-oriented architecture. 




## Use case realisation and service structure

The term ***Use Case*** is commonly used and understood by both business analysts and developers -- we will not attempt to define it here.  

A service ***Endpoint*** is a high-level business function provided by the service-based application.  This is a term familiar to developers and business analysts.  From a business perspective, a service endpoint is a business function and, from a technical perspective, it can be implemented as a function in a programming language.

In a service-based application, the realisation of a use case involves interaction with one or more service endpoints -- a use case is realised by calling particular service endpoints in a particular sequence.

On the other hand, an endpoint may support the realisation of multiple different use cases.  In the diagram below, the preceding statements are represented by the many-to-many association between the *Use Case* and *Endpoint* entities.

![BROKEN_LINK](/wp-content/uploads/2021/FOA_Images/Use_Cases_and_Services.png)

The rest of the diagram represents how a *Service* is decomposed physically and logically.

A ***Service*** is defined here as a logical grouping of service *Endpoint*s.  *Note: In a microservices solution, all the endpoints in a Service would be functions operating on a single aggregate (or perhaps even a bounded context) in the domain-driven design sense.*

An ***Executable*** is what actually runs on the operating system.  A *Service* is made up of one or more *Executable*s and each *Executable* houses one or more *Endpoint*s.  Each *Endpoint* corresponds one-to-one to a *Service Flow*, which was defined in the previous section.  The *Service Flow* is decomposed in the [Core Stereotypes](#core-stereotypes) diagram.




## Vocabulary

With the concepts discussed so far, we have established a basic vocabulary that can be used by architects, business analysts, and developers to define the structure of services and express the realisation of use cases in service-based applications.  

The vocabulary is summarized below:
* *Use Case* -- as commonly understood.
* *Endpoint* -- high-level business function provided by the service-based application.
* *Service* -- logical grouping of *Endpoint*s.
* *Executable* -- binary that contains *Endpoint* implementations and runs on the operating system.
* *Service Flow* -- function that implements an *Endpoint*; a sub-kind of *Flow*.
* *Flow* -- function whose only responsibility is to orchestrate other functions.
* *Business Function* -- function that only performs pure business logic.
* *Data Access Function* -- function that only performs database I/O.
* *Service Client* -- function that only calls an internal or external service endpoint.
* *Event Publisher* -- function that only publishes an event.
* *Business Entity* -- core entity of the business domain; the main nouns in the vocabulary.
* *Business Transfer Data* varieties:
  * *Function Input* -- input to a function.
  * *Function Output* -- output of a function.
  * *Event* -- an event to be published or consumed.



## Module granularity: function-oriented vs object-oriented

Function-Oriented Architecture (FOA) implies a finer-grained module structure than with an object-oriented decomposition.  Below, we compare the module structure, function granularity, and call stack for the function-oriented and object-oriented decomposition approaches.

### Common object-oriented decomposition

The following diagram exemplifies a typical object-oriented decomposition of a service -- a controller calls a business service (a.k.a. [use case interactor](http://www.plainionist.net/Implementing-Clean-Architecture-UseCases/)) which in turn calls a DAO (or repository).  The business logic is embedded in the XyzBusSvc main method.

<img style="display: block; margin: auto;" alt="BROKEN_LINK" src="/wp-content/uploads/2021/FOA_Images/Call_Stack_OO_Coupled.png">


XyzBusSvc can reference the interface of AbcDao instead of its concrete implementation and thus avoid the problem of having the business logic depend on platform-specific logic.

While this decomposition achieves some separation of concerns and some reduction of module dependencies, it has the following drawbacks:
* The core business functionality (validate input, process Abc, and prepare response) is comingled with the doIt method's primary purpose which should be just orchestration logic.
* The business functionality can't be easily tested on its own -- the various pieces need to be tested together and XyzBusSvc requires a mock implementation of AbcDao.
* XyzBusSvc has a semantic dependence on AbcDao.  This will be discussed with the example below.

### Less coupled object-oriented decomposition

The previous decomposition can be modified so that the business logic is factored-out into separate methods. This way, the main method of XyzBusSvc is only responsible for orchestrating calls to methods on XyzBusSvc itself and on AbcDao.

<img style="display: block; margin: auto;" alt="BROKEN_LINK" src="/wp-content/uploads/2021/FOA_Images/Call_Stack_OO_Less_Coupled.png">

This decomposition has the following advantages over the previous one:
* Improved separation of concerns as now the doIt method is only concerned with orchestration.
* Improved testability as each of the three core business function methods can be tested on its own without the need for mocks.

However, some drawbacks remain:
* XyzBusSvc's main doIt method has a direct dependence on the three core business logic methods and all four methods are in the same module/class/file.  This means that it is harder to have these methods independently implemented by different developers.  In particular, source version management merge conflicts can arise if the methods are independently developed.  This may not be a problem for a relatively simple service but can be significant for a complex service.
* XyzBusSvc has an avoidable semantic dependence on AbcDao.  The business service class is semantically coupled to the DAO interface because it is aware of the name of the interface and the names of the abstract data access methods it needs to call.  In addition, if the AbcDao interface contained a method (e.g., listOverdueAbcs) that is used by a use case interactor (business service) other than XyzBusSvc, then, because XyzBusSvc depends on the entire DAO interface, it indirectly depends on changes to code it has no interest in.

### Function-oriented decomposition

A function-oriented decomposition takes the above object-oriented decomposition one step further:
* There is a separate *stereotype function* for each method in the previous object-oriented decomposition.  A ***stereotype function*** is produced in a module (file) that implements one of the stereotypes in the function-oriented architecture stereotype model.
* Each individual stereotype function lives in its own file/module/class/interface/type (depending on the programming language).
* The main service function (the service flow) depends only on general functional interfaces and knows nothing about specific classes/interfaces that implement the various other functions.  This is key to avoid coupling.

<img style="display: block; margin: auto;" alt="BROKEN_LINK" src="/wp-content/uploads/2021/FOA_Images/Call_Stack_Functional.png">

The implications of (a) dependence on general functional interfaces versus specific named interfaces and (b) separate functions with more files versus grouped functions with fewer files are:
* By only using general functional interfaces, the function-oriented decomposition avoids the kind of semantic dependence found in an object-oriented decomposition, where a business service (business case interactor) semantically depends on the data access object.
* For a relatively simple business service (use case interactor) like the above XyzBusSvc, if one developer is responsible for coding all the methods in the business service, then having fewer files would be advantageous.  However, for a complex business service, with potentially dozens of methods, having them all in one file makes it harder to divide the work among multiple developers as they would all be working on the same file and source code merge conflicts would be likely.  This can be mitigated by creating sub-services (sub-interactors), but the "one stereotype function per file" approach is simpler and more flexible for work assignment and change control.
* Importantly, for a complex business service, the physical separation of the orchestration logic (XyzSfl or XyzBusSvc.doIt) from the core business functions is especially beneficial as the high-level orchestration logic may be assigned to a different developer role (in terms of skill and seniority) than the lower-level core business logic.
* For a data access object, one would typically have the same developer responsible for all its methods, so having fewer files would be advantageous.  When each data access function lives in a separate file/module/class, common configuration logic (e.g., assigning a database connection) needs to be repeated for each function.  This, however, is typically a one-liner, so it doesn't weigh heavily.
* A similar consideration to the above may apply for business functions, though less likely as different business functions are less likely to share configuration parameters.
* Having each stereotype function in a separate file provides better visibility to the scope of work and makes it easier to estimate, assign, work on, and track the status of work units.
  * As noted earlier, work assignment and source control are easier, and merge conflicts are avoided when each functional component lives in a separate file.
  * A Kanban board for an FOA decomposition is simple, clear, and explicit.  When using an object-oriented decomposition, one could track individual methods on the Kanban board but there are dependencies, as discussed above, among methods in the same class.

Overall, as demonstrated by large projects using the function-oriented architecture, the advantages of having each function in a separate file usually outweigh the disadvantages, especially for complex services.



## Grouping pragmatics and coupling-cohesiveness trade-offs

Coupling and cohesiveness of modules cannot necessarily be both optimised independently.  It can be advantageous to trade-off a little less coupling for a little more cohesiveness.

This may be accomplished by grouping into one file multiple stereotype instances that are logically related.  For example, data access functions associated with the same entity might be grouped together, especially as they share similar configuration information.

By grouping a set of stereotypes into a file, there may a higher potential for merge conflicts.  However, when the grouping involves stereotype instances that would naturally be assigned to a single developer, that concern is removed.

The possible advantage of such a grouping is the increased cohesiveness and reduction of cognitive load or noise resulting from the reduction of the number of files.



## Coupling and code navigability trade-offs

The function-oriented architecture style's use of general function interfaces (instead of more traditional object-oriented interfaces with multiple methods) can make it harder to navigate from a flow that uses a stereotype instance to the stereotype's implementation.

To mitigate that inconvenience without entirely giving-up the decoupling advantages previously discussed, the following techniques can be used.  Each has its trade-offs:
1. **Naming of the variables representing stereotype dependencies**

    Simply name each variable representing a stereotype dependency with the name of the stereotype instance, possibly changing the case of the first character to conform with language requirements or conventions.

    For example, in Kotlin, a dependency on a stereotype instance `FooBf` that produces a function of type `(Int) -> String` would appear as `val fooBf: (Int) -> String`. 
    
    This allows one to find the module implementing the dependency using the IDE's search capability.  

    This approach works well enough and has been successfully used on large projects.  The key drawback of this approach is that when the dependency's type name is changed via refactoring then the variable name needs to be adjusted manually. If the variable name is not adjusted, the code still works but the ability to navigate from the variable to the stereotype implementation is gone.

2. **Dependence on functional interfaces with concrete methods**

    In JVM languages like Kotlin and Scala, where interfaces may have concrete methods, a stereotype instance may be implemented as an interface with a concrete method that implements the desired functional interface (e.g., `invoke` in Kotlin or `apply` in Scala).  To facilitate navigation, a flow that depends on another stereotype instance may have a variable with the type of the interface that defines the instance.  
   
    For example, in Kotlin, a dependency on a stereotype instance `FooBf` that produces a function of type `(Int) -> String` would appear as `val fooBf: FooBf` instead of `val fooBf: (Int) -> String`.  
   
    In this case, the name of the variable is immaterial as the navigation is directly provided by the name of the interface that defines the stereotype instance of the dependency.  With this approach, navigation is straightforward and resilient to name refactoring, but the flow that holds the variable is directly dependent on the implementation of the other stereotype.  
   
    This may still be OK, provided that: (a) the flow only needs to ever access a single implementation of the dependency (e.g., there is no need to support variants of the same DAF with implementations for different databases) and (b) there is no need to unit test the flow in isolation.  If (b) is not true then mocking the dependency would be tricky.  One of the advantages of the function-oriented architecture style is that mocks are normally not needed and, when used, should be trivial to implement.

3. **Dependence on type aliases**

    This is a middle ground between the two above approaches and may be the best policy for most projects.  This technique, which can be used with languages that support type aliases (e.g., Kotlin, Scala, Go, TypeScript, Rust), works as follows:

    * A type alias is defined for the general function interface implemented by each stereotype.
    * The type alias should be named like the stereotype instance but with a suffix.  For example, in Go, for a stereotype instance called `FooBf` that produces a general function of type `func(int) string`, the type alias could be `type FooBfT = func(int) string`.
    * The type alias can be used instead of the corresponding general function interface by other stereotype instances that depend on that general interface.
    * The type alias must be used in the file that implements the dependency stereotype instance to ensure that the type alias corresponds to the actually implemented general function interface.
    * In most cases, the type alias should be defined in the file that implements the dependency stereotype instance.  This promotes higher cohesiveness.  There is no need to worry that the flow that depends on the type alias would end-up depending on the dependency stereotype implementation.  Even though the type alias and the implementation may be in the same file, the flow need not have any knowledge of the implementation.
    * For stereotype instances that require multiple implementations in different technologies (e.g., a DAF that needs to be implemented for two different databases), the type alias should not be placed in any of the technology-specific implementation files.
    * By searching in an IDE for the type alias minus its suffix, the implementation file can be easily found, thus providing navigability without coupling.  When the type alias is defined in the same file as the dependency stereotype instance, by looking-up the definition of the type alias one would land directly on the dependency stereotype instance file.



## Function flow pattern

  ### Sample flow diagram

  ### Sample flow code

  ### Flow composition

  ### Mapping of functional to technical design

## Module stereotypes

  ### Adapters

## Levels of decoupling

<a name="mapping-to-clean-architecture"></a>
## Mapping to Clean Architecture 

## Benefits

## Programming language requirements

FOA can be successfully practised with pretty much any modern programming language.  However, when using languages with support for both methods and top-level functions (Kotlin, Scala, Go, TypeScript, JavaScript, C++, etc.), the coding idioms associated with FOA are easier to express and there is a lower cognitive load on developers as compared to languages that do not support top-level functions. 


## Testability

Component or unit testing of function-oriented architecture software components is discussed in this section.

An important aspect of the function-oriented architecture is that the stereotypes that interact with the runtime platform (DAFs, SCs, and EPs) are, by design, [humble objects](https://martinfowler.com/bliki/HumbleObject.html), i.e., objects that are so simple that there is little or no logic in them for unit testing.

  ### Business functions (BFs)

  ### Data access functions (DAFs)

  ### Service clients (SCs)

  ### Event publishers (EPs)

  ### Supporting functions (SUPs)

  ### Flows (FLs) and service flows (SFLs)


## Project management implications

## Methodology

  ### Use case realisation

  ### Service endpoint function inventory

  ### Design with code

, and to design for this kind of structure using code.

## Estimation

## Supporting frameworks

  ### Type-safe dependency injection

  ### Type-safe configuration

  ### Other

## Microservice Project, Package, Class and File Naming Standards

## Miscellaneous microservice architecture considerations

  ### Desirable traits

  ### Microservices scope

  ### Event standards and governance

  ### Event sourcing implementation

  ### Reactive

## References

* [Your Server as a Function](https://monkey.org/~marius/funsrv.pdf), by Marius Eriksen (2013)
* [The Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html), By Robert C. Martin (2012)
* [Clean Architecture: A Craftsman's Guide to Software Structure and Design](https://www.oreilly.com/library/view/clean-architecture-a/9780134494272/), by Robert C. Martin (2017)
* ## [Common web application architectures](https://docs.microsoft.com/en-us/dotnet/architecture/modern-web-apps-azure/common-web-application-architectures), Microsoft (2020)
