---
title: 7. Routing
permalink: /document/1.0.0-beta/routing
layout: article
is_doc: true
---

It's possible to map URLs by the configuration files for **Routing**.
URLs are restricted to the routes only declared in `route.yml`.

{% include toc.html %}

# Configuring A Route For Home Page

You have just one route and is for homepage after generating an application.

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

`WelcomeHandler` is a Handler that will be used on the above route.

<p class="tip">Handler is explained <a href="{{site.data.document.contents.handler.path}}">this chapter</a>.</p>

The above route matches `http://example.com` or `http://example.com/`.

`css` and `page-title` are custom fields. Custom fields will be assigned into your template files automatically.
It's possible to reference the above value by \{\{field-name}} in your template files. e.g. \{\{css}}, \{\{page-title}}

# The Required Directive

|name|type|description|default value|example value|
|:-|:-|:-|:-|
|**path**|string|A URL path pattern for a route.|-|/path/to/something|

The values on `path` directive will be converted to regular expression.

|value on path|converted to|
|:-|:-|
|/|/^\/?$/|
|/foo|/^\/foo$/|
|/path/to/:something/foo/:id|/^\/path\/to\/\w+/foo/\w+$/|


# The Optional Directives

`path` directive is the required though, there are some optional directives for routes.

|name|type|description|default value|example value|
|:-|:-|:-|:-|
|**execute**|string|A method to call in a route.|-|index|
|**method**|string|A HTTP request method which is accepted in a route.|-|post|
|**content**|string|A Content-type of response body. You must choose from following words, 'html', 'json', 'xml', 'plain'. Default is 'html'. If you set invalid words, The reponse body format will be set 'plain'.|html|json|
|**template**|string|A template name to render in a route.<br>e.g. setting 'home/mypage' to searching 'template/home/mypage.tpl'.|-|home/mypage|
|**layout**|string|A layout name.<br>e.g. setting 'common' to searching 'template/layout/common.tpl'.|-|common|
|**page_cache_time**|int|Setting an integer in seconds to turning on page caching. More information please see <a href="{{site.data.document.contents.page_cache.path}}">{{site.data.document.contents.page_cache.num}}. {{site.data.document.contents.page_cache.title}}</a>.|-|3600|
|**include**|string|Includes a group of the directives from route_include.yml.|-|common

# Configuring Multiple Pathes

It's possible to set multiple paths by regular expression.

### preference/environment_name/route.yml
{:class="cb-title"}

```yaml
FooHandler
    path: /(foo|bar|baz)
    execute: index
```
The above route matchs on following URLs `http://example.com/foo` or `http://example.com/bar` or `http://example.com/baz`.

It's possible to use the same Handler again any times you want.

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

# Configuring Routes

This is a tutorial to configure routes.

### 1\. First, setting a Handler class to use in a route.
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

<p class="tip"><code>path</code> directive is the required. It's supposed to be set in each of routes.</p>
<p class="tip"><code>path</code> is supposed to be started from slash.</p>


### 3\. Setting the Handler's method

{:class="cb-title"}
```yaml
FooHandler:
    path: /foo
    execute: do_something
```

You can set template and/or layout in your Handlers but it's possible to set them by the directives.

{:class="cb-title"}
```yaml
FooHandler:
    path: /foo
    execute: do_something
    template: article/index
    layout: default
```

Requesting `http://example.com/foo` matchs this route and the method named `foo` in `FooHandler` will be called.
`template/article/index.tpl` and `template/layout/default.tpl` will be rendered.

# Custom Fields

Anything not listed in the above directives are special. That will be assigned in your template files automatically.

### Example

### Setting Custom Fields
{:class="cb-title"}

```yaml
FooHandler:
    path: /foo
    name: Luna
    state: kittien
```
<p class="tip"><code>handler</code> is the reserved word for <code>route_error.yml</code>.</p>

### Referencing Custom Fields In Liquid templates
{:class="cb-title"}

{% raw %}
```
Output is => {{name}} is {{state}}.
```
{% endraw %}

The above string will be replaced to `Output is => Luna is kittien.`

### Referencing Custom Fields In Handler
{:class="cb-title"}

Custom Fields has been set in `@custom_fields`.

```ruby
class FooHandler < BaseHandler
    def index
        p @custom_fields #=> {"name"=>"Luna", "state"=>"kittien"}
    end
end
```

<p class="tip">Empty Hash object will be set in <code>@custom_fields</code> in case of no Custom Fields given.</p>


# URL Path Parameters

It's possible to get input from URL paths. Staring from `:` in `path` directive is special.

Just for instance,

### preference/environment_name/route.yml
{:class="cb-title"}

{% raw %}
```yaml
ArticleHandler:
    path: /article/:user_id/:id
    execute: index
    method: get
    content: json
```
{% endraw %}

There is a need to generate *ArticleHandler*.

### Creating ArticleHandler
{:class="cb-title"}

```bash
$ nekonote new handler article #=> handler/article.rb will be generated
```

The above route matches `http://example.com/article/99/2002`.

`Nekonote::Request` class object is set to a property named `@request`.
You can reference the values from URL by `@request.path_params`.

### handler/article.rb
{:class="cb-title"}

```ruby
class ArticleHandler < BaseHandler
    def index
        @request.path_params #=> {"user_id"=>"99", "id"=>"2002"}
        @request.path_params 'user_id' #=> 99
        @request.path_params :id #=> 2002   # symbol is OK as well
        @request.path_params 'not-defined' #=> nil
    end
end
```

# Configuring Common Preferences

`route_include.yml` is supposed to configure a group of some common preferences to configure routes in `route.yml`.

### preference/your_environment/route_include.yml
{:class="cb-title"}
```yaml
article:
    content: json
```

### preference/your_environment/route.yml
{:class="cb-title"}
```yaml
GetHandler:
    path: /get
    execute: get
    include: article

EditHandler:
    path: /add
    execute: add
    method: post
    include: article

EditHandler:
    path: /sort
    execute: sort
    method: post
    include: article
```

The directives in `route.yml` take precedence.

{% include doc-nav-link.html prev=site.data.document.contents.controlling_server next=site.data.document.contents.routing_err_page %}
