---
layout: post
title: Visualizing Development, Take 2
---

The following are the slides from a talk I am giving on Thursday, December 3, 2015:

[Visualizing Development](https://docs.google.com/presentation/d/1apvuR7xTVIPQ9G_kPRSN9Hk6t9eONV7m8MaDc2OT95s/edit?usp=sharing)

The motivation for the talk was a follow up to the [Visualizing Collaborative Software Development]({% post_url 2015-11-04-visualizing-collaboration %}) post and [related video](https://www.youtube.com/watch?v=FVSVhKSBYrs) I published previously.

Looking back at that original post, I think the video does a good job of showing collaboration, but I think it falls short in demonstrating the actual size of the assets we manage. Each of the dots is a file, but no dot is larger than any other. I don't think it's fair to show a 20 line file and a 10,000 line file as having the same size.

This new approach tries to relate the size of our projects' codebases to the novel [Anna Karenina](https://en.wikipedia.org/wiki/Anna_Karenina) by [Leo Tolstoy](https://en.wikipedia.org/wiki/Leo_Tolstoy). There are a lot of similarities to made between writing code and authoring a novel. I think we can all have a reference point of the mental effort and time required to read a novel. It's not a stretch to relate those same mental activities to reading code - either someone else's or even our own from a few months back. With contemporary sofware engineering tools and techniques, the scale of what an engineer needs to understand escalates exponentially. 

This talk's first audience includes the leadership of my current organization. My hope for presenting the talk is to recommend a few ideas regarding how we maintain our software projects:

1. Be more transparent. **Code really should be visible at the organization level.** We gain nothing by hiding from ourselves, we only introduce a high potential cost that is realized with staff change - not just turnover, but re-assignment too.
2. Write more that isn't "code." Executable/deployed code by itself is only a small part of <abbr title="Information Technology">IT</abbr> solution delivery. Without context, that code can be unintelligible, as the last slide in the bonus code samples. **Documentation and unit/integration/e2e tests are really the things that capture the business need of why we worked on a project in the first place, and can even persist through multiple different implementations.** Version control history is critical too, because it lets us capture what we've tried before, particularly when we [write good commit messages](http://chris.beams.io/posts/git-commit/).
3. Curate. Dedicate more time to keeping things buildable, deployable, repeatable. **If we've done 1 and 2, it will be easier for groups with cross cutting focus (deployment, security, testing) to interact with our IT solutions.** That can even facilitate those groups contributing fixes or patches without waiting for a developer to be available to unlock access to the code.

### Additional Resources

* [Google presentation on the scale of their codebase](https://www.youtube.com/watch?v=W71BTkUbdqE). Watch this video, it's worth the 30 minutes. Teaser: Google's internal source is managed in one large repository, shared with the company, with *over 1 billion files and over 2 billion lines of code*. That is **46,296 Anna Kareninas**.
* Anna Karenina metrics courtesy of [Visualization of the complete text of Anna Karenina](http://lab.softwarestudies.com/2010/11/anna-karenina.html).
* Bonus code samples courtesy of [Obfuscating 'Hello World' in Python](https://benkurtovic.com/2014/06/01/obfuscating-hello-world.html).
* Lines of code metrics courtesy of [cloc](https://github.com/AlDanial/cloc).