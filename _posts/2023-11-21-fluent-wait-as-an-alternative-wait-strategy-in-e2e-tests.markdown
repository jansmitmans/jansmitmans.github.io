---
layout: post
title: "A better approach to dealing with asynchronous processes in automated tests"
date: 2023-11-21 12:02:41 +0100
categories: jekyll update
---

# A better approach to dealing with asynchronous processes in automated tests

Most sofware and qa engineers have faced the issue where an automated tests shows flaky behaviour.

In some cases, this is due to timing or synchronization issues between the test execution and the system-under-test (SUT).

Simply put, the tests expects a state to be reached, which the SUT does not (yet) have.

Symptom: The tests fails sometimes, but not all the time.

### Example how this can happen

In order to create an order in a generic e-commerce application, you first need to register a user.
The registration is done via REST, but takes a couple of seconds to be asynchronously written to a database, despite the service returning `201 Created` as a response before the database update is done.
This is due to the asynchronous nature of the SUT.

When you now try to add an order for the user via API, then you might receive a reponse that the user is unknown (right now).

### Another example

After clicking a button in a UI via the e2e-UI-testing tool of your choosing, you have to wait for several elements to be available, before the tests can verify the texts behind those elements.

### How to mitigate this problem

There are several well known 'waiting strategies' to tackle those issues. Most are known through Selenium.

1. Implicit wait
2. Explicit wait
3. Fluent wait

You can find a lot of sources on most of those online, so I will not go into too much detail.

The typical way the symptom from the examples above is treated is by a simple

```typescript
await driver.sleep(5000);
```

There is a bit of a downside to this.

- It will always use up the maximum time, even though there only might be 1% of test runs where this is necessary.
- Depending on how often this is necessary, this time spent adds up.

Imagine a situation where you have to wait for a synchronization in 50 tests. A synchronization takes 1-5 seconds.

Let us assume the average would be 3 seconds per tests. This execution would then take 150 seconds.

Now imagine you wait longer and test now takes 5 seconds flat. Suddenly the test execution time is at 250 seconds.

You can tell, that these wait times add up. And this is just 50 tests in a very simplified calculation.

Why would you wait for 5 seconds each test, if there is a more flexible approach?

## Fluent wait to the rescue

![Fluent Drake](/_assets/fluentWait-drake.jpg)

As I have discountered this issue quite often in my past projects and have created a [npm library](https://github.com/jansmitmans/fluent-wait) that tackles this issue with a flexible approach to waiting.

Core concept is that executes a given function in configurable intervals until a condition is met. If the condition is never met, the function times out and returns an exception.

If the condition is met, the function will return the result of the given function.

To put this in perspective, for the first example you could to the following

```pseudocode
1. register a new user
2. access the GET /users/{userId} endpoint every second until the timeout of 5 seconds or until the function returns the expected user
3. proceed with the test
```

Of course this will need some fine-tuning to achieve ideal results, but will save you test execution time and increase your flexibility quite a bit.

Actual code would look something like this

```typescript
async function getUserById(userId: string): Promise<any> {
  return await axios.get(`yourEndpoint/users/${userId}`);
}

const pollingConfiguration: PollingConfiguration = {
  delayTime: 1000,
  timeout: 5000,
  pollingStartAfter: 0,
};

const user = await fluentWait(
  getUserById,
  (result) => result !== null,
  pollingConfiguration
);
```

## Wrap-up

Fluent waiting can help you achieve more flexible and dynamic (test-) code, that runs faster.

What more is there to want? :smile:

Feel free to use [it](https://github.com/jansmitmans/fluent-wait) in your project and give me feedback through GitHub.
