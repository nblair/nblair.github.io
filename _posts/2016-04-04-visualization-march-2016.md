---
layout: post
title: Internal Applications Activity, October 2015 - March 2016
---

This is a report I produce every 6 months to take a look at the activity of my team. For additional background, see [my previous article on Visualizing Collaborative Development]({% post_url 2015-11-04-visualizing-collaboration %}) .

Here is the output from [Gource](http://gource.io) on the combined activity across 119(!) projects between October 2015 and March 2016:

<iframe width="500" height="375" src="https://www.youtube.com/embed/a6-lf1KxLzg" frameborder="0" allowfullscreen></iframe>

### Areas of Interest

* You will notice there are 2 distinct areas of activity; at start, and quickly moved to the right, is the existing Internal Applications team. At about the 5 second mark, you can see a hub of activity drift to the left. That activity is that of our sibling group, the Java section of the WaMS (Web and Mobile Solutions) team. Our teams are joining forces. 
* Much of the work done by the WaMS-Java team focuses on projects related to [Curricular and Academic Operational Data Store (CAOS)](https://wiki.doit.wisc.edu/confluence/pages/viewpage.action?pageId=47562009). Gource only shows the files that are touched in the time period. The longer lines present in projects there indicate deeper nesting of filesystem projects, which is a sign these projects are *quite large in size.*
* In the middle of October, wisclist-custom-interim was added, but has had no activity since that date.
* Knowledgebase continues to be isolated (lower right, 15 second mark onward). I have not yet succeeded in getting the priority and resources to help in that large project. Continues to have lots of volatility.
* At the end of November (20 second mark), the assets for a legacy project named Itemmaster were imported (shown in purple).
* In the middle of December (30 second mark), the assets from the site financial.doit.wisc.edu were imported (also in purple). 
* In early February, 2016 (1:00 mark in the video) we bootstrapped the new techstore project by importing a customized fork of [magescotch](https://github.com/joshuaswarren/magescotch), a development environment using [Scotchbox](https://box.scotch.io/) for Magento 1.9 and 2.0 development. 
* In early March (1:10 mark in the video), we upgraded the Magento 2.0 tree in the techstore project from a pre-release version to the (at the time) latest 2.0.2 release. There have been 2 patch releases since we have yet to apply.


### My Takeaways

* There is a noticeable higher velocity in this video over the last period. Compare the first 15 seconds of this to the [original video]({% post_url 2015-11-04-visualizing-collaboration %}). **This is really encouraging to me for a number of reasons:**
  * Our developers are becoming savvier not just with git but the overall workflow; pull requests, branching, merging, rebasing. Not that they weren't already, but less [learning of the tool](https://xkcd.com/1597/) and more just using of the tool is apparent.
  * Units of work are generally smaller in size and more frequent. We are breaking up work better and avoiding the huge contributions of code that are hard to understand. 
* I intentionally segregated the projects of the WaMS team this time, since our merger is in progress. I can't wait to see what working together over the next six months will be.
* While these visualizations are really good at showing the human collaboration connections, I think they are lacking in showing how these projects actually relate to one another. The connections between projects - how they radiate out from the center - is superficial and a side effect of how gource works and my approach to getting gource to run on the history of multiple projects. **[I'm craving something that actually shows compile/runtime/deployment dependencies across the portfolio.](https://github.com/nblair/nblair.github.io/issues/3)**

I originally intended to produce this annually, but 6 months appears to be a better mid-point to reflect. The source material used to create this video can be found at [https://github.com/nblair/internal-apps-visualization](https://github.com/nblair/internal-apps-visualization).