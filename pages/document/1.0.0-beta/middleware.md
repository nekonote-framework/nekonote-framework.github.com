---
title: 19. Middlewares
permalink: /document/1.0.0-beta/middleware
layout: article
is_doc: true
---

**Middlewares** are useful features. They will help your development.
It's possible to use Rack middlewares easily through `middlewares.rb`.

{% include toc.html %}

# Configuring Rack Middlewares

There is a file called `middlewares.rb` under your `preference` directory. That file is a Ruby file.
It's used to configure Rack middlewares.

The configuration for some middlewares are already listed in `middlewares.rb`.
In order to disable or enable middlewares you must edit `middlewares.rb`.

You are supposed to configure Rack middlewares in `middlewares.rb`.

The following are middlewares which listed in `middlewares.rb` after installing {{site.product}}.

# Rack::Reloader

**Rack::Reloader** reloads modified files without restarting nor reloading the web server.

It's enabled by default.

variable name|type|description
:-|:-|:-
_colldown|int|Modified files will not be reloaded while this time in seconds has passed from last request.


### preference/your_environment/middlewares.rb
{:class="cb-title"}
```rb
# configuring Rack::Reloader
_cooldown = 10
use Rack::Reloader, _cooldown
```


# ShowExceptions

**ShowExceptions** is a GUI based debugger.
When some exception raised, you can see that exception details on your web browser.
It's enabled by default.

<p class="tip">When <code>fatal</code> route is set in <code>route_error.yml</code>, <code>ShowExceptions</code> won't work.<br />In that case you'd better to set the logger enabled for tracking exceptions.</p>
<p class="tip" style="color: red;">IMPORTANT: You are supposed to disable this function on production environment for security reason.</p>


# Auth::Basic

**Auth::Basic** provides Basic access authentication.

It's disabled by defualt.

variable name|type|description
:-|:-|:-
_realm|string|Realm attribute.
_username|string|Username.
_passwd|string|Password.

### preference/your_environment/middlewares.rb
{:class="cb-title"}
```rb
# configuring Basic access authentication
_realm    = 'Here is secret zone'
_username = 'username_here'
_passwd   = 'password_here'
use Rack::Auth::Basic, _realm do |username, passwd|
    username == _username && passwd == _passwd
end
```

# Auth::Digest::MD5

**Auth::Digest::MD5** provides Digest access authentication.

It's disabled by defualt.

Passed password is hashed by the MD5 algorithm.

variable name|type|description
:-|:-|:-
_realm|string|Realm attribute.
_opaque|string|Opaque data. It's supposed to be set a long random string.
_username|string|Username.
_passwd|string|Password.

### preference/your_environment/middlewares.rb
{:class="cb-title"}
```rb
# configuring Digest access authentication
_realm    = 'Here is secret zone'
_opaque   = 'replace_me' # this is supposed to be set to a long random string
_username = 'username_here'
_passwd   = 'password_here'
_pw = {_username => _passwd}
use Rack::Auth::Digest::MD5, _realm, _opaque do |username|
    _pw[username]
end
```

# Rack::Access

**Rack::Access** provides access restrcition based on IP addresses.

It's not installed automatically.
You must add `'rack-contrib'` to your Gemefile and typing `bundle install` to use it.

variable name|type|description
:-|:-|:-
_rules|hash|Rules for access restrcition. Please see the example below.

### preference/your_environment/middlewares.rb
{:class="cb-title"}
```rb
# Configuring access restriction by IP addresses
_rules = {
    # You may append a subnet mask to IP address.
    # You may add any number of IP addresses by comma which allowed accessing the server.
    # '/location' => [ipmasks ,...]
    '/' => ['127.0.0.1/32', '172.16.254.1']
}
require 'rack/contrib'
use Rack::Access, _rules
```

{% include doc-nav-link.html prev=site.data.document.contents.static_file next=site.data.document.contents.session_manage %}
