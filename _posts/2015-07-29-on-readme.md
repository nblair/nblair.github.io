---
layout: post
title: On "README"
---

I'll admit publicly to being in the camp that code is *not* self-documenting. The problem of self-documenting-ness is made further complex by the nature of software projects, which are a big pile not-self-documenting things compiled, transpiled, minified, and assembled by yet more not-self-documenting things. 

Whoever coined the name README and used it enough for it to become a permanent fixture in engineering lexicon is a genius. Right there, in the imperative form, a name for a file that immediately expresses what to do: **READ. ME.** Irresistible.

That contemporary version control (git) providers recognize this important file automatically and even render it nicely gives a software project an easy way to define itself.

But what should it contain? Imagine your project going through an existential crisis. Why am I here? How can I be useful? What keeps me going?

Here's some suggestions:

1. Start with a two or three sentence abstract of the scope of the project. **This is important**: if you find you can't easily express the scope concisely, means you may have a product focus problem to work on first. Here are some examples:
  * This project is a client library for Java that integrates with the foo REST API. It uses blah...
  * This project is a microservice for managing ferrets. It uses Python and blah to present...
2. If the project produces a library that I can include in my projects, provide **a)** the dependency management mechanism I can use (Maven, Gradle, Ivy, Composer, bower, npm, whatever) and **b)** a concise code sample of how to start.
3. Developer requirements: what should I, your fellow engineer, have installed or configured to run this software quickly and painlessly?
4. A single command to run the thing (more on this later).
  
This is enough to capture your viewers limited attention and get them rolling. Assume that potential consumers of your product have very little time. If there are lots of obstacles to adoption, the consumer is more likely to move on to the next option, unless the need is high or your solution is that unique.

This README is every bit as important as the deliverable bits of your end product. The README has a typo? That's a bug. A developer requirement was updated and the README references the wrong version? That's a bug. Care for it just like you would any other piece of source in the project. Your return on investment:

* Clear and consistent focus. You can't forget why the project exists since it's front and center.
* A path to return to the project after a long time. Dusting off an old project is easier when you can quickly reconstruct how to use it.
* Help. When others can find your project and understand what it does, you'll have a better chance to attract more consumers, and if you're lucky, contributors.  


