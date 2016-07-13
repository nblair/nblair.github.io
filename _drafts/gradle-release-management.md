---
layout: post
title: Practical Release Management with Gradle and Jenkins
tags: 
- gradle
- jenkins
- java
- maven
- release
- lifecycle
---

In this post I'll describe a practical approach to publishing releases of your Gradle managed project using some custom tasks and a Jenkins plugin.

## Background

I have a long history and extensive experience with [Maven](http://maven.apache.org). I feel really strongly that [release management is incredibly important and powerful for a project]({% post_url 2015-10-09-release-management %}), and a lot of our related processes depend on the [Maven Release Plugin](https://maven.apache.org/maven-release/maven-release-plugin/).

We are working Gradle more and more into our projects. There are still a few Maven plugins and constructs I pine for, but making headway in finding equivalents, sometimes superior.

Here are a couple of user stories describing what we desired:

```
As a custodian for a software project managed with Gradle
I want contributors to the project to be required to increment the project version
so that I can remove the overhead of tracking 'fix versions' down after the fact.
```

```
As a contributor to a software project
I want the continuous integration job testing my contribution to remind me to increment the version
so that I don't forget and later artifact storage processes work correctly.
```

Basically, we want to implement [Semantic Versioning](http://semver.org), but we don't want to add a whole ton of human labor to make it happen. We have Jenkins for Continuous Integration, let's figure out a way to take the repetitive tasks of incrementing the version, tagging, uploading artifacts to Nexus, and a bit of rule enforcement.

Google searches for "gradle equivalent of the maven release plugin" don't really turn up a clear winner. As I use Gradle more, I think that's the point - they leave a lot of things to your own creativity.

## Idea

Since we don't have a clear plugin to just start using, our idea started like this:

* 2 CI Jobs in Jenkins
  * Job 1: pull request builder. Our institution uses gitlab, so we are using [Gitlab Merge Request Builder Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Gitlab+Merge+Request+Builder+Plugin).
     * The Jenkins 'Merge before build' step MUST come first. We won't get a chance to peek at the `version` property in build.gradle before merging the contribution.
     * In order to get the 'last released version', let's use a tracked text file (Java Properties file format) in the repository.
     * The gradle command we want the builder to invoke will be `gradle clean confirmProjectVersionIncremented test`. confirmProjectVersionIncremented doesn't exist, we'll write a task for that.
  * Job 2: poll the master branch of the primary repo, uploadArchives when commits land.
     * This will fire essentially after a pull request is merged, which means we'll have a new version in build.gradle.
     * First `gradle clean updateProjectLast` to update our file tracking the 'last released version'. We need to write this task.
     * Second, commit the change to the file tracking the last released version.
     * Third, `gradle bootRepackage uploadArchives` to ship our jar to Nexus.
     * Use the Git Publisher post-build step to push the commit, tag, and push the tag to the primary repository.

## Implementation

Here is a simple gradle spring boot project with the necessary tasks added to build.gradle:

The tricks are these fragments:

```
/**
 * Task executed by Jenkins on Pull Requests to confirm the contribution as appropriately
 * incremented the gradle 'version' field above.
 */
task confirmProjectVersionIncremented() {
    doLast {
        Properties properties = new Properties();
        properties.load(new FileInputStream("project.last"))
        def lastVersion = properties.get("last.released.version").split('\\.')
        def currentVersion = getVersion().split('\\.')

        if ( (currentVersion[0] > lastVersion[0]) ||
                (currentVersion[0] == lastVersion[0] && currentVersion[1] > lastVersion[1]) ||
                (currentVersion[0] == lastVersion[0] && currentVersion[1] == lastVersion[1] && currentVersion[2] > lastVersion[2]) ) {
            // success, the project version is greater than 'last.released.version'
            println "Version test successful: proposed version is " + currentVersion.join('.') + " greater than last.released.version " + lastVersion.join('.')
        } else {
            throw new GradleException("version field in build.gradle with current value " + currentVersion.join('.') + " must be incremented past " + lastVersion.join('.'))
        }
    }
}
/**
 * Task executed by Jenkins on merges to master to confirm that we appropriately track the last
 * released version.
 */
task updateProjectLast() {
    doLast {
        Properties properties = new Properties();
        properties.load(new FileInputStream("project.last"))
        properties.setProperty("last.released.version", getVersion())
        properties.store(new FileOutputStream("project.last"), "this file is automatically managed, do not edit")
    }
}
```

The file `project.last` looks like this, and is tracked in git:

```
#this file is automatically managed, do not edit
#Thu Apr 28 10:20:36 CDT 2016
last.released.version=0.3.8
```

## Summary

So far so good.

One drawback: you will regularly need to rebase your branches if there is a lot of activity happening in the project. If development is coming in at a trickle, it's one at a time, nothings going to land on master in between you branching and submitting your pull request, no conflicts. But if others are merging at the same time, it's likely the commit from the job on the master branch that advances the version is going to conflict with your branch. 