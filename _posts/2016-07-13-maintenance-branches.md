---
layout: post
title: Release Management - Maintenance Branches
tags: 
- maven
- git
- release management
- maintenance
- branch
- cherry-pick
---

In this post I'll elaborate on a specific scenario in release management: what to do when fresh new features have been merged to master and the minor (or major) version has gone up, but you need a bug fix for the prior releases.

## Scenario

We have `projectX`, which is a mature, Maven-managed project that is using the [Maven Release Plugin](https://maven.apache.org/maven-release/maven-release-plugin/index.html) to distribute artifacts. The project produces a jar that is used as a dependency in a number of downstream projects in production.

Those downstream projects are depending on 3.0.4 releases of `projectX`. Meanwhile, in `projectX`, just yesterday some features landed on the master branch that necessitated a version bump to 3.1.0-SNAPSHOT (and subsequent 3.1.0 release).

Today, an issue is identified in `projectX` that is disrupting one of the downstream projects. 

## The fix

We've captured the issue affecting `projectX`, a developer was assigned, and produced a fix with a unit test. A pull request is opened targeting the **master** branch of the project which has been reviewed and merged. 

So to get this contribution into a release, if we do nothing else the next available release version from the **master** branch will be *3.1.1*.

## The problem

For a downstream project that was depending on 3.0.4 of `projectX`, having to upgrade to 3.1.1 to get the necessary fix introduces **risk**. 

What if that new feature is unstable? What if that new feature requires refactoring, or configuration changes? We don't want the potential to fix one bug and introduce another.

Enter maintenance branches.

## Path to a 3.0.5 release: Start with a maintenance branch

1. Start with a clone of the `projectX` repository, with a remote to the primary repository (referenced as `upstream` from here on out). Start on the master branch.
2. Make sure your master branch has the fix: `git pull upstream master`
3. Grab all refs that exist in the primary repository: `git fetch upstream`.
4. Checkout the very last tag in the 3.0.x series: `git checkout -b maintenance projectX-3.0.4`
   * Note that this command creates a new branch named *maintenance* that originates from the tag *projectX-3.0.4*. If we just checkout the tag, the subsequent maven command will fail because our working copy is in 'detached HEAD'.
5. Use the following maven command to create the maintenance branch: `mvn release:branch -DbranchName=rel-3-0-patches -DupdateBranchVersions=true -DupdateWorkingCopyVersions=false`[1]
   * You will be prompted for the next version. For this example, our maintenance branch is going to start at 3.0.5-SNAPSHOT (3.0.4 is the origin release, increment patch by 1 and add `-SNAPSHOT`). [See more examples for this goal](https://maven.apache.org/maven-release/maven-release-plugin/examples/branch.html), or [review this goal's full reference](https://maven.apache.org/maven-release/maven-release-plugin/branch-mojo.html).

When this command completes, you'll see a couple things have happened.

* You will have 2 local and remote (the repository listed in your pom's `scm` block) branches: `maintenance` and `rel-3-0-patches`.
* A commit will have been added to both of those branches that changes the `version` element of the pom to the version you were prompted for (3.0.5-SNAPSHOT here) and a change to the `tag` element the `scm` block in the pom that references the `rel-3-0-patches` branch.
* An additional commit will exist on the maintenance branch to revert that commit; feel free to either delete the maintenance branch, or re-use it for later maintenance branch creation.

## Release from the maintenance branch

Now we have a good branch to apply our original code change and make a maven release:

1. Find the SHA-1 id of the commit containing the fix. If the fix is spread across multiple commits, you'll need the ids of each commit, and you'll have to repeat these commands for each one in the order they were applied. Pro tip: squash bug fix pull requests to one commit to save yourself some time.
2. `git checkout rel-3-0-patches`. Double check you are up to date with a `git pull upstream rel-3-0-patches`.
3. Cherry-pick the commit: `git cherry-pick ab423786...`
4. Create a pull request for the commit targeting the upstream's rel-3-0-patches branch.
   * Alternatively, if permissions and team workflow allow, you could just `git push upstream rel-3-0-patches`. I prefer the pull request, because it allows our Continuous Integration server to confirm the merge is successful and the tests all still pass.
5. Run the standard maven release: `mvn release:prepare && mvn release:perform`.

During the prepare phase, you'll be prompted to release `3.0.5-SNAPSHOT` and increment the version of the pom to `3.0.6-SNAPSHOT`. It's important to know that the change to the `tag` element in the `scm` block will result in that version bump commit landing on the `rel-3-0-patches` branch upstream, rather than master (that would be bad).

## Without the mvn release:branch

This process can be replicated without the `mvn release:branch` command, however you **MUST** make the changes to the pom (changing version to [patch+1]-SNAPSHOT, changing the scm.block) that that command does, or else your maven release will be performed on master and pushed upstream. 

That is bad, because now master, which was 3.1.X, now goes back to 3.0.X, and you'll need to do some major surgery or manual changes to the pom to get back to 3.1.X series. No one likes to see a messy git history like that!





