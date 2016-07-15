---
layout: post
title: Automating Release Management with Gradle and Jenkins
tags: 
- gradle
- jenkins
- java
- maven
- release management
- lifecycle
---

In this post I'll describe a practical approach to automatically publishing releases of your Gradle managed project using some custom tasks and a Jenkins plugin.

*This post was updated on July 15, 2016, edits are inline.* 

## Background

I have a long history and extensive experience with [Maven](http://maven.apache.org). I feel really strongly that [release management is incredibly important and powerful for a project]({% post_url 2015-10-09-release-management %}), and a lot of our related processes depend on the [Maven Release Plugin](https://maven.apache.org/maven-release/maven-release-plugin/).

We are working Gradle more and more into our projects. There are still a few Maven plugins and constructs I pine for, but making headway in finding equivalents, sometimes superior.

Here are a couple of user stories describing what we desired:

```
As a contributor to a software project
I want the continuous integration job testing my contribution to remind me to increment the version
so that I don't forget 
and artifact release processes work correctly.
```

```
As a custodian for a software project managed with Gradle
I want contributors to the project to be required to increment the project version
so that I can remove the overhead of tracking 'fix versions' down after the fact 
and I can see immediately the impact of the change, be it patch, minor feature, or major refactor.
```

Basically, we want to implement [Semantic Versioning](http://semver.org), but we don't want to add a whole ton of human labor to make it happen. We have Jenkins for Continuous Integration, let's figure out a way to take the repetitive tasks of incrementing the version, tagging, uploading artifacts to Nexus, and a bit of rule enforcement.

Google searches for "gradle equivalent of the maven release plugin" don't really turn up a clear winner. As I use Gradle more, I think that's the point - they leave a lot of things to your own creativity.

## Idea

Since we don't have a clear plugin to just start using, our idea started like this:

* 2 CI Jobs in Jenkins
  * Job 1: pull request builder. Our institution uses gitlab, so we are using [Gitlab Merge Request Builder Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Gitlab+Merge+Request+Builder+Plugin).
     * The Jenkins 'Merge before build' step MUST come first. ~~We won't get a chance to peek at the `version` property in build.gradle before merging the contribution.~~
     * In order to get the 'last released version', ~~let's use a tracked text file (Java Properties file format) in the repository.~~ we can execute `git describe`.
     * The gradle command we want the builder to invoke will be `gradle clean confirmProjectVersionIncremented test`. confirmProjectVersionIncremented doesn't exist, we'll write a task for that.
  * Job 2: poll the master branch of the primary repo, uploadArchives when commits land.
     * This will fire essentially after a pull request is merged, which means we'll have a new version in build.gradle.
     * First, run `gradle clean bootRepackage uploadArchives` to ship our jar to Nexus. 
     * Then use the Git Publisher post-build step to ~~push the commit,~~ tag and push the tag to the primary repository.

## Implementation

The tricks are these fragments, in your `build.gradle`:

<script src="https://gist.github.com/nblair/53f44e08104354dd078e6104d7dae4a8.js"></script>


## Summary

So far so good.

One drawback we've observed so far: **you will regularly need to rebase your branches if there is a lot of activity happening in the project**. If development is coming in at a trickle, one at a time, nothing iss going to land on master in between you branching and submitting your pull request, no conflicts. But if others are merging at the same time, it's likely the commit from the job on the master branch that advances the version in build.gradle is going to conflict with your branch. 

## Update July 15, 2016

A recent edit to the gist shows a simpler way to calculate the previous version using `git describe`. This is a big improvement, we don't have to have that dance around the project.last file.

The [gradle-git plugin](https://github.com/ajoberstar/gradle-git) lets us embed git instead of having to shell out, and a [Java implementation of the Semantic Versioning spec](https://github.com/zafarkhaja/jsemver) gives us an easier way to do the math comparing version numbers. 
