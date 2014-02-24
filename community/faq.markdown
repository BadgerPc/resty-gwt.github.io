---
layout: default
title: RestyGWT / F.A.Q.
slogan: This page contains all the frequently asked questions about the RestyGWT project.
---

## General Questions

General questions on the *{{site.name}}* project.

### Why the name {{site.name}}?

### What is the license?

The license is [Apache 2 License](http://www.apache.org/licenses/LICENSE-2.0)

### How do I get support?

See the [support guide](support.html) for more details.

### How do I build Scalate?

First [get the source](/source.html) then see the [building guide](/building.html)

### Why does the Maven build not download jars?

The default profile in maven does not include remote repos, so make sure
you add the download profile when building for the first time. First [get the source](/source.html) then see the [building guide](/building.html).<br/><br/>

    mvn install -P download

### How do I contribute or become a committer?

We love [contributions](contributing.html)! More details on how to
contribute and how to become a committer are in the [contributing guide](contributing.html).

### How does the website work?

For details on how to edit the website and how it works see 
[How the Site works](site.html)

## Using {{site.name}}

### How do I get started?