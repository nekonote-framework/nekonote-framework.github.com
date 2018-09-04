---
title: 8. Error Routes
permalink: /document/1.0.0-beta/routing-err-page
layout: article
is_doc: true
---
{{site.product}} provides routing for **Error Pages**.

{% include toc.html %}

# Error Routes

There are the following four type's Error Routes.

|field name|explaination
|:-|:-
|**missing_route**|This route is used when any route does not match a URL.
|**wrong_http_method**|This route is used when a HTTP method in request does not much the value set in "method" directive in route.yml.
|**fatal**|This route is used when a fatal error raised in your program. The exception object will be set to the property named @error.
|**not_found**|This route is used when a request file does not exist under the "public" directory.

# Directives For Error Routes

Each route can be set the directives below. Almost directives are the same as `route.yml`.

|field name|description|default value|example value|
|:-|:-|:-|:-|
|**handler**|A handler class name to contorl the route. When some value is set to 'execute' directive, then this directive is required.|-|ErrorHandler|
|**execute**|The same as route.yml.|-|index|
|**content**|The same as route.yml.|-|html|
|**template**|The same as route.yml.|-|example/index|
|**layout**|The same as route.yml.|-|example|
|**page_cache_time**|The same as route.yml.|-|3600|

<p class="tip">You may also set custom fields in <code>route_error.yml</code>.</p>

# Configuring Error Routes

You can configure or turning off Error Routes on `route_error.yml`. 

The following is the default.

### preference/environment_name/route_error.yml
{:class="cb-title"}

```yaml
# This route is used when any route does not match a URL.
missing_route:
    handler: ErrorHandler
    execute: missing_route
    template: error
    layout: error

# This route is used when a HTTP method in request does not much the value set in "method" directive in route.yml.
wrong_http_method:
    handler: ErrorHandler
    execute: wrong_http_method
    template: error
    layout: error

# This route is used when a fatal error raised in your program.
# The exception object will be set to the property named @error.
fatal:
    handler: ErrorHandler
    execute: fatal
    template: error
    layout: error

# This route is used when a request file does not exist under the "public" directory.
not_found:
    handler: ErrorHandler
    execute: not_found
    template: error
    layout: error
```

Your application structure will contain `ErrorHandler` class in the `handler/` directory to control the above routes.

### handler/error.rb
{:class="cb-title"}

```ruby
class ErrorHandler < BaseHandler
    def missing_route
        @subject = 'Invalid URL'
        @msg     = 'The URL does not match any route'
        @detail  = %('#{Nekonote::Env.get :REQUEST_URI}' does not match any route.)
    end

    def wrong_http_method
        @subject = 'Unacceptable HTTP method'
        @msg     = 'You have accessed with the unacceptable HTTP method.'
    end

    def fatal
        @subject = 'Server Error'
        @msg     = 'You can not access this page temporary.'
    end

    def not_found
        @subject = 'No such resource'
        @msg     = %('#{Nekonote::Env.get :REQUEST_URI}' was not found on the server.)
    end

    # It will be called at last
    def __post
        # assign values to templates
        list = {}
        list['subject'] = @subject if defined?(@subject)
        list['msg']     = @msg     if defined?(@msg)
        list['detail']  = @detail  if defined?(@detail)
        __assign list

        # change the response code to not 500 but 200
        __set_code 200
    end
end
```

# ShowExceptions And 'fatal' Route
While `ShowExceptions` is enabled, the request will not be forwarded to `fatal` route. `ShowExceptions` shows exception details on your web blowser instead.
More information for exception handling is [{{site.data.document.contents.exception.num}}. {{site.data.document.contents.exception.title}}]({{site.data.document.contents.exception.path}}).

<p class="tip">Please try not to be raised any exception in the Handler class for <code>fatal</code> route.</p>


# Turning Off Error Routes

It's possible to comment out the directives then error routes does not be used.

### preference/your_environment/route_error.yml
{:class="cb-title"}

```yaml
#fatal:
#    handler: ErrorHandler
#    execute: fatal
#    layout: error
```

<p class="tip">While <code>not_found</code> route has not been set, the response will be output <code>Not Found: /path/to/request</code> with HTTP response code 404.</p>

{% include doc-nav-link.html prev=site.data.document.contents.routing next=site.data.document.contents.handler %}
