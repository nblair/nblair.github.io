---
layout: post
title: Peloton
tags: 
- agile
- scrum
- collaboration
---

Premise: describe state:

* More projects than staff members.
* Heterogeneous portfolio. Different stakeholders, different audiences, different runtime platforms.

Challenge:

* Share things between these sites. 
* Don't Repeat Yourself. 
* Efficiency and productivity

### Option 1: Just react to what the customers are asking for, when they ask for it

As you can imagine, this really doesn't work very well. It's pretty much like this:

![Fire!]({{ BASE_PATH }}/public/images/peloton/fire.gif)

We tried this for a while and learned pretty quickly we couldn't get very far. 

### Option 2: Divvy it up

"Jim you take Project A and Project B. Jane you take Project C and Project D. Sam you take Project E...."

To be honest, we haven't tried this one in my current group. I have had similar work experience to this, and it's got a few drawbacks.
  
**There's no sense of teamwork.** Sure, we may share the same space, and see each other at the monthly group function or annual Christmas party, but
we don't get to _truly_ collaborate.

When one member leaves, there's a terrible scramble:

"Jane's leaving, so Jim you take Project D and Sam you take Project C. Her last day is Friday, so get as much in as you can."

What if Project C is 100,000+ lines of code (or [~3 Anna Kareninas]({% post_url 2015-12-02-visualizing-development-2 %}))? There is **no way**
Sam is going to be able to gain enough understanding of that product in a couple of business days.

It takes months even for small projects, years, and maybe even never for staff to recover from a turnover event like this.

Even if you have low turnover, another critical factor can hurt your productivity.

Every project has a blend of all sorts of different technology.
HTML, JavaScript, CSS, AngularJS, Less, Sass, Grunt, Gulp, Bower, Node, Maven, Gradle, Java, Groovy, Go, PHP, XML, XSLT...

Every developer has unique skillset. We all aren't masters of every possible language we need to use. 
Jim may be an absolute wizard in front-end technologies, but struggles with dynamic languages like Java and PHP. Jane is 
absolutely amazing with Groovy, but struggles with JavaScript.
 
There will be no coherence between the projects that these individuals create. 

### Option 3: Peloton

Here's what we want:

* Leverage each team members unique specialties on every project.
* Even if we can't get the stakeholders to align, align the technology we use in our solutions.

We like Agile and Scrum. We can't get all 12 people working on one project; the other 20+ projects would starve for too long.
We can however, break up into a few teams of 3 or 4. 

I work with an avid bicyclist who follows road bicycle racing, and he immediately suggested a term:

https://en.wikipedia.org/wiki/Peloton

"Riding in the main field, or peloton, can save as much as 40% of the energy employed in forward motion when compared to riding alone."

Paceline: Small, focused groups moving together. Rotating leader/scrum master.

3 pacelines operating independently. Each one focusing on specific product in the portfolio. Schedule Wed through following Friday (8 business day sprint).
 
Before the sprint starts, "support and gear" team meets, clearly defines a goal and a "definition of done" for each paceline's sprint.

Example Schedule:

```
Paceline Cardinal:
+ E-Commerce Project
* Goal: First sprint, demonstrate working software.
* Definition of Done: Site deployed to test environment and accessible to product team

Paceline White:
+ STAR Project
* Goal: Fix bugs recorded from prior QA effort.
* Definition of Done: Maven release 2.0.x including fixes deployed through QA environment.

Paceline Green:
+ NetID Account Recovery 
* Goal: Add Google Analytics and enhance feature X.
* Definition of Done: 1.2.x release including changes deployed through QA environment.
```

"Support and Gear"
https://en.wikipedia.org/wiki/Road_bicycle_racing#Gruppetto

### Summary

One thing that I think is important to keep into perspective is that with the working relationship we have, Project A isn't *Jim's* and Project E isn't *Sam's*,
those projects are the **employer's**. It's Jim and Sam's duty to make sure the employer is equipped not just to have a quality
product right this minute, but ongoing.

