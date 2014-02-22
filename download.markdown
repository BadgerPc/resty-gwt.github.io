---
layout: default
title: RestyGWT / Download
slogan: Download it today!
---

## Download

You can download {{site.name}} from the [Maven Repository](http://repo.fusesource.com/nexus/content/repositories/public/) 
in the [{{site.name}} area]({{site.release_base}}).
<br/>
<br/>
Download:

{% for version in site.project_versions  %}

- [{{site.project_name}}-{{version}}.jar]({{site.release_base}}/{{version}}/{{site.project_name}}-{{version}}.jar)

{% endfor %}

## Snapshots

Want to help stablize the latest and greatest nightly development build? **Warnning**: This build may be extremely bleeding edge!  

- [{{site.project_name}}-{{site.project_snapshot_version}}.jar](http://repo.fusesource.com/nexus/service/local/artifact/maven/redirect?r=snapshots&g=org.fusesource.{{site.project_name}}&a={{site.project_name}}&v={{site.project_snapshot_version}}&e=jar)

## As a Maven Dependency 

If you are a maven user, then just add the following dependency to your `pom.xml`

{% highlight xml %}
<dependency>
    <groupId>org.fusesource.{{site.project_name}}</groupId>
    <artifactId>{{site.project_name}}</artifactId>
    <version>{{site.project_version}}</version>
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



 