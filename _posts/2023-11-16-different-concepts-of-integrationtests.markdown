---
layout: post
title: "Different concepts of integration tests and their pros/cons"
date: 2023-11-16 12:02:41 +0100
categories: jekyll update
---

# Different concepts of integration tests and their pros/cons

As a strong advocate of integration tests as the base of a solid test automation architecture, I found multiple ways on how to setup your integration tests.

Both ways have their own pros and cons which I want to further evaluate.

## What are integration tests anyways?

There are multiple definitions of integration tests. Generally speaking an integration tests is a test level where individual units or components of a system are combined and tested as a group.

This is as broad as it gets. This can basically mean a multitude of things that would count as an integration tests.

In a microservice environment, I see an integration tests as a means to make sure the contract that your SUT has with outside dependencies (database, other services) does not break, when changing the implementation in your SUT.

## Example of different integration test setups

Let take the example of a generic e-commerce 'order service' which allows you to make an order for a product.

This service is what would be called a 'microservice'. It has a REST-API and when it receives an order, it will write the order to a database.

In order to validate parts of the payload that the service receives, it will call another service, which we will call 'user service'.

Please note that this serves as an example. The following two variants can also be applied to frontend applications.

### Variant I: Blackbox style

In this example, we will make use of the possibility to test our SUT as a whole application.
We will treat the system mostly as a black-box, evaluating what kind of inputs produce a certain output.

All external dependencies that we can control easily, such as an in-memory database or a message queue, we will start aswell.
Dependencies we can not control we will mock.
Lets say that we do not have direct control over the 'user service'.

This means that our test generally would follow the following steps:

1. start the SUT in a docker container or directly as a process
2. start all possible dependencies (database, message queue, ...) in a docker container or directly as a process
3. start mock for the request to the 'user service'
4. run the actual test against the SUT
5. validate the order in the database
6. validate the http requests to the user service mock

#### Pros

- Tests will hit all layers and all frameworks used

Most frameworks have their own way of bootstrapping functionality that is commonly used, like routing annotations, etc.
The correct configuration and use of these frameworks is testable through this approach.

- Close to productive use

Related to the point above. Testing like this will be closer to the productive use of the service, promising less problems when actually integrating

- Focuses on validating the interfaces that the SUT has to the outside world

Especially good for microservices, where you potentially have interfaces to multiple other services.

- Good reproducibility for real-life issues

When facing any sort of productive issues, this approach makes it easier to reproduce these issues, as it is set up close the real usage

#### Cons

- Tricky setup of dependencies

Especially in CI/CD pipelines, setting up and provisioning the service and its dependencies might be difficult.
There are solutions that make this easier however: https://testcontainers.com/

- HTTP mocking more difficult

The more complex the http requests/response are, the more boilerplate code you will need to write in order to make the test run. If you have multiple dependent services that you need to mock, then it will get even more complex.

- Debugging might be more complex

Bigger applications even more.

- Running tests in parallel might be problematic

Depending on the application.

### Variant II: Whitebox style 

In this example, we will use a more conventional way of integration tests. Here, we use a more white-box approach and directly access and mock internals of the service.

This means that our test generally would follow the following steps:

1. Prepare mocks for our dependencies (mock the repository connection, mock the clients that talk to the user service)
2. Run test against the rest controller function 
3. validate mocks
4. validate return value of function

#### Pros
- Easy setup

Especially in CI/CD pipelines this approach is a lot easier, since dependent services/databases are completed managed "as code". 

- Faster run times

No latencies through not using http will result in faster test run times.

- Bigger possibilities on what to test

Want to test what happens if the database connection is down? Any other edge cases? No problem, say no more.

- Easy debugging

Easier to locate problems due to the white-box nature of the tests.

#### Cons

- Less real-life applicability

You tend to design tests that are not realistic. 

- Less trust in test results

You most likely have to work around some of your used frameworks. You will tend to miss problems that come with those frameworks or the way you use it.
Example: Classes with SpringBoot annotations in Java. 


## Conclusion

Both approaches come with their own problems and benefits. 
Variant I is ideal for a microservice that is not overly complex. It will provide good lifelike results.

Variant II is more suited for a monolith with a lot of dependent services with complex interfaces that may be hard to mock. 

Please note that there is no clear line between those two variants. It is much more of a spectrum, where you can decide on where to put your efforts for your specific use case. 

