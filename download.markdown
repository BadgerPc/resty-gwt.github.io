---
layout: default
title: RestyGWT / Download
slogan: Download it today!
release_base: http://repo.fusesource.com/nexus/content/groups/public/org/fusesource/restygwt/restygwt
project_versions: [1.0,1.1,1.2,1.3]
project_id: restygwt
project_snapshot_version: 1.4-SNAPSHOT
project_version: 1.3
---

Warning : This site is in "re-construction". Please visit [http://restygwt.fusesource.org/](http://restygwt.fusesource.org/)

## Download

You can download {{site.name}} from the [Maven Repository](http://repo.fusesource.com/nexus/content/repositories/public/) 
in the [{{site.name}} area]({{page.release_base}}).
<br/>
<br/>
Download:

{% for version in page.project_versions  %}

- [{{page.project_id}}-{{version}}.jar]({{page.release_base}}/{{version}}/{{page.project_id}}-{{version}}.jar)

{% endfor %}

## Snapshots

Want to help stablize the latest and greatest nightly development build? **Warnning**: This build may be extremely bleeding edge!  

- [{{page.project_id}}-{{page.project_snapshot_version}}.jar](http://repo.fusesource.com/nexus/service/local/artifact/maven/redirect?r=snapshots&g=org.fusesource.{{page.project_id}}&a={{page.project_id}}&v={{page.project_snapshot_version}}&e=jar)

## As a Maven Dependency 

If you are a maven user, then just add the following dependency to your `pom.xml`

{% highlight xml %}
<dependency>
    <groupId>org.fusesource.{{page.project_id}}</groupId>
    <artifactId>{{page.project_id}}</artifactId>
    <version>{{page.project_version}}</version>
</dependency>
{% endhighlight %}

If you are using a snapshot version, then you will also need to 
also add the following snapshot repository to your pom:

{% highlight xml %}
<repository>
    <id>fusesource-snapshots</id>
    <name>Fusesource Snapshots</name>
    <url>http://repo.fusesource.com/nexus/content/repositories/snapshots</url>
    <snapshots><enabled>true</enabled></snapshots>
    <releases><enabled>false</enabled></releases>
</repository>
{% endhighlight %}



 