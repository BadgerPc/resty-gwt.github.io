---
layout: default
title: RestyGWT / Release Notes
slogan: Enjoy it!
---

## Version 1.4

    * fix generated enum serializer with @JsonSubTypes.
    * better support decoding of Long using strings
    * fix getField can throw assertion error
	* added custom serializer generators configurable via module XML
	* supports normal inheritance, subtypes can be listed recursively
	* fix naming for inner classes
    * getters and setters do not need a matching field
	* @JsonIgnore works on fields or the setter or the getter
	* allow Attribute annotation expression to return null value
	* encode/decode nested arrays, lists, sets and maps as fields or property of a pojo
	* move all GWT.log to use java.util.logging.Logger
	* make the URLencode working with null values as well
	* url encode PathParam
	* Support default value for @JsonTypeInfo property
	* Add support for JsonDeserialize for interfaces
	* allow RestService to return a JsonpRequest/Request, can be used to cancel the request
	* Fixed 204 not handled roperly for MSIE 8 and 9
	* reduces overall logging
	* Fixed serializing lists and arrays using @FormParams.
	* make the resource creation lazy inside restService impl - helps GIN setup
	* let rest-service use default dispatcher from Defaults when none is set on interface
	* added DirectRestService interfaces - allow to reuse interface from server and call them in async manner from the client.
