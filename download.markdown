---
layout: default
title: RestyGWT / Download
slogan: Download it today!
---
{% assign project_version = site.project_versions.last %}
## Download

You can download {{site.name}} from the [Maven Repository](http://repo.fusesource.com/nexus/content/repositories/public/) 
in the [{{site.name}} area]({{site.release_base}}).
<br/>
<br/>

Download latest stable version
<br/>
<br/>

[{{site.project_name}}-{{project_version}}.jar]({{site.release_base}}/{{project_version}}/{{site.project_name}}-{{project_version}}.jar)

<br/>
<br/>

Previous versions
<br/>
<br/>

{% for version in site.project_versions reversed %}
	{% unless version == site.project_versions.last %}
- [{{site.project_name}}-{{version}}.jar]({{site.release_base}}/{{version}}/{{site.project_name}}-{{version}}.jar)
	{% endunless %}
{% endfor %}

## Snapshots

Want to help stabilize the latest and greatest nightly development build? **Warning**: This build may be extremely bleeding edge!
<br/>
<br/>
Latest version : 
{{site.project_snapshot_version:}}
<br/>
<br/>
Snapshots can be found here :
<br/>
<br/>
[https://repository.jboss.org/nexus/content/repositories/fs-snapshots/org/fusesource/restygwt/restygwt/](https://repository.jboss.org/nexus/content/repositories/fs-snapshots/org/fusesource/restygwt/restygwt/)

## As a Maven Dependency 

If you are a maven user, then just add the following dependency to your `pom.xml`

{% highlight xml %}
<dependency>
    <groupId>org.fusesource.{{site.project_name}}</groupId>
    <artifactId>{{site.project_name}}</artifactId>
    <version>{{project_version}}</version>
</dependency>
{% endhighlight %}

If you are using a snapshot version, then you will also need to 
also add the following snapshot repository to your pom:

{% highlight xml %}
<repository>
    <id>fusesource-snapshots</id>
    <name>Fusesource Snapshots</name>
    <url>https://repository.jboss.org/nexus/content/repositories/fs-snapshots</url>
    <snapshots><enabled>true</enabled></snapshots>
    <releases><enabled>false</enabled></releases>
</repository>
{% endhighlight %}



 
