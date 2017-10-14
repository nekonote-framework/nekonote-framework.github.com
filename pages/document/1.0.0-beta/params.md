---
title: 13. HTTP Request Parameters
permalink: /document/1.0.0-beta/params
layout: article
is_doc: true
---

`Nekonote::Request` is the class to manage **HTTP Request Parameters** such as form data, query string, URL path parameters.

There is the filter to sanitize HTTP Request parameters.

{% include toc.html %}

# Nekonote::Request class

The property named `@request` will be set an object of `Nekonote::Request` class.

The following is the list of **public instance methods** in `Nekonote::Request` class.

|:-|:-|
[params](#paramsname--nil)|Returns sanitized values by the given name from the POST data or the query string.
[params_raw](#paramsrawname--nil)|Returns raw values by the given name from the POST data or the query string.
[query_string](#querystringname--nil)|Returns sanitized values by the given name from the query string.
[query_string_raw](#querystringrawname--nil)|Returns raw values by the given name from the query string.
[post_data](#postdataname--nil)|Returns sanitized values by given name from the POST data.
[post_data_raw](#postdatarawname--nil)|Returns raw values by the given name from the POST data.
[path_params](#urlparamsname--nil)|Returns sanitized values by the given name from the URL path parameters.
[path_params_raw](#urlparamsrawname--nil)|Returns raw values by the given name from the URL path parameters.
[payload](#payload)|Returns the payload as string.
[json_payload](#jsonpayload)|Returns the payload as hash.
[path](#path)|Returns the request path.
[uri](#uri)|Returns the request URI.
[method](#method)|Returns the HTTP request method.
[valid_str?](#validstrname)|Checks if the value of given parameter name is valid string.
[having?](#havingname)|Checks if the value of given parameter name exist in the POST data or the query string.

### params(name = nil)
{:class="ck-title"}

Returns sanitized values by the given name from the POST data or the query string.

The values will be sanitized by your specification in `route.yml`. The query string will be URL decoded.

<p class="tip">URL path parameters or payload will not be set.</p>

<p class="tip">The HTTP request method is GET: <code>params</code> finds out the value from the query string.<br />
The HTTP request method is POST: <code>params</code> finds out the value from the POST data.</p>

|parameter|type|required|description
|:-|:-|:-|
|**name**|string or symbol|no|The searched name from the POST data or the query string.

### Return Values:

The return type depends on your configuration in `route.yml` when the given parameter name exists in the POST data or the query string.

It returns `nil` if the given parameter name is not found in the POST data nor the query string.

It returns all request parameters as `hash` in case of the given `name` is not `string` nor `symbol`


### params_raw(name = nil)
{:class="ck-title"}

This method is almost the same as `params`. The only deference is `params_raw` returns the raw values.

### query_string(name = nil)
{:class="ck-title"}

This method is almost the same as `params`. The only deference is `query_string` tries to find the values from the query string.

Request values are URL decoded.

### query_string_raw(name = nil)
{:class="ck-title"}

This method is almost the same as `query_string`. The only deference is `query_string_raw` returns the row values.

### post_data(name = nil)
{:class="ck-title"}

This method is almost the same as `params`. The only deference is `post_data` tries to find the values from the POST data.

### post_data_raw(name = nil)
{:class="ck-title"}

This method is almost the same as `post_data`. The only deference is `post_data_raw` returns the raw values.

### path_params(name = nil)
{:class="ck-title"}

This method is almost the same as `params`. The only deference is that `path_params` tries to find the values from the URL path parameters.

### path_params_raw(name = nil)
{:class="ck-title"}

This method is almost the same as `path_params`. The only deference is that `post_data_raw` returns the raw values.

### payload
{:class="ck-title"}
Returns the payload.

### Return Values:
It returns `string`. In case of there is not any payload it returns `nil` instead.

### json_payload
{:class="ck-title"}
Returns the payload as Hash object when the payload is valid json data.

### Return Values:
It returns `hash` when the payload is valid json data and succeeded parsing.

It returns `nil` when there is not any payload.

It returns `false` if unable to parse the payload as json.

### path
{:class="ck-title"}
Returns the request path.

### Return Values:
It returns `string`.

### uri
{:class="ck-title"}
Returns the request URI.

### Return Values:
It returns `string`.

### method
{:class="ck-title"}
Returns the HTTP request method.

### Return Values:
It returns `string`.

### valid_str?(name)
{:class="ck-title"}

Checks if the value of given parameter name is `string` and is not empty which in the POST data or the query string.

|parameter|type|required|description
|:-|:-|:-|
|**name**|string or symbol|yes|A parameter name.

### Return Values:
It returns `true` when the type of inspected value is `string` and is not empty string. If not it returns `false`.

### having?(name)
{:class="ck-title"}

Checks if the value of given parameter name exist in the POST data or the query string.

|parameter|type|required|description
|:-|:-|:-|
|**name**|string or symbol|yes|A parameter name.

### Return Values:
It returns `true` when the specified parameter exists in POST data or query string.


# Sanitizing Request Parameters by Expected Types

Each parameter declared in `route.yml` can be specified its expected type.

For example:

### preference/your_environment/route.yml
{:class="cb-title"}
```yaml
ExampleHandler:
    path: /example
    execute: index
    params: id=int
```

The above route restricts request parameters to a parameter named `id` only, and the value is casted to `int`.

All parameters declared in `params` directive are forcibly casted to their expected types.

### handler/example.rb
{:class="cb-title"}
```rb
class ExampleHandler < BaseHandler
    def index
        p @request.params :id
    end
end
```

The above exmaple code will be called when accessing to the URL `http://example.com/example?id=2002`.
The return value of `@request.params :id` is `2002` and its type is `integer`.

When accessing to the URL `http://example.com/example?id=injected-string`, The return value of `@request.params :id` is `0`, because the expected type is `integer`.

# Removing Waste Parameters

HTTP request parameters are restricted to just one parameter only which named `id` in the following route.

### preference/your_environment/route.yml
{:class="cb-title"}
```yaml
ExampleHandler:
    path: /example
    execute: index
    params: id=int
```

Sanitized values doesn't include any extra parameter.

It's possible to get no sanitized values by `@request.params_raw` or the other \*_raw methods.

### Case: Request to http://example.com/example?id=2002&waste=foo&waste2=bar
```rb
class ExampleHandler < BaseHandler
    def index
        @request.params # It returns => {"id"=>2002}
        @request.params_raw # It returns =>  {"id"=>"2002", "waste"=>"foo", "waste2"=>"bar"}
    end
end
```

# Filling for Missing Parameters

If some parameters are missing which declared in `route.yml`, they will be filled as sanitized parameters.

In the above case, `id` is required parameter name.
So `id` will be filled even if `id` is missing in request parameters.

### Case: Request to http://example.com/example
```rb
class ExampleHandler < BaseHandler
    def index
        @request.params # It returns => {"id"=>nil}
        @request.params_raw # It returns => {}
    end
end
```
There is no `id` in parameters, but `id` was filled as its value is `nil`.
But `params_raw` doesn't include it, because `params_raw` returns raw request parameters.

# Getting Values From URL Path

When some variable is set in `path` directive in `route.yml`, it's possible to get values from the URL by `path_params`.

Variables can be decleared by colon as value of `path` directive such as `:var_name`.

### preference/your_environment/route.yml
{:class="cb-title"}
```yaml
ExampleHandler:
    path: /example/:user_id/:article_id
    execute: index
    params: user_id=int, article_id=int
```

### Case: Request to http://example.com/example/2002/1
```rb
class ExampleHandler < BaseHandler
    def index
        @request.params # => {}
        @request.params_raw # => {}
        @request.path_params # => {"user_id"=>2002, "article_id"=>1}
        @request.path_params_raw # => {"user_id"=>"2002", "article_id"=>"1"}
    end
end
```

# Restricting Request by HTTP Request Method

By the default any HTTP request methods are acceptable. It's possible to restrict HTTP request method to an expected method by declaring in `route.yml`.

### preference/your_environment/route_error.yml
{:class="cb-title"}

At first, you are supposed to set the route that matches a request using wrong HTTP request method.

### preference/your_environment/route_error.yml
{:class="cb-title"}
```yaml
wrong_http_method:
    handler: ErrorHandler
    execute: wrong_http_method
    layout: error
    css: /css/layout/error.css
```

Next restricting request method to `POST` only.

### preference/your_environment/route.yml
{:class="cb-title"}
```yaml
ExampleHandler:
    path: /example
    execute: index
    params: id=int
    method: POST
```

Now ready to access the web server. Starting the web server and accessing to the URL `http://example.com/example` using `GET` method.

GET Requests to `http://example.com/example` will be forwarded to the route `wrong_http_method`. Because that route accepts `POST` requests only.

When accessing the URL as a POST request, the request is accepted and `ExampleHandler#index` will be called.

It's possible to write any number of request methods. The way for it is just putting comma between value and value of `params` directive as following.

### preference/your_environment/route.yml
{:class="cb-title"}
```yaml
ExampleHandler:
    path: /example
    execute: index
    params: id=int
    method: POST, GET
```

Now, `POST` or `GET` are acceptable in the above route.


# Getting Payload

When there is some request body in a request, you may reference it by `json_payload`.

<p class="tip">Payload will not be sanitized as the others.</p>

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

When accessing `http://example.com/example` using `POST` method and with `Content-Type: application/json` header and with the request body below,

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
