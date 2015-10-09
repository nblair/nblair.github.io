---
layout: post
title: Release Management
---

The following are the slides from a talk I am giving on Friday, October 9, 2015:

https://docs.google.com/presentation/d/1l2-1ZXV75ryGG9qeThGou7ht6uHvVr4IG0J-oDmOhXk/edit?usp=sharing

The premise behind the talk is to describe why, and to some extent how, to include  "Software Releases" within your Continuous Delivery pipeline. A Software Release isn't the same thing as "deploying/shipping/rolling out" to any particular environment; those verbs are the action you perform on the deployable thing that the release creates.

I think releases are an important step for a few reasons:

* Releases are really easy to make and cost nothing. 
* [Releases have an identifier that expresses something meaningful to people beyond your developers.](http://semver.org)
* Releases make tags in your version control.
* Releases make deployable things.
* Releases are immutable.

All of these things give you **clarity**:

* What is in production right now?
* What was in production yesterday?
* What is the significance of today's release?

Releases also give you a *repeatable* way to deploy any milestone of your project; going back to any particular state is a lot easier, and doesn't depend on a single individual or a data recovery system.

A lot of this is covered in the presentation and the notes. What's not shown in the slide deck is the tour of my team's chosen tools to perform release management.

### Screenshot Tour

Here are some screenshots of JIRA (task tracker and project management), Bitbucket (git version control), and Sonatype (artifact repository)

Road Map for upcoming versions:

![Road Map for upcoming versions]({{ BASE_PATH }}/public/images/release-management/1-roadmap.png)

Summary for next release:

![Summary for next release]({{ BASE_PATH }}/public/images/release-management/2-upcoming.png)

In progress work:

![In progress work]({{ BASE_PATH }}/public/images/release-management/3-in-progress.png)

Release notes from a prior version:

![Release notes from a prior version]({{ BASE_PATH }}/public/images/release-management/4-prior-release-notes.png)

Tags in version control:

![Tags in version control]({{ BASE_PATH }}/public/images/release-management/5-scm-tags.png)

Deployable artifacts:

![Deployable artifacts]({{ BASE_PATH }}/public/images/release-management/6-deployable-artifact.png)

### Additional Resources 

* [Maven Release Plugin](http://maven.apache.org/maven-release/maven-release-plugin/)
* [A release plugin for gradle](https://github.com/researchgate/gradle-release)
* [A gist providing an analogue to the Maven Release process for PHP using Phing](https://gist.github.com/nblair/345692c79d673c7edee5)
