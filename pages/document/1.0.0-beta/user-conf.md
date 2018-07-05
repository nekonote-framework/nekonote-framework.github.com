---
title: 12. User Configuration Files
permalink: /document/1.0.0-beta/user-conf
layout: article
is_doc: true
---
**User Configuration Files** are configuration files which located in `preference/your_environment/setting` directory.

You may configure some constant values such as configuration for middlewares. They can be referenced from Handler classes or templates.

{% include toc.html %}

# Setting Values

User configuration files are YAML files.

The following example configures the some middlewares through the user configuration file.

### preference/your_environment/setting/server.yml
{:class="cb-title"}

```
server:
    mysql:
        host: mysql.example.com
        port: 3306
        user: user
        password: password
        socket: /path/to/socket
    memcached:
        host: memcached.example.com
        port: 11211
```

<p class="tip">Field names in top level among files are not supposed to be duplicate.</p>

# Getting Values

All of files located in `preference/your_environment/setting` directory are loaded automatically and recursively.

## Getting values from Handlers

### handler/example.rb
{:class="cb-title"}
```ruby
class ExampleHandler < BaseHandler
    def index
        # This returns Hash => {"mysql"=>{"host"=>"mysql.example.com", "port"=>3306, "user"=>"user", "password"=>"password", "socket"=>"/path/to/socket"}, "memcached"=>{"host"=>"memcached.example.com", "port"=>11211}}
        Nekonote::Setting.get :server

        # This returns Hash => {"host"=>"mysql.example.com", "port"=>3306, "user"=>"user", "password"=>"password", "socket"=>"/path/to/socket"}
        Nekonote::Setting.get :server, :mysql

        # This returns String => "mysql.example.com"
        Nekonote::Setting.get :server, :mysql, :host

        # It's possible to use __setting_get instead of Nekonote::Setting.get.
        # This returns Hash => {"host"=>"memcached.example.com", "port"=>11211}
        __setting_get :server, :memcached
    end
end
```

## Getting the values from templates

You may use a special liquid tag called `__setting_get` to get the values declared in user configuration files.

### template/example.tml
{:class="cb-title"}

{% raw %}
```
server: {% setting_get server %}<br />
server, mysql: {% setting_get server, mysql %}<br />
server, mysql, host: {% setting_get server, mysql, host %}
```
{% endraw %}
{:class="code-block-command"}

The result on blowser must be the following:

```
server: {"mysql"=>{"host"=>"mysql.example.com", "port"=>3306, "user"=>"user", "password"=>"password", "socket"=>"/path/to/socket"}, "memcached"=>{"host"=>"memcached.example.com", "port"=>11211}}
server, mysql: {"host"=>"mysql.example.com", "port"=>3306, "user"=>"user", "password"=>"password", "socket"=>"/path/to/socket"}
server, mysql, host: mysql.example.com
```
{:class="code-block-result"}

# Nekonote::Setting class

`Nekonote::Setting` is the class to get some values from user configuration files.

The following is the list of class methods of `Nekonote::Setting`.

|:-|:-|
[Nekonote::Setting.get](#nekonotesettinggetfieldname-)|Returns values from user configuration files by some specific field names.
[Nekonote::Setting.keys](#nekonotesettingkeys)|Returns all field names in the top level from user configuration files.
[Nekonote::Setting.get_all](#nekonotesettinggetall)|Returns all field names and values from user configuration files.

### Nekonote::Setting.get(field_name [,...])
{:class="ck-title"}

Returns values from user configuration files by some specific field names.

parameter|type|required|description
:-|:-|:-
**field_name**|string or symbol|yes|A field name to get the value from user configuration files. It’s allowed to give any number of field names by comma.

<p class="tip">The first <code>field name</code> must be in top level. The next field name must be placed under the first field name.</p>

### Return Values:
It returns mixed types if the given `field_name` was found.

It returns `nil` if `field_name` was not found or given no `field_name`.

### Nekonote::Setting.keys
{:class="ck-title"}

Returns all field names in the top level from user configuration files.

### Return Values:

It returns `array`.

### Nekonote::Setting.get_all
{:class="ck-title"}

Returns all field names and values from user configuration files.

### Return Values:
It returns `hash`.

# More Examples

## Getting Values as Array

### preference/your_environment/example.yml
{:class="cb-title"}
```
data:
    - foo
    - bar
    - baz
```

### preference/handler/example.rb
{:class="cb-title"}
```ruby
Nekonote::Setting.get :data # It returns => ["foo", "bar", "baz"]
```


{% include doc-nav-link.html prev=site.data.document.contents.rack_env next=site.data.document.contents.params %}
