# AWS LAMBDA - event, do your magic and act

> An event-driven computing service for the cloud

Amazon AWS' new service Lambda runs your code in response to events. All things operation is really done for you from scaling, availability to logging and monitoring. Instead of renting compute power Lambda lets you ship code and run it in response to specified events. Pricing in lambda is therefore based on how much computing time your functions use. A pretty lenient free tier is also included.

<!-- -->
>AWS Lambda is one more abstraction that lowers the necessity of what we have to build and maintain to the absolute minimum. By giving us a scalable, event-based infrastructure we can split up tasks that we might not have wanted to split previously because of operational complexity.
>
> *Florian Motlik - [blog.codeship.com](http://blog.codeship.com/aws-lambda-introduction/?utm_source=CodeshipNewsletter&utm_medium=newsletter&utm_campaign=AWS)*


When creating Lambda Amazon wanted users to enjoy the following properties: No infrastructure to be managed. Pricing based on the time your code is actually being run. No vendor specific code or library needed, your code should simply work as anywhere else.

    // An actual codesnippet running on AWS Lambda
    exports.handler = function(event, context) {
        context.done(null, 'Hello World');
    };


**Talk from re:Invent - [Getting started with Lambda](https://www.youtube.com/watch?v=UFj27laTWQA)**

Events are central when working with Lambda. More and more of AWS tools and services now emits events. S3 will emit an event when something is changed. DynamoDB offers a stream of delta changes of the data stored. In addition Lambda can listen to custom events from the like of your web application or internet of things box.

Worth to mention is that Lambda is not designed for request response. Hence replacing your entire backend with Lambda is probably not the way to go yet. You would need some backend or database that will serve data back to your client, Lambda's could be very capable of doing the actual writing and updating. More obvious matches for Lambda however are in the like of doing background jobs, sanitizing data and aggregating data.  

<!-- -->
> Tag code to your data

Not long ago servers where being cuddled and cared of by ops. Today we are seeing a drift towards immutable server patterns where the servers themselves are ephemeral and provisioned programmatically. Lambda is really a step even further where your server only last for the milliseconds a request takes to process before it goes away.
