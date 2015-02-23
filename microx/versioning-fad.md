# The versioning fad

When discussing microservices you often hear about Docker, Netflix, Spring Boot and versioning. Specifically how to do versioning and how complex it can become in scenario xyz. Following some rules of thumb however versioning of services can be pretty straight forward.

### API versions
The important version to think about for a microservice is its API version(s). Semantic versioning is a fine approach for versioning of APIs.

A semantic version consists of the following parts `major.minor.patch`. A bumped major version means backwards compatibility is broken. A bumped minor means some functionality is added, but backwards compatibility with the major is kept. A patch does not add functionality, but typically fixes a bug or improves performance.

Releasing new minors and patches should by definition of semantic versioning be straight forward. It just would not impose any changes on any other service in your system. However, releasing new majors would break all consuming services if no measures of backwards-compatibility are made.

There are typically two approaches to backwards compatibility in the face of a breaking API - here a new major version. The first is to duplicate the deployment of your service. The second approach is to duplicate your versions in code.

To duplicate the deployment of your service you can simply run multiple versions of your API behind a loadbalancer. The issue with this approach however is when security issues or bugs arise that effects more versions of the service. Changes would have to be made independently in the code of all running versions. To actually access and change the code of multiple versions of the same service would also imply either forking your repository or rely on vcs magic with branches or tags. This approach would also imply an operational overhead to make sure multiple versions of a service is running.

Duplicating APIs in the code in order to support multiple versions seems a more viable approach. So this approach demands a bit more of the developer, code has to be duplicated. And the code has to be restructured in such a way that all versions can still be supported. Importantly however is that this approach is more transparent to the developers working on a service. Secondly it does not add any operational overhead at all. The joyous part of this approach is when a version of the API is no longer supported. Then you can simply remove it from the code. Everybody enjoys to delete code.

### The version of a Microservice in your system
I added the "in your system" such that both scenarios of handling backward compatibility in your APIs are applicable.

The thing a consumer of your service cares about is whether the API version it needs is supported by your service or not. As you in many scenarios will have multiple major versions of your service's API running simply using a API version is not sufficient as a versionnumber for your service. A meaningful version scheme for your service by consumer standards is really a tuple of the current API versions supported.

Other things can factor in to the version of your service than consumer needs only. If changes to the underlying infrastructure such as logging or hosting are carried out your service version might want to take account of that as well. Semantic versioning of the service itself would be a good fit for this.

How to build a versioning scheme that tells both what API versions it supports and what semantic version itself is without simply using something like a JSON object has dumbfounded me. Using a JSON object to hold this information i propose something simple as the follows:

```JS
version {
  "service_version": "1.42.15",
  "api_versions": ["1.9.3", "2.1.9"]
}
```
Here consumers would query against the `api_versions` to ensure their demands are met. In the mean time the `service_version` will tell the version of the service code itself. I believe the patch number of all `api_versions` should be bumped when a new version of the service is released. This such that you consumers more easiliy can give you notice if an unforseen bug when changing some seemingly unrelated code has arised.

- service discovery with info about what api version needed a service discloses what api versions it holds
