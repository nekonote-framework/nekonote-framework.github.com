---
title: 13. HTTP Request Parameters
permalink: /document/1.0.0-beta/params
layout: article
is_doc: true
---

`Nekonote::Request` is the class to manage **HTTP Request Parameters** such as form data, query string, URL path parameters.

{% include toc.html %}

# Nekonote::Request Class

An object of `Nekonote::Request` class will be set to `@request` in Handler classes.

The following is the list of **public instance methods** which defined in `Nekonote::Request` class.

|:-|:-|
[params](#paramsname--nil)|Returns values by the given name from the POST data or the query string.
[query_string](#querystringname--nil)|Returns values by the given name from the query string.
[post_data](#postdataname--nil)|Returns values by given name from the POST data.
[path_params](#urlparamsname--nil)|Returns values by the given name from the URL path parameters.
[payload](#payload)|Returns the payload as string.
[json_payload](#jsonpayload)|Returns the payload as hash.
[path](#path)|Returns the request path.
[uri](#uri)|Returns the request URI.
[method](#method)|Returns the HTTP request method.
[valid_str?](#validstrname)|Checks if the value of given parameter name is valid string.
[having?](#havingname)|Checks if the value of given parameter name exist in the POST data or the query string.

### params(name = nil)
{:class="ck-title"}

Returns values by the given name from the POST data or the query string.

<p class="tip">URL path parameters or payload can not be reference by this method.</p>

When HTTP request method is GET this method finds the value from the query string, when POST from the POST data.
 
|parameter|type|required|description
|:-|:-|:-|
|**name**|string or symbol|no|A parameter name to get.

### Return Values
It returns `hash` when just calling `@request.params`.
It return `string` when value found by the given parameter name when not returns `nil`.

### query_string(name = nil)
{:class="ck-title"}

This method is almost the same as `params`. The only deference is `query_string` tries to find the values from the query string.

### post_data(name = nil)
{:class="ck-title"}

This method is almost the same as `params`. The only deference is `post_data` tries to find the values from the POST data.

### path_params(name = nil)
{:class="ck-title"}

This method is almost the same as `params`. The only deference is that `path_params` tries to find the values from the URL path parameters.

### payload
{:class="ck-title"}
Returns the payload.

### Return Values
It returns `string`. In case of there is not any payload it returns `nil` instead.

### json_payload
{:class="ck-title"}
Returns the payload as Hash object when the payload is valid json data.

### Return Values
It returns `hash` when the payload is valid json data and succeeded parsing.

It returns `nil` when there is not any payload.

It returns `false` if unable to parse the payload as json.

### path
{:class="ck-title"}
Returns the request path.

### Return Values
It returns `string`.

### uri
{:class="ck-title"}
Returns the request URI.

### Return Values
It returns `string`.

### method
{:class="ck-title"}
Returns the HTTP request method.

### Return Values
It returns `string`.

### valid_str?(name)
{:class="ck-title"}

Checks if the value of given parameter name is `string` and is not empty which in the POST data or the query string.

|parameter|type|required|description
|:-|:-|:-|
|**name**|string or symbol|yes|A parameter name.

### Return Values
It returns `true` when the type of inspected value is `string` and is not empty string. If not it returns `false`.

### having?(name)
{:class="ck-title"}

Checks if the value of given parameter name exist in the POST data or the query string.

|parameter|type|required|description
|:-|:-|:-|
|**name**|string or symbol|yes|A parameter name.

### Return Values
It returns `true` when the specified parameter exists in POST data or query string.


# Getting Payload

When there is some request body in a request, you may reference it by `json_payload`.

### preference/your_environment/route.yml
{:class="cb-title"}
```yaml
ExampleHandler:
    path: /example
    execute: index
    method: POST
```

### handler/example.rb
{:class="cb-title"}
```ruby
class ExampleHandler < BaseHandler
    def example
        logwrite @request.json_payload
    end
end
```

When accessing `http://example.com/example` with `POST` method and with `Content-Type: application/json` header and with the request body below,

```yaml
{
    "id": 2002,
    "name": "example",
    "type": 1
}
```

`@request.json_payload` returns <code>{% raw %}{"id"=>2002, "name"=>"example", "type"=>1}{% endraw %}</code> in `hash`.

In order to reference payload which is not json data, please use `@request.payload` instead. It returns no parsed request body as json data.

{% include doc-nav-link.html prev=site.data.document.contents.user_conf next=site.data.document.contents.page_cache %}
