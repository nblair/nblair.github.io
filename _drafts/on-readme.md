---
layout: post
title: On "README"
---

I'll admit publicly to being in the camp that code is *not* self-documenting. The problem of self-documenting-ness is made further complex by the nature of software projects, which are a big pile not-self-documenting things compiled, transpiled, minified, and assembled by yet more not-self-documenting things. 

Whoever coined the name README and used it enough for it to become a permanent fixture in engineering lexicon is a genius. Right there, in the imperative form, a name for a file that immediately expresses its importance: **READ. ME.** Irresistible.

That modern version control (git) providers recognize this important file automatically and even render it nicely gives a software project an easy way to define itself.

But what should it contain? Imagine your project going through an existential crisis. Why am I here? How can I be useful? What keeps me going?

Here's some ideas:

1. Start with a two or three sentence abstract of the scope of the project. This is important; if you find you can't easily express the scope concisely, means you may have a product focus problem to work on first. Here are some examples:
  * This project is a client library for Java that integrates with the foo REST API. It uses blah...
  * This project is a microservice for managing ferrets. It uses Python and blah to present...
2. If the project is hosted somewhere as a service, here's a good spot to link to it. If the project produces a library that I can include, provide **a)** the dependency management mechanism I can use (Maven, Gradle, Ivy, Composer, bower, npm, whatever) and **b)** a concise code sample of how I might first use it within my applications.
3. Developer requirements: what should I, your fellow engineer, have installed or configured to run this software quickly and painlessly?
4. A single command to run the thing (more on this later).
5. How to contribute. This matter is important whether or not your project is internal to your company or publicly available and open source. How we software engineers use version control varies wildly:
   * "commit straight to master, who needs branches!"
   *  "never commit to master, only to develop first", 
   *  "fork the project, create a feature branch, and submit a pull request", 
   *  "squash branches before pull request", 
   *  "don't squash branches before pull request
  
This is enough to capture your viewers limited attention and get them rolling. Assume that potential consumers of your product have very little time. If there are lots of obstacles to adoption, the consumer is more likely to move on to the next option, unless the need is high or your solution is that unique.

This README is every bit as important as the deliverable bits of your end product. The README has a typo? That's a bug. A developer requirement was updated and the README references the wrong version? That's a bug. Care for it just like you would any other piece of source. Your return on investment will be:

* Clear and consistent focus. You can't forget why the project exists since it's front and center.
* Help. When others can find your project and understand what it does, you'll attract more consumers and contributors.

