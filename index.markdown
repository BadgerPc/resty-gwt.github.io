---
layout: index
title: RestyGWT
---

<div class="jumbotron">
	<div class="logo">
      <img src="/images/restygwt-logo.png" alt="RestyGWT logo">
    </div>
  <h1>Making Restful Services Accessible to GWT Applications</h1>
</div>

<div class="major-links">
    <a href="{{site.github_page}}"><i class="fa fa-github"></i><span >Fork RestyGWT !</span></a>
    <a href="http://groups.google.com/group/restygwt"><img src="/images/my-groups-color.png" width="50px"/>RestyGWT discussion group</a>
  </div>
<br/><br/>

<i class="fa fa-circle number"><span class="number">1</span><span class="text">Add RestyGWT to your classpath</span></i>
<br/><br/>
Download the latest stable RestyGWT jar
<br/>

- [{{site.project_name}}-{{site.project_version}}.jar]({{site.release_base}}/{{site.project_version}}/{{site.project_name}}-{{site.project_version}}.jar)

Or if you are a maven user, then just add the following dependency to your `pom.xml`.
<br/><br/>

{% highlight xml %}
<dependency>
    <groupId>org.fusesource.{{site.project_name}}</groupId>
    <artifactId>{{site.project_name}}</artifactId>
    <version>{{site.project_version}}</version>
</dependency>
{% endhighlight %}

<i class="fa fa-circle number"><span class="number">2</span><span class="text">Add RestyGWT to you GWT module file</span></i>
<br/><br/>
{% highlight xml %}
<inherits name="org.fusesource.restygwt.RestyGWT"/>
{% endhighlight %}

<i class="fa fa-circle number"><span class="number">3</span><span class="text">Define your REST client interface</span></i>
<br/><br/>
Use [JAX-RS](https://jax-rs-spec.java.net/) annotations to make it even simpler !
<br/><br/>
{% highlight java %}
public interface PizzaService extends RestService {
    @POST
    @Path("pizzaorders")
    public void order(PizzaOrder request, 
                      MethodCallback<OrderConfirmation> callback);
}
{% endhighlight %}

<i class="fa fa-circle number"><span class="number">4</span><span class="text">Use your client</span></i>
<br/><br/>
{% highlight java %}
PizzaService service = GWT.create(PizzaService.class);
service.order(order, callback);
{% endhighlight %}


<br/><br/>
Want to know more ? Checkout the [documentation](/documentation/restygwt-user-guide.html) or [restygwt google group](http://groups.google.com/group/restygwt)

<br/><br/><br/><br/>
