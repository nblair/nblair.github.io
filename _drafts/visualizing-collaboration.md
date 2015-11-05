---
layout: post
title: Visualizing Collaborative Software Development
---

I've been told that from a non-technical observer, [it's difficult to understand what software engineering is](http://www.bloomberg.com/graphics/2015-paul-ford-what-is-code/).

As the technical lead for a team of engineers, I'm regularly tasked with explaining or justifying the effort behind custom software development projects, from "small" to "large."

There are a [number](https://en.wikipedia.org/wiki/COCOMO) [of](https://en.wikipedia.org/wiki/Source_lines_of_code) [different](https://en.wikipedia.org/wiki/Cyclomatic_complexity) methodologies for describing the relative difference between a small and a large effort *in technical terms*, but those methods are lacking in clarity for a non-technical observer. 

Some projects have an incredibly large but simple feature set that can be provided with a relatively small code base. Other projects have a seemingly tiny feature set, but have enormous complexity and large amounts of code. To a non-technical observer, it can be difficult to comprehend how a seemingly "small" web site can require so much development effort to deliver.
 
### Visualizing Relative Scale

I've known about the existence of a tool called [Gource](http://gource.io) for some time, and experimented with it a few times. I had an idea recently to use Gource to demonstrate:

* relative size and complexity of the projects in our portfolio, and
* how our engineers work together on those projects

My hypothesis: a visual representation of the activity of the team may provide a non-technical observer a better understanding of the relative difference of effort between software projects.

Part of my team's current portfolio is made up of about two dozen different independent software projects, primarily web applications. Those applications are composed of different technologies and are different stages of the lifecycle, from green field through end of life.  

Here is the output from Gource on the combined activity for those two dozen projects between June 2014 and late October 2015:

<iframe width="420" height="315" src="https://www.youtube.com/embed/FVSVhKSBYrs" frameborder="0" allowfullscreen></iframe>

Description:

* Internal Applications team members are shown using their avatars. Non-team members are represented with the little chess piece icon.
* Department and division web sites my team regularly constructs are not included in this demonstration.
* Each path radiating from the center point represents a different project in our portfolio. 
* Each leaf dot is a file within the project, only file extensions are shown to show the diversity of languages and content we must produce.
* Each laser beam from an avatar to one or more dots represents an individuals contribution; each bug fix or feature we develop results in adding or removing 1 to thousands of files.
* On Monday, August 17, 2015 we imported a software project that has been maintained without verson control for more than a decade. The primary developer for that project transferred from another part of the organization to our team a few weeks prior.

### My Takeaways

* Collaborative development provides *enormous* value to a project, particularly as the project increases in size. Any engineer from our team - and even engineers outside of our team - should be able to contribute to any project in our portfolio. I'll write a future article on the technology [strategy](https://www.atlassian.com/git/tutorials/comparing-workflows/forking-workflow) and [tools](https://en.wikipedia.org/wiki/Continuous_delivery) we use to accomplish that. You can see each multiple different members contributing to each of the different projects and pathways.
* Our biggest challenge: our engineers are outnumbered by projects. This video could look completely different:
  * If each engineer worked solo on projects, their avatar would stick on top of one portion of the tree and never move. We would never have the benefit of different perspectives contributing across the portfolio, and we'd have difficulty in cross training or filling gaps if an employee leaves.
  * If we could focus the entirety of the team on single project for longer contiguous periods of time, you would see less dancing from project to project. [We are losing efficiency with all of this context switching](https://en.wikipedia.org/wiki/Peloton); I will write a future article on our team's ongoing method to address this.
* **August 17, 2015 is a watershed moment for our team.** As demonstrated by the video, the sheer size of that project is greater than all of our existing projects (in version control) combined. Simply switching from a single-developer to a collaborative model is no small feat for this project, it will take us significant time and effort to transition that project.

I hope to re-visit this technique on a yearly basis. 

