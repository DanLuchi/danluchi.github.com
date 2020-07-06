---
title: Flows, Components, and Isolated Business Logic
date: 2020-07-06T15:49:00-05:00
---

### A better testing pyramid for front end applications

If you’ve read anything about software testing, you’ve probably seen the Testing Pyramid. They have different layers, but they all convey the same idea: we should prefer unit tests to integration tests because they are faster and cheaper. I like this one from Martin Fowler’s blog because it’s not as rigid as most and I have less rigid views on testing than most.

![Test Pyramid](https://github.com/DanLuchi/danluchi.github.com/blob/master/images/test-pyramid.png?raw=true "Test Pyramid")
[https://martinfowler.com/bliki/images/testPyramid/test-pyramid.png](https://martinfowler.com/bliki/images/testPyramid/test-pyramid.png)

As front ends have gotten more complicated in the last few years, front end testing has become more and more important as business logic gets moved to the client. I’ve had difficulty adapting the traditional test pyramid for the front end development environment. A lot of the tests that I wanted to write didn’t seem to fit into the bucket of a “unit” or “integration” test and even when I backed into what should be a unit test vs an integration test, the distinction didn’t seem that useful or helpful.

So here is a new test pyramid for front end development. It has given my team a lot more clarity about what we want to test and where.

![Pyramid with flows at the top, components and isolated business logic at the bottom](https://github.com/DanLuchi/danluchi.github.com/blob/master/images/flows-components-business-logic.png?raw=true "Flows, Components and Isolated Business Logic Pyramid")

## Isolated Business Logic

These are the unit tests that you are already familiar with. You probably have a testing framework like [Jest](https://jestjs.io/) or [Mocha](https://mochajs.org/) and it works well enough. I refer to this as Isolated Business Logic because when testing your business logic is difficult, that’s usually because it’s wrapped up with other concerns like presentation and persistence. 

A good example of isolated business logic is a form validation function. Form validation can be very difficult to test when it’s embedded in a component that renders the form and saves to a Redux store or even a backend, but at its core, you just have a function that takes some data and returns true or false.

## Components

You may have heard [presentational](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0) or [pure](https://medium.com/groww-engineering/stateless-component-vs-pure-component-d2af88a1200b) components. The idea is to extract all of your business logic from these components to make them only concerned with how your app looks. This makes testing much simpler: arguments come in, markup comes out.

Let’s go back to our form validation example: now that we’ve extracted the validation logic from our component, we don’t need our form to test every possible input condition, we only test what the form looks like when it's valid and what it looks like when there is an error.

I’ll discuss tools more in depth later, but making the distinction that components are presentational allows us to consider alternative ways to test components such as visual regressions.

## Flows

At the top of our new pyramid are Flows. You can think of these as “integration tests” or “end to end” tests, but I chose the word “flows” deliberately. These are tests of how a user interacts with your application. They should be driven in the same way that a user drives your application: clicking and typing. Just like your typical test pyramid, these tests are slower and thus more expensive, so they should be used sparingly.

![Pyramid with flows at the top and a small arrow pointed to it. Components and isolated business logic are the bottom with larger arrows pointing to them.](https://github.com/DanLuchi/danluchi.github.com/blob/master/images/flows-components-business-logic-with-arrows.png?raw=true "Flows, Components and Isolated Business Logic Pyramid")

For the last several years, writing front end tests has always seemed like a pain. It seemed like I never knew what tests to write, the tests that we had were brittle and didn’t provide very much value, and I rarely felt like my tests put positive pressure on the design of my code. These ideas have been swirling around in my head for a few years until I was able to put words to the concepts. For the first time, I finally feel like I have a good approach for writing front end tests.

## Tools

One of the biggest benefits of breaking up your testing strategy into 3 distinct pieces is that you can think about tooling differently. My team currently uses different tools for each type of testing.

For our isolated business logic, we use [Jest](https://jestjs.io/). [Mocha](https://mochajs.org/) is also a good choice that I have used in the past. Once you isolate your business logic into functions, it is pretty easy to use anything for this kind of testing.

Components are typically the first thing we build when creating new features. We develop them in isolation with [Storybook](https://storybook.js.org/) and then use [Chromatic](https://www.chromaticqa.com/) for visual regressions and accessibility checks. [Jest snapshots](https://jestjs.io/docs/en/snapshot-testing) are another approach, but the visual regressions that we get with Chromatic are really excellent. 

Testing flows is something that I’ve wanted to do for front-ends for a long time, it’s possible with Jest or Mocha, but setting up your tests and mocking out API calls is really difficult. In the past, I’ve just given up on doing this kind of testing. My team started using [Cypress](https://www.cypress.io/) a few months ago and our experience has been excellent. We generally start with the API stubbed out to allow ourselves to work on the frontend by itself. You can choose to leave the API endpoints stubbed out or have it talk to a real API and turn these into true end to end tests.

How does your team approach front end testing? What problems are you having? What have you found that works? I’m always looking for ways to improve our process.

