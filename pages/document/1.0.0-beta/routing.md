---
title: 7. Routing
permalink: /document/1.0.0-beta/routing
layout: article
is_doc: true
---

It's possible to map URLs by the configuration file for **Routing**.
URLs are restricted to the routes only declared in `route.yml`.

{% include toc.html %}

# Configuring A Route for Home Page

After generating an application structure was, You will only have the route to home page.

### preference/your_environment/route.yml
{:class="cb-title"}
```yaml
WelcomeHandler:
    path: /
    execute: index
    template: welcome/index
    css: /css/welcome.css
    page-title: Welcome
```

`WelcomeHandler` is Handler that will be used on the above route.

<p class="tip">Handler is explained <a href="{{site.data.document.contents.handler.path}}">this chapter</a>.</p>

The above route matches on `http://example.com` or `http://example.com/`.
That's because the value of `path` directive is set to `/`.

You are supposed to set a method name to the `execute` directive.
The method will be called when the route matches on a request.

A template file named `static/template/welcome/index.tpl` will be used on the above route.

In case of a layout file hasn't been set, the default layout will be used. It is `static/layout/default.tpl`.

The above `css` and `page-title` are custom fields. Custom fields are assigned into templates automatically.
It's possible to reference the above value writing \{\{field-name}} in templates. e.g. \{\{css}}, \{\{page-title}}

<p class="tip">You can't overwrite existing custom fields by calling <code>__assgin</code> method.
The custom fields will not be overwritten even if calling it.</p>

# Configuring Routes

I'll show you the way to configure routes.

### 1\. First, set a Handler class using in a route.
{:class="cb-title"}

```yaml
FooHandler:
```

<p class="tip">There is a need to create <code>FooHandler</code>, but let's put that aside for now.</p>

### 2\. Filling 'path' directive
{:class="cb-title"}
```yaml
FooHandler:
    path: /foo
```

<p class="tip">'path' is the required directive. It's supposed to be set in each of routes.</p>
<p class="tip">'path' is supposed to begin with slash as <code>/path/to/something</code>.</p>

`path` is the required but there are optional directives.
The following is optional directives which can be set in each of routes.

# The Optional Directives

|name|type|description|default value|example value|
|:-|:-|:-|:-|
|**execute**|string|The method to call when the route matches on a request. If this directive is not specified no method will be called.|-|index|
|**method**|string|Acceptable HTTP method names in the route. The default is null and all request HTTP methods will be accepted. It's possible to set any number of values by comma.|-|POST, GET|
|**params**|string|Configuring the request parameters. The default is null and that means all request parameters are accpected. The format is,<br />"**parameter_name: {int\|string\|array\|float\|bool}**"|-|foo=int, bar=string, baz=bool|
|**content**|string|Configuring format of response body. You must choose from following words, 'html', 'json', 'xml', 'plain'. Default is 'html'. If you set invalid words, The reponse body format will be set 'plain'.|html|json|
|**template**|string\|false|A template name. The default is made form class name of Handler.<br />e.g. _FooHandler_ will use _static/template/foo.tpl_ for the default.<br />When you specified some template name here and if it doesn't exist or not readable the exception will raise. false to no template will be used.|*It depends on class name*|home/mypage|
|**layout**|string\|false|A layout name. The default is 'default' and it indicates _static/layout/default.tpl_. If the default layout doesn't exist or not readable, layout is not used. When you specified some layout name and if it doesn't exist or not readable the exception will raise. false to no layout will be used.|default|common|
|**page_cache_time**|int|Page caching will be turned on when the value here is set to some number. Please set a ache time in seconds. More information please see <a href="{{site.data.document.contents.page_cache.path}}">{{site.data.document.contents.page_cache.num}}. {{site.data.document.contents.page_cache.title}}</a>.|-|3600|
|**include**|string|Configuring the directives from route_include.yml.|-|common

# Multiple Pathes

It's possible to set multiple paths by using regular expression.

### preference/environment_name/route.yml
{:class="cb-title"}

```yaml
FooHandler
    path: /(foo|bar|baz)
    execute: index
```
The above route matchs on following URLs `http://example.com/foo` or `http://example.com/bar` or `http://example.com/baz`.

<p class="tip">There is a need to set <code>path_as_regexp: true</code> in route.yml for using regular expression in <code>path</code> directive.</p>

It's possible to use the same Handler again any times you want.

Configuring the above route without regular expression is as follows.

```yaml
FooHandler
    path: /foo
    execute: index

FooHandler
    path: /bar
    execute: index

FooHandler
    path: /baz
    execute: index
```

# Custom Fields

In case of there is a directive except thouse mentioned above, it's assumed as custom field.

### Example

### Setting Custom Fields
{:class="cb-title"}

```yaml
FooHandler:
    path: /foo
    name: Luna
    state: kittien
```
<p class="tip"><code>handler</code> is the reserved word. It's not possible to set it as custom fields.</p>

### Calling Custom Fields in Liquid templates
{:class="cb-title"}

{% raw %}
```
Output is => {{name}} is {{state}}.
```
{% endraw %}

The above will be replaced to `Output is => Luna is kittien.`

### Calling Custom Fields in Handler
{:class="cb-title"}

```ruby
class FooHandler < BaseHandler
    def index
        p @custom_fields #=> {"name"=>"Luna", "state"=>"kittien"}
    end
end
```

<p class="tip">Even if there is no custom field, the property <code>@custom_fields</code> will be set an empty Hash object.</p>

# Evaluating 'path' directive as Regular Expression

`path_as_regexp` is directive for fixing whether value of `path` directive should be evaluated as regular expression or not.

* true to evaluate value of `path` as a regular expresion.
* false to evaluate value of `path` as a string.

The default value of `path_as_regexp` is `false`.

# Route Options

## path_as_regexp

### Setting to evaluate 'path' directive as regular expression
{:class="cb-title"}

```yaml
preference:
    path_as_regexp: true
```

`^/` will be put to beginning automatically.

It means, `path: /example$` will converted to Regexp object `/^\/example$/`.

### Regular Expression Options

You may set the following regular expression options `i`, `x`, `m`, `n` after a slash at last.

e.g. `/+example$/in` will be converted to the Regexp object `/^\/+example$/in`

<p class="tip">You can not set o, e, s, u options.</p>

When there is unknown regexp option, it will not evaluated as option.

e.g. `/+example$/ixu` will be converted to the Regexp object `/^\/+example$\/ixu/`

## allow_dup_slash

`allow_dup_slash` directive fixing whether routes match on URLswhich  contained duplicate slashes.

* true to routes will match even if a URL contains duplicate slashes such as `http://example.com//path/to//some`.
* false to does not allow duplicate slashes.

The default value of `allow_dup_slash` is `false`.

# Setting Routes with URL path parameters
There is no need to rewrite URLs for URL path parameters in web server side.

Here is showing you how to set up REST like URLs.

At first, you have to set `path_as_regexp` to `true` in `route.yml` for it.

URL path parameters are marked with `:` like the following.

### preference/environment_name/route.yml
{:class="cb-title"}

{% raw %}
```yaml
ArticleHandler:
    path: /article/:user_id/:id
    execute: index
    method: GET
    template: article
```
{% endraw %}

`user_id` or `id` could be replaced a variable on URLs.

Bring to the next, there is a need to create a Handler named *ArticleHandler*.

### Creating ArticleHandler
{:class="cb-title"}

```bash
$ nekonote new handler article #=> handler/article.rb will be created
```

That's all to configure routes included variables.

The above route matches on `http://example.com/article/99/2002`.

## Referencing URL Path Parameters

It's possible to reference the values which passed by a URL by calling `Nekonote::Request#path_params` method.

`Nekonote::Request` class object is set to a property named `@request`

### handler/article.rb
{:class="cb-title"}

```ruby
class ArticleHandler < BaseHandler
    def index
        @request.path_params #=> {"user_id"=>"99", "id"=>"2002"}
        @request.path_params 'user_id' #=> 99
        @request.path_params 'id' #=> 2002
        @request.path_params 'not-defined' #=> nil
    end
end
```

## Calling the values from Templates

When you would like to reference the values in templates, you have to assign them by yourself.

You need to set `request.path_params` to the argument of `__assign` method for it.

### handler/article.rb
{:class="cb-title"}

```ruby
class ArticleHandler < BaseHandler
    def index
        # assign them to templates
        __assign request.path_params
    end
end
```

### static/template/article.tpl
{:class="cb-title"}

{% raw %}
```
User ID: {{user_id}}<br />
Article ID: {{id}}
```
{% endraw %}

The result must say the following on blowser,

```
User ID: 99
Article ID: 2002
```

# Configuring Common Preferences

`route_include.yml` is supposed to configure a group of some common preferences to configure routes in `route.yml`.

### preference/your_environment/route_include.yml
{:class="cb-title"}
```yaml
common:
    params: id=int
    content: json
    template: false
    layoute: false
```
The directives which in the group named `common` can be configured by `include: common` in `route.yml`.

Now, It's time to edit your `route.yml`

### preference/your_environment/route.yml
{:class="cb-title"}
```yaml
GetHandler:
    path: /get
    execute: get
    method: GET
    include: common

EditHandler:
    path: /add
    execute: add
    method: POST
    include: common

EditHandler:
    path: /sort
    execute: sort
    method: POST
    include: common
```

The above will be converted as follows,

```yaml
GetHandler:
    path: /get
    execute: get
    method: GET
    params: id=int
    content: json
    template: false
    layoute: false

EditHandler:
    path: /add
    execute: add
    method: POST
    params: id=int
    content: json
    template: false
    layoute: false

EditHandler:
    path: /sort
    execute: sort
    method: POST
    params: id=int
    content: json
    template: false
    layoute: false
```

<p class="tip">Setting custom fields in <code>route_include.yml</code> is possible as well.</p>

## In case of there is the same directives between route.yml and route_include.yml

The values in `route.yml` overwrite the values in `route_include.yml` except `method` or `params` directives.

The values of `method` or `params` directives are merged if it's duplicate.

### Example

### preference/your_environment/route_include.yml
{:class="cb-title"}
```yaml
common:
    method: GET
    params: id=int
    content: json
    template: false
    layoute: false
```

### preference/your_environment/route.yml
{:class="cb-title"}
```yaml
GetHandler:
    path: /get
    execute: get
    method: POST
    params: type=int
    include: common
```

In the above case, the both values of `method` directive will be merged and the value of `method` will be `POST, GET`,
the value of `params` directive will be `params: type=int, id=int`.

{% include doc-nav-link.html prev=site.data.document.contents.controlling_server next=site.data.document.contents.routing_err_page %}