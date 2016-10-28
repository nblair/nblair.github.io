---
layout: post
title: Outages Design Review
tags: 
- gradle
- jenkins
- java
- pipeline
- continuous delivery
- release management
---

Here are slides from a design review I am presenting on Friday, October 28 2016:

[Outages Design Review](https://docs.google.com/presentation/d/1wgryzLwrWVKgVpmnoDz9P9UhSWwb_351wXnrS3wgfNA/edit?usp=sharing)

This presentation is being given at the biweekly "Application Design Review Brown Bags" series, which is a community of practice at UW-Madison that gives IT professionals from across the university a chance to collaborate and share best practices.

There are three choices that we made during the outages that I'd like to highlight here. 

### Persistence implementation under an interface

Our first choice for persistence didn't pan out. Since the initial persistence layer was guarded by an interface, we could quickly pivot to a different local persistence library in a really short amount of time with little disruption to the project. I expect switching to another implementation in the future to be just as straightforward.

Additionally - not removing the original persistence implementation when we added the new implementation was important. We could ship the release containing the new code without the necessary new configuration in place, and the application would run just fine. We could then experiment with the new configuration and easily rollback to the prior configuration without having to deploy a different release.

### Every contribution is a release

We have a Jenkins job configured to participate in pull requests. After attempting to merge the branch, before running the build, it runs a gradle task to check that the `version` field has been updated using a Gradle plugin described in the post ['Automating Release Management with Gradle and Jenkins']({% post_url 2016-07-13-gradle-release-management %}). If the version hasn't been updated, the build fails.

Once passing, as soon as a pull request is merged, a release of the product is prepared automatically (see ['Have Jenkins Tag your Releases']({% post_url 2016-10-17-jenkins-tag-releases %})) and sent to the test environment. Developers don't have to stop to prepare the release or think about manually deploying - it's just done automatically. 

### Product Owner access to deployment pipeline

Allowing the Product Owner the ability to trigger deployments has been very successful. It's often the case that by the time testing is complete and releases are scheduled, the development team has long since switched contexts to a different product in our portfolio. Having to steal capacity from another project's time to perform production deployments is problematic.

What we've done for outages using the [Jenkins Pipeline Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Pipeline+Plugin) really avoids that resource capacity issue. We do this with confidence too, as rolling back to any prior version is just as easy and accessible to the product owner as well.

These three choices stand out in my opinion as patterns to follow in other current and future projects.




