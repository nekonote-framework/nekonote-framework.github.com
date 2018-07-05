---
title: 10. Business Logic
permalink: /document/1.0.0-beta/business-logic
layout: article
is_doc: true
---

As previously mentioned in the previous chapter,
methods defined in Handlers are **Business Logic**.
You are supposed to write your program in there.

The following methods are predefined in Handlers.
Their names are starting with `__` as prefix to avoid duplicate name.

{% include toc.html %}

# The Callable Methods in Business Logic

|:-|:-|
[__root](#root)|Returns application root.
[__assign](#assignlist)|Assigns specific variables into tempaltes.
[__set_content_type](#setcontenttypetype)|Sets Content-Type header by a specific type.
[__set_header](#setheaderfield-value)|Sets a specific response header.
[__set_headers](#setheadersheaders)|Sets specific response headers.
[__add_header](#addheaderfield-value-delimiter---)|Adds a specific value to the existing header with a specific delimiter.
[__set_code](#setcodecode)|Sets HTTP response status code.
[__set_body](#setbodybody)|Sets specific data to HTTP message body for response.
[__set_json_body](#setjsonbodybody)|Sets specific data as json to HTTP message body for response.
[__add_body](#addbodybody)|Adds specific data to the existing HTTP message body.
[__env_get](#envgetfield--nil)|Returns a value of Rack environment from a specific field name.
[__env_keys](#envkeys)|Returns the list of field names for __env_get.
[__setting_get](#settinggetfields)|Returns values from user configuration files by some specific field names.
[__setting_keys](#settingkeys)|Returns the list of top level field names from user setting files for __setting_get.
[__set_template](#settemplatetemplate)|Sets template.
[__set_layout](#setlayoutlayout)|Sets layout.
[__redirect](#redirectdest-code302)|Issues HTTP redirect.

<p class="tip">The above methods are defined as instance methods and its accessibilities are <code>protected</code>.</p>

### __root
{:class="ck-title"}

It returns a string value as the application root.

### __assign(list)
{:class="ck-title"}

Assigns specific variables into tempaltes.

parameter|type|required|description
:-|:-|:-
list|hash|yes|Variable list to assign into templates.

### __set_content_type(type)
{:class="ck-title"}

Sets `Content-Type` header by a specific type.
The configuration of `content` directive in `route.yml` will be overwritten after calling this method.

|parameter|type|required|description|
|:-|:-|:-|
|**type**|string or symbol|yes|Type of content to set to Content-Type header. It's supposed to be chosen from 'html', 'json', 'xml', 'plain' (These are the same as 'content' directive in route.yml).|

### e.g.
{:class="cb-title"}

```ruby
class ExampleHandler < BaseHandler
    def index
        # The response header 'Content-Type:application/json' will be set
        __set_content_type :json
    end
end
```

### __set_header(field, value)
{:class="ck-title"}

Sets a specific response header. If the specified field name has already been set this method will overwrite it to the new value.

|parameter|type|required|description|
|:-|:-|:-|
|**field**|string or symbol|yes|A response header field name.|
|**value**|string|yes|A value to set to the specified header.|

### e.g.
{:class="cb-title"}

```ruby
class ExampleHandler < BaseHandler
    def index
        # The response header 'X-XSS-Protection:1;mode=block' will be set
        __set_header 'X-XSS-Protection', '1;mode=block'
    end
end
```

### __set_headers(headers)
{:class="ck-title"}

Sets specific response headers. The difference in `__set_header` is `__set_headers` can set any number of headers at the same time.

If the specified field name has already been set this method will overwrite it to the new value.

|parameter|type|required|description|
|:-|:-|:-|
|**headers**|hash|yes|A list of headers to set. The format is **{field => value ,..}**.|

### e.g.
{:class="cb-title"}

```ruby
class ExampleHandler < BaseHandler
    def index
        # The response headers 'Foo:1', 'Bar:2' will be set
        headers = {
            :Foo => 1,
            :Bar => 2
        }
        __set_headers headers
    end
end
```

### __add_header(field, value, delimiter = '; ')
{:class="ck-title"}

Adds a specific value to the existing header with a specific delimiter.

The specific header will be set anew if no header matched the specific field.

|parameter|type|required|description|
|:-|:-|:-|
|**field**|string or symbol|yes|A response header field name to add a specific value.|
|**value**|string|yes|A value to add to the specified header.|
|**delimiter**|string|no|A delimiter between value and value. The default is **'; '**.|

### e.g.
{:class="cb-title"}

```ruby
class ExampleHandler < BaseHandler
    def index
        __set_header :Foo, 'Existing value'
        __add_header :Foo, 'Add a new value'
        #=> Response header 'Foo:Existing value; Add a new value' will be set
    end
end
```

### __set_code(code)
{:class="ck-title"}

Sets HTTP response status code.

|parameter|type|required|description|
|:-|:-|:-|
|**code**|int|yes|A HTTP response status code to set.|

### e.g.
{:class="cb-title"}

```ruby
class ExampleHandler < BaseHandler
    def index
        # 'Status Code:401 Unauthorized' will be set
        __set_code 401
    end
end
```

### __set_body(body)
{:class="ck-title"}

Sets specific data to HTTP message body for response.

<tip>This method replaces the existing HTTP message body to specified data. Any templates won't be used when you set HTTP message body through this method.</tip>

|parameter|type|required|description|
|:-|:-|:-|
|**body**|string|yes|A HTTP message body to set.|

### e.g.
{:class="cb-title"}

```ruby
class ExampleHandler < BaseHandler
    def index
        require 'json'
        response = {
            'foo' => 1,
            'bar' => 2
        }
        __set_content_type :json
        __set_body response.to_json
    end
end
```
The reponse body must be `{"foo":1,"bar":2}`.

### __set_json_body(body)
{:class="ck-title"}

Sets specific data as json to HTTP message body for response.
The parameter `body` requires that it must be set as `hash` type.

The given `body` will be converted to json format string by `JSON.pretty_generate` method of `json` module in the standard library.

|parameter|type|required|description|
|:-|:-|:-|
|**body**|hash|yes|A HTTP message body to set.|

### e.g.
{:class="cb-title"}

```ruby
class ExampleHandler < BaseHandler
    def index
        response = {
            'foo' => 1,
            'bar' => 2
        }
        __set_content_type :json
        __set_json_body response
    end
end
```
The reponse body must be `{"foo":1,"bar":2}`.


### __add_body(body)
{:class="ck-title"}

Adds specific data to the existing HTTP message body.

The specific data will be set anew to HTTP message body if no HTTP message has not been set.

|parameter|type|required|description|
|:-|:-|:-|
|**body**|string|yes|A HTTP message body to add.|

### e.g.
{:class="cb-title"}

```ruby
class ExampleHandler < BaseHandler
    def index
        __set_body 'Foo'
        __add_body 'Boo'
    end
end
```
The reponse body must be `FooBoo`.

### __env_get(field = nil)
{:class="ck-title"}

Returns a value of Rack environment from a specific field name.

|parameter|type|required|description|
|:-|:-|:-|
|**field**|string or symbol|yes|A filed name to get from Rack environment.|

### e.g.
{:class="cb-title"}

```ruby
class ExampleHandler < BaseHandler
    def index
        # Get The User Agent
        ua = __env_get :HTTP_USER_AGENT
    end
end
```

<p class="tip">This method is the alias for the class method <code>Nekonote::Env.get</code>.</p>

### __env_keys
{:class="ck-title"}

Returns the list of field names for `__env_get`. It returns `array`.

<p class="tip">This method is the alias for the class method <code>Nekonote::Env.keys</code>.</p>

### __setting_get(\*fields)
{:class="ck-title"}

Returns values from user configuration files by some specific field names.
The return values depend on your user configuration files and the given values.

<p class="tip">User configuration files are explained <a href="{{site.data.document.contents.user_conf.path}}">this chapter</a>.</p>

|parameter|type|required|description|
|:-|:-|:-|
|**field**|string or symbol|yes|A field name to get the value from user configuration files. It's allowed to give any number of field names by comma.|

### preference/your_environment/example.yml
{:class="cb-title"}

```
path:
    foo: /path/to/foo
    bar: /path/to/bar
```

### handler/foo.rb
{:class="cb-title"}

```ruby
class ExampleHandler < BaseHandler
    def index
        # It returns array {"foo"=>"/path/to/foo", "bar"=>"/path/to/bar"}
        path_list = __setting_get :path

        # It returns string /path/to/bar
        path_bar = __setting_get :path, :bar
    end
end
```

### __setting_keys
{:class="ck-title"}

Returns the list of top level field names from user setting files for `__setting_get`. It returns `array`.


### __set_template(template)
{:class="ck-title"}

Sets template. It overwrites configurations in `route.yml`.

|parameter|type|required|description|
|:-|:-|:-|
|**template**|string|yes|a relative path to template from `template/` directory. Value should be set without '.tpl' like `template` directive in `route.yml`.


### __set_layout(layout)
{:class="ck-title"}

Sets layout. It overwrites configurations in `route.yml`.

|parameter|type|required|description|
|:-|:-|:-|
|**layout**|string|yes|a relative path to layout from `template/layout/` directory. Value should be set without '.tpl' like `layout` directive in `route.yml`.


### __redirect(dest, code=302)
{:class="ck-title"}

Issues HTTP redirect.

|parameter|type|required|description|
|:-|:-|:-|
|**dest**|string|yes|Destination to redirect to.|
|**code**|int|no|A HTTP status code for redirection. The default is 302 and the meaning of it is '302 Found'.|

When you would like to redirect in the middle of program, you must use `return` as the following example.

```ruby
class ExampleHandler < BaseHandler
    def index
        # your program here

        return __redirect 'http://example.com'

        logwrite 'program after redirect'
    end
end
```

The above example redirects the request to the destination consisting of URI scheme and FQDN.

If you would like to redirect another page in the same FQDN, just giving the path to the page as the following example.

```ruby
class ExampleHandler < BaseHandler
    def index
        __redirect 'path/to/some/page'
    end
end
```

The above example redirects the request to `/path/to/some/page` in the same URI scheme and FQDN.

# Predefined Properties in Handlers

The following properties are predefined in Handler classes. You may use them when you need it.

property name|type|description
:-|:-|:-
**@request**|Nekonote::Request|This variable will be set an object of Nekonote::Request. It's used to get HTTP request parameters.
**@view**|Nekonote::View|This variable will be set an object of Nekonote::View.
**@session**|Rack::Session::Abstract::SessionHash or null|This varibale is set an object of Rack::Session::Abstract::SessionHash when the session management is set enabled in middlewares.rb. It's set nil if the session management is set disabled.
**@error**|some Exception|This varibale will be set an object of some Exception class when a fatal error raised. It will be set nil if no exception raised in program.
**@custom_fields**|hash|This varibale will be set the custom feilds in that route. It will be set an empty hash object even if any custom feild doesn't exist.

# Predefined Public Methods in Handlers

These methods are special. You are not supposed to overwrite them.

method name|description
:-|:-
call|Rack required to implement this method.

{% include doc-nav-link.html prev=site.data.document.contents.handler next=site.data.document.contents.rack_env %}
