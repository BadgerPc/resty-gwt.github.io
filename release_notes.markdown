---
layout: default
title: RestyGWT / Release Notes
slogan: Enjoy it!
---

## Version 2.0.1

	* fixes problem with JavaSourceCategory no longer being public in GWT-2.7.0
	* Generated Code fix: The "next" method must have only one return statement.
	* JsonTypeInfo and JsonSubTypes annotations must not be searched on current class only. Theses annotations must be searched on the current class, on all parent classes and interfaces.
	* Allow specifying timezone for date/time string conversions
	* fixing issue with IE sending "undefined" in the body of POST, PUT or DELETE request if no data is set to the request
	* in ServiceRoots: urls ending with slashes are properly handled now
	* add XMLTransient as as supported annotation to ignore fields and methods

## Version 2.0

	* create ExceptionMapper class to allow the application to customize how the exceptions are created
	* Update Jackson annotations to version 2.4
	* Use default timeout only if it was not already set through the @Options(timeout =) annotation.
	* Add support for JacksonAnnotationsInside.
	* Allow abstract methods in Enums

## Version 1.5

	* allow to use base64 byte arrays if configured in Defaults
	* do not url-escape path-params which are already a complete url
	* add support for @JsonValue on enums
	* service root urls can now be set for several services with the ServiceRoots class
	* Enums in @FormParams should not be enclosed in quotes
	* Handling QueryListTypes in Forms
	* handle Collections as Lists in QueryParams and in FormParms
	* Allow collections as keys in maps
	* handle Collections as Lists in QueryParams and in FormParams
	* List, Set, and OrderedSet in form should be handled as per jax-rs standard
	* add collection support for interfaces
	* add Dispatcher to subresource
	* Maps: Don't decode key if map key type is String
	
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
