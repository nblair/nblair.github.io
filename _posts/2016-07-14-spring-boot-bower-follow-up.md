---
layout: post
title: Follow up to 'Combining Spring Boot with Bower dependencies'
tags: 
- spring
- boot
- java
- maven
- bower
- javascript
---

In this post I'll revisit a previous post titled [Combining Spring Boot with Bower dependencies]({% post_url 2016-04-21-combining-spring-boot-bower %}). After working for a few months with the bifurcated project structure described in that post, we ran into more trouble than we expected.

**Tl;DR: Don't split the repositories if you don't have to. Use one repository, and have gradle invoke `bower install`. Also support running `npm start`/`bower install` in src/main/resources.**

## What we thought we'd get

We thought that by splitting the repositories, two different teams with different skill sets could work in isolation on the same target deliverable. Each team could run their relevant sections of code with their preferred stacks, and we'd lean on [some Maven tricks](https://github.com/nblair/spring-boot-plus-front-end/blob/master/pom.xml#L96) with our Continuous Integration environment to stitch it all together nicely.

## What we ended up getting

With two different repositories on different stacks, we spent a lot of time diagnosing local development issues. With the back end project, we had Spring Boot's embedded Tomcat. On the front end, we had Node. Both parts needed things like URL rewriting, redirects, and 404/500 error pages. The configuration for those types of things are container specific, and the configuration we had to develop for the front-end was only applicable to development, and not deployed environments.

Additionally, when we started, all features in the site were available to the anonymous user - no authentication was required. A REST API was then developed that required authentication. The back end had [uw-spring-security]({% post_url 2016-03-03-uw-spring-security %}) for this, the front-end repository had no equivalent. 

We then wanted a log in user experience, and features that toggled on/off if you were logged in. Without authentication in the front-end repository, developing those features was a guessing game. Open a pull request with what you thought would work, get it merged, and wait until it was deployed to the beta environment to see if it worked. No good at all for productivity to have to wait to ship code to see if it works.

Lastly, in the deployed environment, we had to marry the URL rewriting and redirecting needs of the front-end project in to the embedded Tomcat for Spring Boot. [UrlRewriteFilter](http://tuckey.org/urlrewrite/) is actually amazing for this, but it was a piece of code that didn't exist in dev, only in beta and production. Troubleshooting it was tricky, because you could only troubleshoot on some remote server.

## What we did instead

The writing was on the wall: we had to consolidate the front-end and back-end repositories. 
The separation was good in theory, but it created too much friction in development. Having to wait to deploy your code to a remote server to see if it gets the job done is no way to work; we need to go back to being able to run the whole application locally for some tasks.

Mechanically, consolidation was a matter of copying the site content from the front-end repository into src/main/resources/static in the back-end repository.

We then needed gradle's build to invoke `bower install`. The best Google search result for 'gradle bower install' turned out to be [craigburke's bower installer gradle plugin](https://github.com/craigburke/bower-installer-gradle), here's the relevant section of our build.gradle:

<script src="https://gist.github.com/nblair/9ffb8f1709bb4ccf1f0f148639d87d57.js"></script>

We're back to having something buildable, but what about the front-end team? They don't want to run `gradle bootRun` if they don't have to.

We have *slightly* different startup instructions for front-end developers. After cloning the repository:

1. cd src/main/resources
2. npm start

In src/main/resources, we have the same package.json, superstatic.json and bower.json that we had in the separate repository. We had to add .bowerrc to src/main/resources with the following contents:

```
{ "directory": "static/bower_components" }
```

Now when one invokes `gradle bowerInstall` at the root, or if they run `bower install` within src/main/resources, the contents of src/main/resources/static/bower_components are equally valid. They don't match exactly, because the gradle plugin doesn't fetch the entire repository like true bower does, but the bits we depend on are in the exact same directory hierarchy.

This is working much better. Now a front-end developer has the choice:

* Run lean with `npm start` and not have to worry about the full gradle build. The default proxy configuration points to the beta environment, so they always have a REST API available with live data. Make changes to markup, styles, and not have to restart the container.
* If working on something that does require tigher integration with the back end, then the full `gradle bootRun` is an option to run everything, together, locally.

The one drawback is having to maintain bower dependencies in two places. The project doesn't experience a lot of volatility in front-end dependencies right now, so we're living with it.

## Addendum: alternative to bower-installer-gradle plugin

One alternative to using the [bower-installer-gradle plugin](https://github.com/craigburke/bower-installer-gradle) is to include something like this in your `build.gradle`:

```
task bowerInstallManual(type:Exec) {
    workingDir 'src/main/resources/'
    commandLine 'bower', 'install'
}

build.dependsOn bowerInstallManual
build.mustRunAfter bowerInstallManual
```

This works well, but it does have the side effect of requiring that the back-end developer have bower installed.


