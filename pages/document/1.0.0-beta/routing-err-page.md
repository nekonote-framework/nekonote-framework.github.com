---
title: 8. Error Pages
permalink: /document/1.0.0-beta/routing-err-page
layout: article
is_doc: true
---
{{site.product}} provides routing for **Error Pages**.

{% include toc.html %}

# How It Works

If some exception raised on dealing with a request, the request is forwarded to the specified error page automatically.

# Routes for Error Page

There are the following four types routes for error page.

|field name|explaination
|:-|:-
|**missing_route**|This route is used when any route does not match a URL. You must set "path_as_regexp" directive to "true" which in route.yml to use this route.
|**wrong_http_method**|This route is used when some value is set to "method" directive in route.yml and a request HTTP method is not declared in that value.
|**fatal**|This route is used when a fatal error raised in program. The exception object is set to a property called @error in a Handler you specified.
|**not_found**|This route is used when a requested file dosen't exist under the "public" directory.

# Directives for Error Routes
Each route can be set the directives below. Almost directives are the same as `route.yml`.

|field name|description|default value|example value|
|:-|:-|:-|:-|
|**handler**|A handler class name used in that route. When some value is set to 'execute' directive in that route, you are supposed to set this directive.|-|ErrorHandler|
|**execute**|The same as route.yml.|-|index|
|**content**|The same as route.yml.|-|html|
|**template**|The same as route.yml.|-|example/index|
|**layout**|The same as route.yml.|-|example|
|**page_cache_time**|The same as route.yml.|-|3600|

<p class="tip">You may also set custom fields in <code>route_error.yml</code>.</p>

# Configuring Error Routes

The following routes are the default routing for error pages.

### preference/environment_name/route_error.yml
{:class="cb-title"}

```yaml
missing_route:
    handler: ErrorHandler
    execute: missing_route
    layout: error

wrong_http_method:
    handler: ErrorHandler
    execute: wrong_http_method
    layout: error

fatal:
    handler: ErrorHandler
    execute: fatal
    layout: error

not_found:
    handler: ErrorHandler
    execute: not_found
    layout: error
```

An application structure you generated will contain `ErrorHandler` class.
Let's see it.

There is `ErrorHandler` in `handler` directory. It's used in the above routes.

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

When a fatal error raised in your program, The request will be forwarded to the `fatal` route in `route_error.yml`.

<p class="tip">When <code>path_as_regexp</code> in <code>route.yml</code> is set to <code>false</code>, <code>missing_route</code> won't be used in any case.</p>

<p class="tip">On <code>not_found</code> route, a response body for it will be set to <code>Not Found: /path/to/request</code> with code 404.</p>

# ShowExceptions and 'fatal' Route
While a middleware called `ShowExceptions` is enabled, the request will not be forwarded to `fatal` route.

`ShowExceptions` shows exception details on your web blowser instead.
More information for exception handling is [{{site.data.document.contents.exception.num}}. {{site.data.document.contents.exception.title}}]({{site.data.document.contents.exception.path}}).

<p class="tip">Please try not to be raised any exception in the Handler class for <code>fatal</code> route.</p>


# Turning Off Error Pages

The routes for error pages are optional routes. You may turn off some of them.
All you have do is commenting out them.

### preference/your_environment/route_error.yml
{:class="cb-title"}

```yaml
#fatal:
#    handler: ErrorHandler
#    execute: fatal
#    layout: error
```


{% include doc-nav-link.html prev=site.data.document.contents.routing next=site.data.document.contents.handler %}
