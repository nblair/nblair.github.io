---
layout: post
title: Integrated Applications Activity, March 2016 - October 2016
---

This is a report I produce every 6 months to take a look at the activity of my team. For additional background, see [the first article on Visualizing Collaborative Development]({% post_url 2015-11-04-visualizing-collaboration %}).

Here is the output from [Gource](http://gource.io) on the combined activity across 178(!) projects between March 2016 and October 2016:

<iframe width="500" height="375" src="https://www.youtube.com/embed/awCe5XXN6Bg" frameborder="0" allowfullscreen></iframe>

## Notes

* Our group name changed during this period from *Internal Applications* to *Integrated Applications*.
* Techstore (PHP) and Enrollment Tools (Java) have the deepest trees. The teams working on these projects haven't changed any members over the last period, however their members do still contribute to other projects.
* The Techstore project achieved a significant milestone during this time period: they are committed to deploying Magento 2.x and eliminated all Magento 1 assets from the project. This is demonstrated at the 39 second mark in the video, with a good portion of that tree disappearing.
* The center point of the video is quite shallow has the highest diversity of participants. The projects represented in this code are shared libraries and smaller applications.
* Our project count is up to almost 180 different repositories. A casual invocation of [cloc](http://cloc.sourceforge.net/) on the work directory used to produce this video suggests there are over 2.3 million lines of code in total:

![Screenshot of cloc output]({{ BASE_PATH }}/public/images/oct-2016/cloc.png)

And yes - the COBOL code is really there and actively maintained!

## Takeaways

* There are many participants shown in the video that are not actual members of Integrated Applications, but rather are members of peer groups both within DoIT and other campus groups. This is a great thing.
* There is a lot more PHP in the portfolio than I expected. There are less than 10 PHP projects in the portfolio, but what projects we do have in the technology are very large.
* I did exclude one git repository from the cloc output as it contained a full wordpress install (including it pushed the total to over 3 million, with twice the lines of PHP). 


The source material used to create this video can be found at [https://github.com/nblair/internal-apps-visualization](https://github.com/nblair/internal-apps-visualization).

