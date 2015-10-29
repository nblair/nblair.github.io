---
layout: post
title: Configuring GPG/PGP for Maven Releases to Sonatype on Mac OS X
---

Each time I go a long stretch without publishing a Maven artifact via Sonatype, I find it's easy to trip up on the GPG configuration, particularly on Mac OS X. I'm recording this here so hopefully the next time it's a little easier.

### Requirements

Assume [Homebrew](http://brew.sh/) is installed.

1. `brew install gpg2`
2. `brew install gpg-agent`

After gpg-agent is installed, you'll want to tweak your shell via it's rc file (I use [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)). Add the following at the end:

```sh
eval $(gpg-agent --daemon --no-grab --write-env-file $HOME/.gpg-agent-info)
export GPG_TTY=$(tty)
export GPG_AGENT_INFO
```

### Create and publish your Public Key

You don't have to do this every time. Run `gpg2 --list-keys` to see what you have. Publish a key only if you don't have any keys or if they've expired.

[Sonatype has good documentation for this already](http://central.sonatype.org/pages/working-with-pgp-signatures.html). 

TL;DR version:

1. `gpg2 --gen-key`. Yes, you should encrypt your key with a passphrase, remember what you used, you'll need it later.
2. `gpg2 --keyserver hkp://pool.sks-keyservers.net --send-keys keyid`

### Configure Maven

We need to pass properties into the [maven-gpg-plugin](). Putting them in the project's pom is a terrible idea, and passing them on the command line is awkward.

A better place is in our `~/.m2/settings.xml`; add the following to your `<profiles>` block in that file ([and here's the reference if you don't already have a custom Maven settings file](https://maven.apache.org/settings.html)):

```xml
<profile>
  <id>gpg</id>
  <activation>
    <activeByDefault>true</activeByDefault>
  </activation>
  <properties>
    <gpg.useagent>true</gpg.useagent>
    <!-- gpg-plugin defaults to trying 'gpg' on the path, this changes that to 'gpg2' instead -->
    <gpg.executable>gpg2</gpg.executable>
    <!-- <gpg.passphrase>secret-passphrase-here</gpg.passphrase> -->
  </properties>
</profile>
```

Note the commented out property. There is a step during the Maven release perform goal where the gpg-plugin runs that will sign the artifacts generated for the module(s). If your key is encrypted with a passphrase, a prompt will appear. stdin for this prompt isn't reachable, so you have no way to enter it. 

You have 2 choices here:

1. Set `gpg.useagent` to false, and keep a plaintext copy of your passphrase in this file.
2. Set `gpg.useagent` to true, and remember to interact with the gpg-agent before running the Maven Release so that it can cache your passphrase.

Use your best judgment on what you are comfortable doing with your passphrase.

### If you are using gpg.useagent=true

Before you run the Maven release, you need to interact with gpg-agent to get your passphrase cached. [I've created a small gist with the steps](https://gist.github.com/nblair/0ab78266b5226c81f6be). 

1. Create some temp file.
2. `gpg2 -ab that-tempfile`
3. You'll be prompted by gpg for your passphrase. Upon success, you'll see another file with .asc extension next to the tempfile.

Now the gpg-agent in this terminal session has your passphrase. If you repeat the steps, you'll note you don't get asked again for the passphrase! **Now run the Maven release process (mvn release:prepare, mvn release:perform) in the same terminal session.**





 