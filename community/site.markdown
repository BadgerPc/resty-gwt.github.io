---
layout: default
title: RestyGWT / Site
slogan: How the website works
---

##How it works
  
The *{{site.name}}* documentation and site is created using Scalate
and is stored in git in the same [source control system as the code](/source.html)

**Note:** For code highlighting you will need to
[install](http://pygments.org/download/)
[http://pygments.org/](http://pygments.org).

## Editing the source code
  
If you [grab the source code](/source.html) you will find the site
source in the *{{site.project_id}}-website* directory. The website is
also built using Maven so, you would <br/><br/>

    cd {{site.project_id}}-website
    mvn install

If you want to edit the files in your text editor and be able to
immediately see the site re-rendered in a browser then use<br/><br/>

    mvn jetty:run

You can now surf the site and see changes immediately.<br/><br/>

If you are on OS X then we highly recommend
[TextMate](http://macromates.com/) as a great editor which supports
Textile and Markdown

## Deploying the site

  
The site is automatically deployed by the CI builds, so you can
simply wait for the changes to be automatically pushed to the project
site.<br/><br/>

You can manually deploy the site using Maven:<br/><br/>

    mvn deploy

Since deployment to our webserver requires proper authorization,
you will need to add an entry to your ~/.m2/settings.xml file simlilar
to:<br/><br/>

{% highlight xml %}
<server>
  <id>{{site.project_id}}-website</id>
  <username>xxxx</username>
  <password>xxxxx</password>
</server>
{% endhighlight %}