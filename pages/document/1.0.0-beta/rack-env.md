---
title: 11. Environment Variables
permalink: /document/1.0.0-beta/rack-env
layout: article
is_doc: true
---
`Nekonote::Env` class provides the way to get **Environment Variables**.

The detail for environment variables is please see the following link, <a href="http://www.rubydoc.info/github/rack/rack/master/file/SPEC" target="_blank">the documentation of Rack</a>.

{% include toc.html %}

# Nekonote::Env class

`Nekonote::Env` is a class to get some value of environment variable.

Here is a list of the class methods defined in `Nekonote::Env` class.

|:-|:-|
[Nekonote::Env.get](#nekonoteenvgetkeyname)|Returns the value by a specific key_name.
[Nekonote::Env.keys](#nekonoteenvkeys)|Returns a list of keys for Nekonote::Env.get.
[Nekonote::Env.get_all](#nekonoteenvgetall)|Returns all environment variable.
[Nekonote::Env.root](#nekonoteenvroot)|Returns the application root.
[Nekonote::Env.current](#nekonoteenvcurrent)|Returns the current environment (NEKONOTE_ENV).

### Nekonote::Env.get(key_name)
{:class="ck-title"}

Returns the value by a specific `key_name`.

|parameter|type|required|description|
|:-|:-|:-|
|**key_name**|string or symbol|yes|A key name (variable name) to get from environment variables.|

### Return Values:
It returns `string` when the given `key_name` was found.

It returns `nil` if the given `key_name` was not found or given no `key_name`.

### e.g.

```ruby
# Get the query string
Nekonote::Env.get :QUERY_STRING
```

### Nekonote::Env.keys
{:class="ck-title"}

Returns a list of keys for `Nekonote::Env.get`.

### Return Values:
It returns `array`.

### Nekonote::Env.get_all
{:class="ck-title"}

Returns all environment variable.

### Return Values:
It returns `hash`.

### Nekonote::Env.root
{:class="ck-title"}

Returns the application root.

### Return Values:
It returns `string`.

### Nekonote::Env.current
{:class="ck-title"}

Returns the current envinronment (NEKONOTE_ENV).

### Return Values:
It returns `string`.

# Referencing Environment Variables From Templates

For getting values in liquid templates,

There is the special liquid tag `env_get` to get environment values from templates which for {{site.product}}.

It's possible to get some environment value in templates by {% raw %}{% env_get `key_name` %}{% endraw %}.

### In templates
{:class="cb-title"}

{% raw %}
```
<h1>The query string is {% env_get QUERY_STRING %}.</h1>
```
{% endraw %}

{% include doc-nav-link.html prev=site.data.document.contents.business_logic next=site.data.document.contents.user_conf %}
