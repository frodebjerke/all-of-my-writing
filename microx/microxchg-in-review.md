# Microxchg in review

Mid february I was lucky enough to attend the Microxchg (read: Microservices exchange) in Berlin. Microxchg is a two day conference consentrated on the realm of microservices. It was the first edition of this conference.

There were a lot of impulses to take away from this conferences. Also a lot of trends in microservices, distributed architecture or more generally computing could be seen. In the following sections we will delve in to what I think of as my greatest take aways. My takeaways are both in the form of approaches, technology and trends.

## Monitoring with invariants

This section is based on the ideas of Richard Rodgers talk titled "Measuring Microservices".

A central part of a successful microservices system is monitoring and metrics. Exhaustive testing of a microservices is really not a viable option today because of the greatly increased complexity added from the system being distributed. However with good monitoring

<!-- When splitting and distributing applications exhaustive testing of the entire system seems a non-viable option. A reason is that it simply becomes to many paths through the system. Another is that compared to a monolithic system you now have to take network and infrastructure into account. Splitting and distributing your system really adds a lot of new ways in which your system can fail.

Instead of relying on exhaustive testing for trusting your system, microservices systems seems to often rely on monitoring. Instead of focusing on spending a lot of energy upfront ensuring nothing will ever fail, spend time  -->

## Java, Netflix, Spring Boot and more

A general feel I got during the conference is that most people doing microservices do so using the JVM. (Or are there really such a ratio between jvm and other platforms today) Great tools therefore exist on this platform. Also a lot of focus was given to this platform during the conference.

Netflix uses microservices. Netflix' adventure into microservices has really been good to the community as they have made a lot of their tools open source. As they mostly work on the JVM most of their tools are for that platform. You can take a look at all their open sourced tools at their [github-pages](http://netflix.github.io/).

Spring is a frameworke used in many Java projects. Among their newcomings is [Spring Boot](http://projects.spring.io/spring-boot/). What Spring Boot really does is creating opinonated applications favoring convention over configuration. Spring Boot will use default configuration where none is given and

## Developers on call
An important trick implemented by many of the companies building microservices is putting developers on call. That means making developers responsible of the operation of the services themselves have created.

> At Netflix you (as a developer) are done when your code is no longer in production
>
> *Adrian Cockroft - Former cloud architect at Netflix.*

From the conference there were really three main wins by putting developers on call. First the number of errors in production dropped dramatically. Developers really loath being called in at the middle of the night. Second, supporting tools became much better and more numerous. And last developers get more freedom in choosing appropriate technology as it is themselves that must operate it.

This approach is not without drawbacks however. Developers will not like it at all. There will be a lot of shouting initially.

## And databases
Transactional databases no longer viable as your system is highly distributed.

> Nobody talks to my database.

## Generic architecture review
A generic architecture review of a large system looks something like the following
- Building features takes too long
- Technical debt well-known and not addressed.
- Deploy is way too complicated and slow
- Architectural quality has degraded
- Scalability has reached its limit
- -ility problems
- Replacement way too expensive

## Project equals system is stupid
Who are the initiator of a project to say the system boundraries
