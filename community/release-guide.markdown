---
layout: default
title: RestyGWT / Release Guide
slogan: How to create releases of RestyGWT
---

## Release Preparation Steps

Various documentation changes first (which should be done before the release is cut)

* update the **{{site.project_id}}-website/src/main/webapp/download.page** page for this release
* edit the **{{site.project_id}}-website/src/main/scala/Website.scala** file and change the **project\_version** values
* blog about the release by creating an entry in **{{site.project_id}}-website/src/blog/releases** 
* update the changelog.md file, copying and pasting the section the release highlights from the above blog post
* add a link to the **{{site.project_id}}-website/src/main/webapp/versions.page** to the new version. 

## Cutting the release 

* prepare the release

    	mvn -P release release:prepare 


* perform the release

		mvn -P release release:perform


* open [Nexus Staging](http://repository.apache.org/index.html#staging) and close the staged release of {{site.name}}
* now start a vote on the mailing lists and if it passes
* promote the closed release in nexus
* announce the release on the [mailing list](index.html)
* drink beers!