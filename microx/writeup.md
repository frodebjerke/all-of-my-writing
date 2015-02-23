# Microservices writeup

## Why microservices
- Scaling
- Software out faster
- Security
- Polyglot
- Inherently open source
- Modelled around business domain
- Producitivity hockey stick

## Problems with microservices
- A lot of choices
- Testing more complicated
- Monitoring more complex
- Resiliency
- Productivity hockey stick


## Architectural approach
- Evolutionary archiecture
  - "Just enough architecture"
  - Town planner - not architect
  - Evo architecture is in the gaps
- Good services are identified by
  - High Cohesion
    - things that change together, stay together.
  - Loose coupling
    - Lock - step - release
  - (Business) Bounded contexts
    - a business person can understand the flow between services and applications.
    - Techincal boundraries gives cohesion issues
    - Technological boundraries change faster than domain boundraries
  - A team owns the service
    - A team that owns a business bounded service become domain experts
- Choreography over orcehstration
  - Orchestration
    - Central orchestrator becomes smart and important, services dumb.
  - Choreography
    - State is difficult to know
      - Use central monitoring
      - Start state should have end state within time X.
- Testing and monitoring
  - Tests become more difficult and complex in a distributed system.
  - Rather fail fast than test rigourosly

## Tackling the issues

### Breaking changes
- Easy when you know that you break somebody
- Contract tests to know when you unkowingly break somebody
  - Pact is a decent tool
- Handling API versioning
  - Side by side
    - Deploy several versions of the same service, supporting all consumers
    - Netflix does this
  - Expand remove
    - Handle versioning with several versions of the API in the Service code.
    - Semantic versioning
    - Commonly the best choice

## Things to consider

### Higher level REST (hypermedia)
  - Payoff later
  - Versioning
  - Read: [Maturity Model by Martin Fowler](fowler) and [WebApi Design by Apigee](apigee)

### Collabration styles
  - Sync versus async
    - Event based is async
    - Req/res could both be sync and async
  - Pastel's law
  - Tools
    - Message brokers
      - Handles resiliency
      - Can manage client state
    - Atom
      - Not for low latency
    - Actor Frameworks
      - Tradeoffs - lock in does a lot
    - ZeroMQ
      - Low latency queue
      - Same issues as with Atom
  - Generally async communication can be really hard

### Shared code
  - Use with caution
  - DRY not always
  - Shared code could couple else loose coupled services
  - Changing the version of a shared library one place should not affect other services
  - Be specially aware of serialization protocal sharing libs.
  - Client libs ONLY transport

### Servers and devops
To first begin with microservices, some level of devops should already be present. Further the level of devops should heavily effect many of the choices to be made.

##### Levels of Devops
```
1. Automatic deploy
2. Provisioned servers
3. Host provisioning

(higher number better)
```

##### Single service per host
- Easier to reason about
- Easier to provision
- Fewer side effects
- Enforces interpendendence
- Needs host provisioning

##### Multi services per host
- Lower host-management overhead
- Cheaper

### Logging
Logging is one aspect of the system that should be standardized for the entire architecture. As one task might go through numerous systems there is no single log file one can inspect to look for. Therefore central log aggregation is highly recommended. Central logging in realtime is also helpful monitoring the system via dashboards. Great tools for realtime monitoring and central log aggregation such as ELK, Graphite and Hystrix.

##### Logging key metrics
```
1. Response time tracking
2. Logs from machines
3. Monitor downstream services
4. Track health
```
##### Corrolation IDs
Corrolation IDs help identifying chains of a request. When a request is passed to a downstream service a corrolation ID is attached to its header. Such one can easily follow a requests trip throughout the system.

## Splitting stuff
- Take the low hanging fruits first
- Dry not always


### Some tools and resources
- Refactoring databases, book.
- Schemaspy
- Teraform
- Mountebank - stub endpoints, check your dependencies
- Pact - consumer driven contracts
