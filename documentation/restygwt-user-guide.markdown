---
layout: default
title: RestyGWT / Documentation
slogan: Learn how to use restyGWT
---

# RestyGWT User Guide

RestyGWT is a GWT generator for REST services and JSON encoded data transfer objects.

## Features

* Generates Async Restful JSON based service proxies
* Java Object to JSON encoding/decoding
* Easy to use REST API

## REST Services

RestyGWT Rest Services allow you to define an asynchronous Service API which is then implemented via
GWT deferred binding by a RestyGWT generator. See the following listing for an example:

<br/>
{% highlight java %}
import javax.ws.rs.POST;

public interface PizzaService extends RestService {
    @POST
    public void order(
        PizzaOrder request, 
        MethodCallback<OrderConfirmation> callback);
}
{% endhighlight %}

JAX-RS annotations are used to control how the methods interface map to HTTP requests. The
interface methods MUST return either `void`, `Request` or `JsonpRequest`. Each method must declare at least one callback argument.
Methods can optionally declare one method argument before the callback to pass via the request
body.
<br/><br/>
or if using `REST.withCallback(..).call(..)`, you can reuse the same interface on the server side as well:

<br/>
{% highlight java %}
import javax.ws.rs.POST;

public interface PizzaService extends DirectRestService {
    @POST
    public OrderConfirmation order(PizzaOrder request);
}
{% endhighlight %}

### JAX/RS Subresource locators

RestyGWT supports JAX-RS subresource locator methods as synchronous methods on service interfaces. For instance:

<br/>
{% highlight java %}
public interface LibraryService extends RestService {
    @Path("book/{isbn}")
    public BookService getBook(@PathParam("isbn") String isbn);
}

public interface BookService extends RestService {
    @GET
    @Path("title")
    public void getTitle(MethodCallback<String> callback);

    @PUT
    @Path("title")
    public void setTitle(String newTitle, MethodCallback<Void> callback);
}
{% endhighlight %}

This allows developers to decompose service interfaces into logical chunks and even reuse interfaces for multiple sections of the virtual
REST URL hierarchy. Currently only `@PathParam` subresource locators are supported.

### JSON Encoding
Java beans can be sent and received via JSON encoding/decoding. Here what the classes declarations
look like for the `PizzaOrder` and `OrderConfirmation` in the previous example:

<br/>
{% highlight java %}
public class PizzaOrder {
    public String phone_number;
    public boolean delivery;
    public List<String> delivery_address = new ArrayList<String>(4);
    public List<Pizza> pizzas = new ArrayList<Pizza>(10);
}

public class OrderConfirmation {
    public long order_id;
    public PizzaOrder order;
    public double price;
    public Long ready_time;
}
{% endhighlight %}

The JSON encoding style is compatible with the default [Jackson](http://wiki.fasterxml.com/JacksonHome) Data Binding. For example,
`PizzaOrder`'s JSON representation would look like:

<br/>
{% highlight json %}
{
    "phone_number":null,
    "delivery":true,
    "delivery_address":[
        "3434 Pinerun Ave.",
        "Wesley Chapel, FL 33734"
    ],
    "pizzas":[
        {"quantity":1,"size":16,"crust":"thin","toppings":["ham","pineapple"]},
        {"quantity":1,"size":16,"crust":"thin","toppings":["extra cheese"]}
    ]
}
{% endhighlight %}

A GWT client creates an instance of the REST service and associate it with a HTTP
resource URL as follows:

<br/>
{% highlight java %}
Resource resource = new Resource( GWT.getModuleBaseURL() + "pizza-service");

PizzaService service = GWT.create(PizzaService.class);
((RestServiceProxy)service).setResource(resource);

service.order(order, callback);
{% endhighlight %}

### Request Dispatchers

The request dispatcher intercepts all requests being made and can supply
additional features or behavior. For example, RestyGWT also supports a
`CachingRetryingDispatcher` which will automatically retry requests if
they fail.
<br/><br/>
To configure the `CachingRetryingDispatcher`, you can configure it on
your service interface at either the class or method level. Example:

<br/>
{% highlight java %}
@POST
@Options(dispatcher=CachingRetryingDispatcher.class)
public void order(
    PizzaOrder request,
    MethodCallback<OrderConfirmation> callback);
{% endhighlight %}

### Creating Custom Request Dispatchers

You can create a custom request dispatcher by implementing the following `Dispatcher`
interface:

<br/>
{% highlight java %}
public interface Dispatcher {
    public Request send(
        Method method, 
        RequestBuilder builder) throws RequestException;
}
{% endhighlight %}

Your dispatcher implementation must also define a static singleton instance in a public
`INSTANCE` field. Example:

<br/>
{% highlight java %}
public class SimpleDispatcher implements Dispatcher {
    public static final SimpleDispatcher INSTANCE = new SimpleDispatcher();

    public Request send(
        Method method, 
        RequestBuilder builder) throws RequestException {
        return builder.send();
    }
}
{% endhighlight %}

When the dispatcher's `send` method is called, the provided builder will already
be configured with all the options needed to do the request.

### Configuring the Request Timeout

You can use the `@Options` annotation to configure the timeout for requests
at either the class or method level. The timeout is specified in milliseconds,
For example, to set a 5 second timeout:

<br/>
{% highlight java %}
@POST
@Options(timeout=5000)
public void order(
    PizzaOrder request,
    MethodCallback<OrderConfirmation> callback);
{% endhighlight %}

### Configuring service root URLs

There are two ways to configure service root URLs which are appended with the `@Path` annotation property
when building the final service URL. For single service root URL the `Defaults.setServiceRoot(String)` method can be used. When several services with different service roots are used the `@Options` annotation is equipped with
the `serviceRootKey` property which can be set to read service root entries provided with the static
`ServiceRoots.add(String, String)` method.

### Configuring the Expected HTTP Status Code

By default results that have a 200, 201, or 204 HTTP status code are considered
to have succeeded. You can customize these defaults by setting the
`@Options` annotations at either the class or method level of the service interface.
<br/><br/>
Example:

<br/>
{% highlight java %}
@POST
@Options(expect={200,201})
public void order(
    PizzaOrder request,
    MethodCallback<OrderConfirmation> callback);
{% endhighlight %}

### Configuring the `Accept` and `Content-Type` and HTTP Headers

RestyGWT rest calls will automatically set the `Accept` and `Content-Type`
and HTTP Headers to match the type of data being sent and the type of
data expected by the callback. You can override these default values
by adding JAX-RS' `@Produces` and `@Consumes` annotations to the method
declaration.

### Keep the Java Interface clean

If you need an attribute of your DTO to be part of the URL you can do it by adding the `@Attribute` annotation alongside the `@PathParam`. The `@PathParam` references the placeholder in the `@Path` as usual and the `@Attribute` identifies the attribute/field of the DTO which shall be used for the replacement in the path.
<br/><br/>
Example:

<br/>
{% highlight java %}
@PUT
@Path("/{id}")
public void updateOrder(
    @PathParam("id") @Attribute("order_id") OrderConfirmation order,
    MethodCallback<OrderConfirmation> callback);
{% endhighlight %}

### Mapping to a JSONP request

If you need to access JSONP URl, then use the `@JSONP` annotation on the method.
For example:

<br/>
{% highlight java %}
import org.fusesource.restygwt.client.JSONP;

public interface FlickrService extends RestService {
    @Path("http://www.flickr.com/services/feeds/photos_public.gne?format=json")
    @JSONP(callbackParam="jsonFlickrFeed")
    public void photoFeed(JsonCallback callback);
}
{% endhighlight %}

or if you have a `FlickrFeed` value object

<br/>
{% highlight java %}
import org.fusesource.restygwt.client.JSONP;
 
public interface FlickrService extends RestService {
    @Path("http://www.flickr.com/services/feeds/photos_public.gne?format=json")
    @JSONP(callbackParam="jsonFlickrFeed")
    public void photoFeed(MethodCallback<FlickrFeed> callback);
}
{% endhighlight %}

## JSON Encoder/Decoders

If you want to manually access the JSON encoder/decoder for a given type just define
an interface that extends `JsonEncoderDecoder` and RestyGWT will implement it for you using
GWT deferred binding.
<br/><br/>
Example:
<br/><br/>
First you define the interface:

<br/>
{% highlight java %}
import javax.ws.rs.POST;

public interface PizzaOrderCodec extends JsonEncoderDecoder<PizzaOrder> {}
{% endhighlight %}

Then you use it as follows

<br/>
{% highlight java %}
// GWT will implement the interface for you
PizzaOrderCodec codec = GWT.create(PizzaOrderCodec.class);

// Encoding an object to JSON
PizzaOrder order = ...
JSONValue json = codec.encode(order);

// decoding an object to from JSON
PizzaOrder other = codec.decode(json);
{% endhighlight %}

In case you need to parse the JSON from a `String` use

<br/>
{% highlight java %}
JSONValue json = JSONParser.parseStrict( jsonString );
{% endhighlight %}

### Date Format

the default date format is `yyyy-MM-dd'T'HH:mm:ss.SSSZ`
and can be set in the `Defaults` class

<br/>
{% highlight java %}
Defaults.setDateFormat("yyyy-MM-dd'T'HH:mm:ss");
{% endhighlight %}

when setting it to `null` then unix time will be used.

### Customizing the JSON Property Names

If you want to map a field name to a different JSON property name, you
can use the `@Json` annotation to configure the desired name. Example:

<br/>
{% highlight java %}
public class Message {
    @Json(name="message-id")
    public String messageId;
}
{% endhighlight %}

or with the Jackson annotation

<br/>
{% highlight java %}
public class Message {
    @JSONProperty("message-id")
    public String messageId;
}
{% endhighlight %}

### Using read-only objects

If you want to declare final fields and initialize an object through its constructor,
you can use the `@JsonCreator` and the `@JsonProperty` annotations. Example:

<br/>
{% highlight java %}
public class Credentials {
    @JsonProperty
    private final String password;
    @JsonProperty
    private final String email;

    @JsonCreator
    public Credentials(
        @JsonProperty("email") String email, 
        @JsonProperty("password") String password) {
        this.email = email;
        this.password = password;
    }

    public String getEmail() {
        return email;
    }

    public String getPassword() {
        return password;
    }
}
{% endhighlight %}

Note that all the parameters of an annotated constructor should be annotated with the `@JsonProperty`.
As long as each property name matches the declared field names, the
order can be freely chosen.

### Using gwt-jackson for JSON serialization and deserialization

**NOTE**: the gwt-jackson integration is still *experimental*, so
  please give it trial and report bugs to resty-gwt and/or gwt-jackson as in the long run gwt-jackson will the default.
  <br/><br/>

Add following to your GWT module XML file:

<br/>
{% highlight xml %}

    <inherits name="com.github.nmorel.gwtjackson.GwtJackson" />

    <set-property name="restygwt.encodeDecode.useGwtJackson" value="true" />

{% endhighlight %}

See also
[jackson support](https://github.com/nmorel/gwt-jackson/wiki/Jackson-annotations-support)
of gwt-jackson.

### Custom Serializer Generators

RestyGWT allows you to add another encode/decoder-generator which is used with certain classes. There are three simple steps for enabling fully customized serializer generation:

* Extend [`org.fusesource.restygwt.rebind.JsonEncoderDecoderClassCreator`](https://github.com/cponomaryov/resty-gwt/blob/master/restygwt/src/main/java/org/fusesource/restygwt/rebind/JsonEncoderDecoderClassCreator.java) to create an external serializer generator that can be registered to handle values of certain types. You should override the `generate()` method. Example: [`org.fusesource.restygwt.server.complex.OptionalSerializerGenerator`](https://github.com/cponomaryov/resty-gwt/blob/master/restygwt/src/test/java/org/fusesource/restygwt/server/complex/OptionalSerializerGenerator.java).
* Extend [`org.fusesource.restygwt.rebind.RestyJsonSerializerGenerator`](https://github.com/cponomaryov/resty-gwt/blob/master/restygwt/src/main/java/org/fusesource/restygwt/rebind/RestyJsonSerializerGenerator.java) and specify two things:
    * your custom serializer generator class created in the first step (`getGeneratorClass()` method)
    * target class or its superclass to use this generator for (`getType(TypeOracle)` method).
Example: [`org.fusesource.restygwt.server.complex.OptionalRestySerializerGenerator`](https://github.com/cponomaryov/resty-gwt/blob/master/restygwt/src/test/java/org/fusesource/restygwt/server/complex/OptionalRestySerializerGenerator.java).
* Specify your `RestyJsonSerializerGenerator` custom implementation fully qualified class name in the `org.fusesource.restygwt.restyjsonserializergenerator` configuration property in your GWT module XML file. This property is multi-valued so you can add multiple custom serializer generators. Example:

<br/>
{% highlight xml %}
    <extend-configuration-property 
        name="org.fusesource.restygwt.restyjsonserializergenerator"
        value="com.example.CustomRestySerializerGenerator1"/>
    <extend-configuration-property 
        name="org.fusesource.restygwt.restyjsonserializergenerator"
        value="com.example.CustomRestySerializerGenerator2"/>
{% endhighlight %}

## Custom Annotation Handler

Due to the functionality of GWT generators, a generator needs to be responsible
for the whole generation processing of a particular type. To give RestyGWT users
the flexibility to extend this generation process, you can register custom
implementations of `org.fusesource.restygwt.rebind.AnnotationResolver`.


### Runtime Annotation Information

Whilst annotation-parsing and processing happens only in generation/compilation
process so far, `AnnotationResolver` are able to compile extra information into the
`org.fusesource.restygwt.client.RestService` implementation, which will be
available inside a `org.fusesource.restygwt.client.Dispatcher` and inside a
`com.google.gwt.http.client.RequestCallback`.
<br/><br/>
Transport of the Annotation information happens via
`org.fusesource.restygwt.client.Method#addData(String key, String value)`
<br/><br/>
The ability to use annotation information during runtime makes it really easy
to implement own annotations which can be applied to `RestService` definitions
and cause a customized runtime behaviour later on.
<br/><br/>
One use case of such an extension of the annotation parsing is the following:


### Model Change Events

Goal: the `Presenter` wants to be informed about changes in the model, so a
`reload` of some data can be automatically triggered, whenever a particular
`org.fusesource.restygwt.example.client.event.ModelChangeEvent` is caught.
<br/><br/>
To have a central location where such `ModelChangeEvents` are thrown, a
`RequestCallback` seems perfect. Usually -- without custom `AnnotationResolver`s
there would be no information about "what event for which domain type should be thrown".
<br/><br/>
Solution is to have a `org.fusesource.restygwt.rebind.ModelChangeAnnotationResolver`.
This class must be registered on RestyGWT's generation process in your `*.gwt.xml` via

<br/>
{% highlight xml %}
    <set-configuration-property 
        name="org.fusesource.restygwt.annotationresolver"
        value="org.fusesource.restygwt.rebind.ModelChangeAnnotationResolver"/>
{% endhighlight %}

Additionally assume the following `RestService` interface definition

<br/>
{% highlight java %}
package org.fusesource.restygwt.client.event;

import javax.ws.rs.GET;
import javax.ws.rs.HeaderParam;
import javax.ws.rs.PUT;
import javax.ws.rs.Path;
import javax.ws.rs.PathParam;

import org.fusesource.restygwt.client.MethodCallback;
import org.fusesource.restygwt.client.ModelChange;
import org.fusesource.restygwt.client.RestService;

import com.google.gwt.json.client.JSONValue;

/**
* @author andi
*/
public interface ModelChangeAnnotatedService extends RestService {

    @PUT
    @Path("/foo/{fooId}")
    @ModelChange(domain=Foo.class)
    public void setItem(
        @HeaderParam("X-Echo-Code") int responseCode,
        @PathParam("fooId") int fooId, MethodCallback<Void> callback);
}
{% endhighlight %}

Now we will have information about our custom annotation inside the
`Dispatcher`...

<br/>
{% highlight java %}
public class CachingRetryingDispatcher implements Dispatcher {

    public Request send(Method method, RequestBuilder builder) throws RequestException {
        String[] DomainNameForUpdate = method.getData()
            .get(ModelChange.MODEL_CHANGED_DOMAIN_KEY);
        //...
{% endhighlight %}

... and inside the `RequestCallback`

<br/>
{% highlight java %}
modelChangeAnnotatedService.setItem(
    Response.SC_CREATED, 
    1, 
    new MethodCallback<Void>() {
        @Override
        public void onSuccess(Method method, Void response) {
            GwtEvent e = ModelChangeEvent.factory(method.getData()
                        .get(ModelChange.MODEL_CHANGED_DOMAIN_KEY));
            eventBus.fireEvent(e);
            // ...
{% endhighlight %}

A fully working example can be found in the integration-test:
`org.fusesource.restygwt.client.event.ModelChangeAnnotationTestGwt`.


## REST API

The RestyGWT REST API is handy when you don't want to go through the trouble of creating
service interfaces.
<br/><br/>
The following example, will post  a JSON request and receive a JSON response.
It will set the HTTP `Accept` and `Content-Type` and `X-HTTP-Method-Override` headers to the expected values.
It will also expect a HTTP 200 response code, otherwise it will
consider request the request is a failure.

<br/>
{% highlight java %}
Resource resource = new Resource( GWT.getModuleBaseURL() + "pizza-service");

JSONValue request = ...

resource.post().json(request).send(
    new JsonCallback() {
        public void onSuccess(Method method, JSONValue response) {
            System.out.println(response);
        }
        public void onFailure(Method method, Throwable exception) {
            Window.alert("Error: "+exception);
        }
    });
{% endhighlight %}

All the standard HTTP methods are supported:

* `resource.head()`
* `resource.get()`
* `resource.put()`
* `resource.post()`
* `resource.delete()`

Calling one of the above methods creates a `Method` object. You must create a new one
for each HTTP request. The `Method` object uses fluent API for easy building
of the request via method chaining. For example, to set the user id and password
used in basic authentication you would:

<br/>
{% highlight java %}
method = resource.get().user("hiram").password("pass");
{% endhighlight %}

You can set the HTTP request body to a text, XML, or JSON value. Unless the content type
is explicitly set on the method, the following methods will set the `Content-Type` header
for you:

* `method.text(String data)`
* `method.xml(Document data)`
* `method.json(JSONValue data)`

The `Method` object also allows you to set headers, a request timeout, and the expected
'successful' status code for the request.
<br/><br/>
Finally, once you are ready to send the HTTP request, pick one of the following methods
based on the expected content type of the result. Unless explicitly set on the method,
the following methods will set the `Accept` header for you:

* `method.send(TextCallback callback)`
* `method.send(JsonCallback callback)`
* `method.send(XmlCallback callback)`

The response to the HTTP request is supplied to the callback passed in the `send` method.
Once the callback is invoked, the `method.getRespose()` method to get the GWT `Response`
if you are interested in things like the headers set on the response.

### Polymorphic Sub Types

A common feature used in Object Oriented languages is to use polymorphism to represent
several specialist versions of a parent object. This structure is simple to serialise
using GWT''s built-in RPC system as it represents the Java types via a specialist protocol.
<br/><br/>
Unfortunately the JSON spec doesn't directly support this behaviour. However it can be
added by including the type information in an extra JSON property. JSON parsers that are
not aware of this special behaviour will see it as a regular JSON property.
<br/><br/>
RestyGWT supports polymorphic sub-types via two of the Jackson features -- `@JsonSubTypes` and `@JsonTypeIdResolver`.
`@JsonSubTypes` is used when you have perfect knowledge of all possible subtypes at coding time. `@JsonTypeIdResolver`
can be used in situations where you want to classpath-scan or use some other dynamic mechanism to determine
the possible subtypes at application assembly (GWT compilation) time.

#### `@JsonSubTypes` Example

The Jackson library (a JSON parser) supports this behaviour by adding a series of annotations to the
POJO classes will be serialised to JSON. RestyGWT can also use these annotations
to provide the same Jackson compatible behaviour.
<br/><br/>
In the below example we have a zoo that contains various different types of animals, each
with their own specific properties. The super class that the other classes inherit must
be an abstract class and annotated like the example below:

<br/>
{% highlight java %}
@JsonSubTypes({@Type(value=Dog.class, name="Dog"), @Type(Cat.class)})
@JsonTypeInfo(use=Id.NAME, include=As.PROPERTY, property="@class")
public abstract class Animal {
    protected Animal() { }
}
{% endhighlight %}

The sub class uses the `@JsonTypeInfo` annotation to declare how the type information will
be included in the JSON. Only the `Name` and `Class` `use` methods are supported at present.
`Class` will use the full Java class name whereas `Name` will use a name provided by the user
to represent each type.
<br/><br/>
The `@JsonSubTypes` annotation provides a list of possible types which the JSON can be mapped
to. These must all be sub classes of this type or one of its sub classes. If the name method
is used the name can be optionally specified for each type. In the example above only the `Dog`
class name is specified.

<br/>
{% highlight java %}
public class Dog extends Animal {
    public double barkVolume; // in decibels
    public Dog() { }
}

@JsonTypeName("Cat")
public class Cat extends Animal {
    public boolean likesCream;
    public int lives;
    public Cat() { }
}
{% endhighlight %}

The `@JsonTypeName` annotation can be used to specify the name the type will be mapped to. It must be
specified via this method or the `@JsonSubTypes` annotation on the super class. If both are
specified the one on the `@JsonSubTypes` is used.
<br/><br/>
Notice no additional information is needed to serialise the `Dog` class as all its information
is specified on the `Animal` super class.

<br/>
{% highlight java %}
public class Zoo {
    public List<Animal> animals;
}
{% endhighlight %}

Finally we can use the `Animal` class as we would if it was a single concrete class. When the
POJO is used in the GWT code you can use the animal list the same as you would with any
polymorphic list. Usually by using  the instanceof keyword and casting the object to the specific type.

#### `@JsonTypeIdResolver` Example

In this example, suppose we have an abstract class `Animal`, but we don't know until we have all of the
jars assembled for the final application just what kinds of animal the application will be supporting. Resty
can handle this situation like this:

<br/>
{% highlight java %}
@JsonTypeIdResolver(my.project.AnimalResolver.class)
@JsonTypeInfo(use=Id.NAME, include=As.PROPERTY, property="@type")
public abstract class Animal{}
{% endhighlight %}

You may have a proper implementation of `AnimalResolver` (which must implement `org.codehaus.jackson.map.jsontype.TypeIdResolver`)
for use by Jackson elsewhere in your system (e.g. on the server to write out these objects properly in response to
REST calls). However, because the `TypeIdResolver` interface does not offer an API that publishes all of the available subtypes, we
will need to implement an extension interface -- namely `RestyJsonTypeIdResolver`.
<br/><br/>
This is done as follows:
<br/><br/>
First, in your `*.gwt.xml`, instruct Resty to add your implementation to the available `RestyJsonTypeIdResolver`s available at compile time.

<br/>
{% highlight xml %}
    <extend-configuration-property 
        name="org.fusesource.restygwt.jsontypeidresolver" 
        value="my.project.gwt.dev.AnimalRestyTypeIdResolver"/>
{% endhighlight %}

and then implement `AnimalRestyTypeIdResolver` as follows:

<br/>
{% highlight java %}
public class AnimalRestyTypeIdResolver implements RestyJsonTypeIdResolver
{
    @Override
    public Class<? extends TypeIdResolver> getTypeIdResolverClass()
    {
        return AnimalResolver.class;
    }

    @Override
    public Map<String, Class<?>> getIdClassMap()
    {
        // use classpath scanning or SPI or some other dynamic way to generate
        // definitive map of subtypes and associated "tags"
    }
}
{% endhighlight %}

'AnimalRestyTypeIdResolver will be instantiated and interrogated at GWT compile time (deferred binding time, actually). This code will not be compiled itself into
JavaScript -- just used to generate JavaScript classes that will be used to serialize your Jackson annotated POJOs.
<br/><br/>
You can optionally implement `RestyJsonTypeIdResolver` on your `TypeIdResolver` class, in which case you do not need to include the configuration property setting.

### Reusing server interfaces.

If you're using JAX-RS on the server-side as well, you might notice that you're repeating yourself in the client and
server interfaces, with only a minor change for the client interface (since the callback must be asynchronous). So if
you would have a JAX-RS service that just lists files, implemented like this:

<br/>
{% highlight java %}
public class ListFilesService
{
    @Path("/files/list")
    @POST
    public List<FileVO> listFiles(@QueryParam("path") String path)
    {
        // actual logic to fetch the files.
    }
}
{% endhighlight %}

Your corresponding RestyGWT interface would be:

<br/>
{% highlight java %}
public interface ListFilesService extends RestService
{
    @Path("/files/list")
    @POST
    public void listFiles(@QueryParam("path") String path, MethodCallback<List<FileVO>> callback);
}
{% endhighlight %}

Now if on the server the interface would change, let's assume a new `filter` parameter would be added, your service call
will fail at runtime, without giving you a chance to know this API contract is now invalidated at compile time.
<br/><br/>
Using the new `REST.withCallback().call()` API this issue is addressed. How does it work?
<br/><br/>
First you need now an interface that will extend the marker interface `DirectRestService` (not simply `RestService`):

<br/>
{% highlight java %}
public interface ListFilesService extends DirectRestService
{
    @Path("/files/list")
    @POST
    public List<FileVO> listFiles(@QueryParam("path") String path);
}
{% endhighlight %}

Secondly, your server will just implement it:

<br/>
{% highlight java %}
public class ListFilesServiceImpl implements ListFilesService
{
    @Override
    public List<FileVO> listFiles(String path)
    {
        // Logic to fetch the files. Annotations are being used from the interface, so there's no need to duplicate them here
    }
}
{% endhighlight %}

And third, client side, you will just declare it:

<br/>
{% highlight java %}
ListFilesService listFilesService = GWT.create(ListFilesService.class);
{% endhighlight %}

...and use it; since the calls are still async, you still need to provide a callback, and call it:

<br/>
{% highlight java %}
List<FileVO> noop = REST.withCallback(
    new MethodCallback<List<FileVO>>() {
        // callback implementation.
    }).call(listFilesService).listFiles("/mypath");
{% endhighlight %}

Because the service is called asynchronously, the response returned by the `listFiles()` method -- from
`call(listFilesService).listFiles("/mypath")` -- will always be null, and in this example
is being assigned just to enforce the check by the compiler of the return type as well.
<br/><br/>
The parameters are the actual parameters sent to the service, and the response will be received by the callback.
<br/><br/>
Since the same interface is reused, this is extremely well fit to changes, including service path,
response type or parameters changes. In case the API changes, the compilation will break.
<br/><br/>
Thus you basically have now statically typed JSON services, with JAX-RS/Jackson on the server side, and RestyGWT on the
client side.
