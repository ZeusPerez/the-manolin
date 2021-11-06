---
title: "Code challenge: My tips"
published: true
---

In the past years, I had the chance to get involved in recruiting processes as a reviewer for the code challenge and interviewer. This gave me a different perspective from when I'm on the candidate side, taught me a lot of things, and make me improve significantly my code challenges. In this post, I will go over some of the points I usually have in mind when I make the challenge and when I review it.

Not all of the following points apply to all the code challenges. It depends a lot on what the challenge consists of. It is very different if you have to develop a server, a client, or an application that has no interaction with the network. Also, sometimes the time is limited for delivering the resolution, or we don't want to iterate endlessly for delivering the challenge close to perfect (because perfect is the enemy of good), but it is worth having these points in mind and, if you don't think it is worth to implement some, you can mention it in the `readme` or in the interview for defending the solution if there is one as part of the process.

Note that some of the links and tools I mention are related to Golang, but this should be similar to other languages.
<br/><br/>

## Read carefully
This can be obvious but sometimes we're so eager to start coding that we miss important details in the problem description. Try to understand the use cases, the happy path, and the corner cases. If possible, extract some important test cases from the reading that ensure that you match the requirements and the solution is valid. Keep in mind all the instructions given and make sure that you meet them: how to deliver the solution, content, etc.

Another important point is the documentation attached. For example, if you have to use an API and the documentation and the access is provided, it is important that you read it carefully and test the API in order to confirm what is its behavior.

If there is any doubt after reading it, you should assess if you can make any assumption about it and justify your solution according to that assumption or if you need to address this question to the recruiter or the person that send you the code challenge.
<br/><br/>

## Production readiness
We know that the challenge won't run in production, and we did not invest enough time to have something good enough for production readiness with intensive use, but some points are easy to care about in a small solution. For example:

- Unused code or comments: review your code to ensure there is no dead code or confusing comments.
- Logs: add logs with the proper level.
- Errors: handle the errors properly and return useful messages.

Although there are many other points to improve the production readiness with low effort, I think these are the most common to fail and require low effort to make it right.
<br/><br/>

## Input validation and error handling
This point is related to the previous one. If the code challenge receives input in any form (API, standard input, files, etc.) it should have some kind of validation. How extensive it depends on your criteria and the problem requirements. In some cases, it is almost impossible to cover all the cases. For example, if the input is text, you can be validating endless if you want to control every possible encoding or symbol. In these cases, it may make sense to try to open/process with the expected encoding and return an error otherwise.

Regarding the error handling, you should be doing it as you do it for any production system:
- Handle exceptions and errors to fail gratefully returning the proper error with the proper message instead of panic.
- Define custom errors when it makes sense.
- Log the errors at the right level (and report if necessary).
- Fail fast.

Antoher important point that can give you points and is a good practise is **shutdown gratefully**. If you have to use databases or any other dependencies in which the order in the shutdown can generate errors or incorrect closed connection it makes sense to implement a shutdown mechanism that allows to handle an shutdown signal like SIGINT, SIGKILL or SIGTERM properly. [This post](https://medium.com/honestbee-tw-engineer/gracefully-shutdown-in-go-http-server-5f5e6b83da5a) explain how it can be done in Golang as an example.
<br/><br/>

## Design of the solution
During the resolution of the challenge, we will need to make a lot of decisions and make some assumptions. I think that a very important point is to document all of these decisions and assumptions and justify them in the readme. Maybe this is one of the most difficult parts of the challenge and if you succeed in making the reviewer understand why did you design the solution in that way it can be a favorable point in the code review.

When thinking about the design sometimes we tend to guess the future and we make incorrect abstractions or do overengineer. I would be cautious regarding this point. I would make some duplication over an incorrect abstraction and make something simple that solves the current problem instead of something complex that solves problems that the description does not specify.

Apart from that, I would try to follow the SOLID principles and try to pay attention to some points that make the exercise looks better without much effort:
- Create classes and methods that have sense.
- Try to inject the dependencies.
- Parameterize when it is possible.
- Add constants or config files instead of hardcoding in the code.
- Care about configurations: allow configurations HTTP clients, database connectors, etc.
- Etc.

Another important point here is to use the right types. Don't use an `int64` if an `int32` is enough. A very common mistake here is with the specific case of handling money. Many times, people forget (or don't know) about [precision problems](https://stackoverflow.com/questions/3730019/why-not-use-double-or-float-to-represent-currency) with `float` and use a `float` instead of `big decimal` for money amounts.

<br/><br/>

## Performance
In a lot of code challenges, you would need to keep an eye on time and memory consumption and you usually will need to sacrifice one in favor of the other. Each time you need to use an algorithm for search or sort or to use non-basic data structures, keep in mind the know solutions for the known problems and justify the election of one algorithm or data structure versus another. I usually prioritize time complexity versus memory complexity because the reviewers want to see efficiency and memory is much cheaper than CPU so it is easier to scale by memory, but it depends a lot on the problem given. If you have enough time and you want to justify your decisions with data, you could add some benchmarks to your solution.

There are some specific problems when you need to be especially aware of the memory and it's when you have to handle very high files or inputs. In these cases, you will need to find strategies that allow you to read in chunks to avoid loading everything in memory if it is possible. You will usually find buffered readers in almost any language for facing this kind of problem.

Another important point to keep in mind when you develop the solution is **concurrency**. This depends a lot on the language and the problem. If it doesn't make sense, don't add it. It can seem obvious but you may want to demonstrate that you know how to handle concurrency but adding it without needing it is not a good choice. If you need it, keep in mind that it would add more complexity to the solution and you will need to deal with mutual exclusion access, race conditions, add racy tests, etc...

<br/><br/>

## Storage
Sometimes you will be asked to persist some information for the challenge, and most times, you don't have any requirement about this. Of course, if the problem description specifies how to add the persistence layer, you don't need to make this choice, just decide how to implement it. But if there isn't anything specified, you will need to make a decision here. I think there are two main choices in this case:
- In memory: this option will be the simpler one and the easiest to implement, but it has some drawbacks: it doesn't work for distributed systems, is limited to the memory of the service and share the resources with it, you need to implement mutex manually because it doesn't provide transactions, etc.
- External: you can add a small DB in this case, for example, an SQLite, but it is more work than the previous point. The integration is more complex, and you will need to add mocks for the unit tests and integration tests. You will need to choose between SQL and NoSQL too, and with the size of the problem given, it is common that you don't have enough information for deciding with cause. As a reviewer, sometimes the argument from the candidate was that she has more experience with that DB. When there isn't enough information, at least for me, it is a valid point.

<br/><br/>

## Security
I've never had to take a lot of care about the security in the code challenges that I've made. However, it is possible that you need to add some kind of authentication or authorization. A simple token in the header could be a solution it the problem it is not demanding in terms of security. If it is, you will need to take care of this security layer.

Additionally, there are code linters and static analysis tools that provide some security checking. Maybe it is not very exhaustive, but they are easy to add and it could be a quick win.

Another point, that could be obvious, but I've seen and I've done in the past, especially when I was hurried, is to hardcode secrets in the code. You can always move it to a config or credentials file and isolate it from the code and I would explain in the readme that you should not upload it to the repository.
<br/><br/>

## Testing
For testing, I would follow the [testing pyramid](https://martinfowler.com/articles/practical-test-pyramid.html): good coverage of unit tests, some integration tests and a lower amount of end to end tests.

**Unit tests:** I would follow the [AAA](https://medium.com/@pjbgf/title-testing-code-ocd-and-the-aaa-pattern-df453975ab80) and [F.I.R.S.T.](https://medium.com/@tasdikrahman/f-i-r-s-t-principles-of-testing-1a497acda8d6) principles for this and I would try to get a coverage closer to 100% with tests that make sense, not adding them just for gaining coverage. Also, I think you can add a coverage report and if are some parts are not tested, I would justify it in the `readme`.

**Integration and acceptance tests:** this doesn't apply to all code challenges, but in the cases where you have to create a server with a DB or similar, I think they provide more value to your solution. Of course, these are very expensive in terms of time because you will need to set the environment and run all the dependencies, usually with `docker-compose` and `cucumber`, [`acceptadora`](https://medium.com/cabify-product/acceptance-testing-go-services-using-aceptadora-428254c34d56) or similar for running them, which increases significantly the time you have to dedicate to finish your solution.

If you want to every exhaustive, you can also add more types of tests like:
- [Racy tests](https://golang.org/doc/articles/race_detector): if you have added concurrency to your solution, you can add some tests for testing race conditions.
- Benchmarking: if you want to provide a way for testing the performance of your system and provide some performance information.
- Live test: in some cases, you will need to connect with a working and external API or system. For these cases, I would add a live test, which will be executed manually and outside your test suite, just for confirming that your code works fine with the external system provided.
<br/><br/>

## Resiliency
The more affordable point here is the timeouts. Add timeouts and make them configurable in your dependencies. This is easy to tackle and gives good impressions.

Additionally, some other mechanisms are relatively easy to implement but they usually are outside the scope of a code challenge:
- Circuit breakers: it is a good practice to stop hitting any dependency if it is not available. There are a lot of third-party implementations but it is not difficult to implement your own. A simpler alternative to this can be using
 [client-side throttling](https://sre.google/sre-book/handling-overload/).
- Retriers: sometimes you would like to retry momentary errors. Again, like the previous point, there are a lot of open-source libraries for this but implementing a basic behavior with linear and/or backoff retrying is not very difficult. [Exponential backoff with jitter](https://aws.amazon.com/es/blogs/architecture/exponential-backoff-and-jitter/) can be a great option for a code challenge.
- Rate limiters: if the code challenge consists in implementing something on the server-side, this is a good mechanism for gaining resiliency.
- Another backpressure mechanism: you can get some ideas from [this post](https://medium.com/@jayphelps/backpressure-explained-the-flow-of-data-through-software-2350b3e77ce7) to add extra resiliency to your code.

You can find examples of how to apply these concepts in the following links: [on a server](https://ieftimov.com/post/make-resilient-golang-net-http-servers-using-timeouts-deadlines-context-cancellation/) and [a client](https://medium.com/@_jesus_rafael/making-http-client-more-resilient-in-go-d24c66a64bd1).

<br/><br/>

## Docker
This applies especially if you have to build a server, but I think it is useful for the rest of the code challenges too. If you add a `Dockerfile` that allows the reviewer to execute the code, you can avoid any compatibility issues on different local machines. Moreover, you are showing that you are familiar with `Docker` which usually does you good.
<br/><br/>

## Makefile
This is something easy to add, that gives a good impression and helps the execution of the code. You don't need to build something super complex, just a few commands will provide value: `help`, `test`, `build`, `run`,  `lint`, `run-in-docker`, etc.

Also, if you are adding some CI to your code, for example, a simple `travis` or `circleci` execution, you can make use of this `Makefile` within.
<br/><br/>

## Code style
Try to follow the code style and add a mechanism for ensuring you did it. For example, you can add a script or step in the makefile that makes a check for the style in the principal static analysis tool of the language you used for the challenge: `rubocop` and `reek`, `pycodestyle`,  `golangci-lint`, etc.
<br/><br/>

## Third party libraries
It is okay to use third-party libraries for some behavior that is not part of the requested functionality, like log libraries or database connectors.  However, if it is something related to the main goal of the exercise, I would avoid using them. For example, if part of the exercise consists on create a cache for a client, it doesn't make sense to use a third-party package that implements that for you.

It is good practice too to justify the use of these third-party libraries in your `readme`.
<br/><br/>

## Code version control
If you have to upload your exercise to a repository using any version control, care about the frequency, name, and description of the commits. Lots of people, including me, make the code revision commit by commit, so they must make sense and help the reviewer to understand the evolution of the exercise during its implementation.
<br/><br/>

## Documentation
Take care of your comments. Don't abuse them or miss them if they are necessary. I would follow the [usual best practices](https://stackoverflow.blog/2021/07/05/best-practices-for-writing-code-comments/) that do you in your daily work.

Another option here is adding automatic documentation for your code. For example, in Golang there is [`godoc`](https://go.dev/blog/godoc) natively and if we are building API we can use any tool for autogenerating documentation with good visuals like swagger. This last point can be time-consuming and sometimes can add noise to the code because it requires a lot of comments so if the code won't have many users, which is probably that it will only be used/read by the reviewer, it may make sense to mention that it could be nice to have in the future instead of implementing it. It depends on you and the nature of the code challenge.
<br/><br/>

## README
A lot of people don't pay much attention to this part, but I think it is a key point. It is the point where most of the reviewers will start, and it could be a presentation letter for the rest of your solution. It doesn't mean that you can add padding information or not be concise, but you can explain your motivations, decisions, assumptions, and tradeoffs you thought about.

There are some points that I (almost) always add to the `readme`:
- How to run it: explain all the dependencies needed, the commands for compile, test, and run, with and without `make` commands, with or without containers, etc.
- Testing: I like a lot the testing and I usually try to explain why I tested some parts and what parts I would like to test more. As I usually add different types of tests, I explain the purpose of all of them.
- Structure and/or code design: in this section, I explain how and why I structured the code in that way and all the design decisions I took, and why.
- Decisions, assumptions, and trade-offs: depending on the size of the exercise and the number of decisions, I add a specific point for this, or I include it in the previous point. Here I try to explain any not obvious decisions and assumptions and why I took them over another alternative.
- BUGs: sometimes there are some points that you did not handle in the solution and can cause bugs. For example, the aforementioned problem of encoding in text processing, is usually difficult to cover completely. Other common examples could be huge files, huge load, size constraint in fields and structs, etc.
- Future interactions: I will always propose improvements that I would implement in the future. Although, as always, this depends a lot on the code challenge, some points are wildcards for many of them:
  - Observability: in production code, you would add more observability and reporting metrics to the proper platform, but it is usually outside the scope of the code challenge.
  - CI/CD: in a code challenge it does not make sense to implement continuous delivery, although if you are using a repo you can provide some continuous integration. At this point, you can explain how you would deliver this code. For example, if you are implementing a client or library you can explain that you would add code for publishing it or if you are implementing a server, for deploying it in the cloud.
  - Distributed systems: you usually design the code challenge to run in a single node but you can mention what you will need for making it distributed. For example, if you chose in-memory storage, you can mention that this won't work in distributed systems and you would need an external database.
  - BUGs: if you added a bugs section in the readme, you can also mention how you will fix them or at least prevent them, for example, adding test cases for all the possible malfunctioning scenarios.


Additionally, you could add all the information that you think is valuable for the reviewer.
<br/><br/>

## Final considerations
During this post, I've covered too many points to include in your test challenge, but this is just something to keep in mind. It is difficult that all of them can be applied to a code challenge. You don't need to implement them, some of them can be ignored and some of them can be commented on in the `readme` and how you would apply them. I've mentioned it all over the post, but I would like to emphasize it again: these are very generic points, and **it depends to a large extent on the specific code challenge**. The purpose of this post is only to give ideas of how to improve your challenges, but there is not a silver bullet for always doing it well.

Usually, when you receive a code challenge, they mention the expected amount for resolving it. Unless you have a time constraint and, you need to do it in a specific amount of time, I would not take this reference too seriously. In my experience, I usually invest more than this expectation and I saw a lot of people taking more time too (although you always say that it took less).

I hope these tips help you to improve your code challenges and you find them useful when you are facing a new one.
