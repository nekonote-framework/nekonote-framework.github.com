---
title: 20. Session Management
permalink: /document/1.0.0-beta/session-management
layout: article
is_doc: true
---
You may use the Rack middlewares for **Session Management**.

The some middlewares are listed in `middlewares.rb` as following this chapter.

{% include toc.html %}

# Most Common Options

There are the three Rack middlewares in `middlewares.rb` which for managing sessions.
They are listed in `middlewares.rb` after installing {{site.product}}

The most common directives are listed below.

<p class="tip">Some of the common directives wont't be used. It depends on the middlewares.</p>

varibale name|type|description|default
:-|:-|:-|:-
**_key**|string|A cookie name to store a session ID. When using Session::Dalli you shold set 'namespace' directive instead.|rack.session
**_domain**|string|The cookie is sent to the web server when the accessing domain name (includes sub domain) matched the value set here. When you haven't set anything or setting nil, 'Domain=domain-value' won't be append to the value of 'Set-Cookie' header.|-
**_path**|string|The cookie is sent to the web server when the accessing URL includes the path set here. '/' matches any paths.|/
**_expire_after**|int|Lefetime of the session cookie. When you haven't set this option or setting nil, the cookie is a persistent cookie.
**_secure**|boolean|True to a cookie is set and is sent when the web server is using SSL.|false
**_httponly**|boolean|Accessibility of a cookie from javascript. Setting true to cookie is HttpOnly and can not access the cookie from javascript.|false
**_sidbits**|int|Length of session ID. The default value is 128 bits.|128

# Optional Directives For Session::Pool

variable name|type|description|default
:-|:-|:-|:-
**_drop**|boolean|True to session data will be deleted precisely from the session cache.|false

# Required Directives For Session::Dalli

`Session::Dalli` uses memcached servers for storing session data.
There are the options to configure memcached servers. More information is later point of this chapter.

varibale name|type|description|example
:-|:-|:-|:-
**_servers**|array|Information of the memcached server to add. Need to set as "host:port:weight". 'port' and 'weight' are optional. It's possible to set any number of servers by comma.|['127.0.0.1:11211']
**_connection_pooling**|boolean|True to enabling connection pooling. False to disabling connection pooling|false

# Optional Directives For Session::Dalli

varibale name|type|description|default
:-|:-|:-|:-
**namespace**|string|Namespace for keys in the memcached servers. The key is set similar "&lt;namespace specified here>:&lt;session ID>".|rack.session
**failover**|boolean|True to enabling failover and if the memcached server is down, the other available memcached server responds instead. You need to set more than one servers at the 'server' directive.|true
**threadsafe**|boolean|True to thread-safe access to a socket. False to allow no thread-safe access.|true
**expires_in**|int|TTL (Time to live) for cookie in seconds. The default is 0 and the meaning of 0 is forever.|0
**compress**|boolean|True to when the data is larger than 1024 bytes, it's compressed and is sent to the memcached server. False to no compression.|false

Connection pooling enables reusing the connections to access the memcached server quickly.
In order to enable it, please set `_connection_pooling = true`.

The following directives is about connection pooling.

varibale name|type|description|default
:-|:-|:-|:-
**_size**|int|Maximum number of connections a pool.|5
**_timeout**|int|Maximum time in seconds of making a connection from the connection pool.|5

# Session::Cookie
**Session::Cookie** provides cookie based simple session management.

Session ID and session data are stored in a cookie.

There is a risk that session data can be changed through editing cookie.
So this way is not secure.

You are not supposed to set some important data if enabling this feature.
On top of that 4 kilobyte is maximum amount of storing cookie.

### preference/your_environment/middlewares.rb
{:class="cb-title"}

```rb
use Rack::Session::Cookie, _session_options
```

# Session::Pool

**Session::Pool** provides property-based session management.

Session data is stored in the web server, cookie is only used to store session ID.

It will work faster but keep in mind that session data is stored in the instance variable on Ruby.
It means that session data is on memory and is gone after restarting the web server.

### preference/your_environment/middlewares.rb
{:class="cb-title"}

```rb
_drop = false
_session_pool_options = {
    :drop => _drop
}
use Rack::Session::Pool, _session_options.merge(_session_pool_options)
```

# Session::Dalli

**Session::Dalli** provides memcached based session management.
You must prepare memcached server to store session data.

Session data is stored in specific memcached server, cookie is only used to store session ID.

In order to enable this middleware you must install a library called *dalli*.
Need to add `'dalli'` to your Gemfile, and running `bundle install` for getting *dalli*.

### preference/your_environment/middlewares.rb
{:class="cb-title"}

```rb
_servers = ['127.0.0.1:11211'] # you may add any number of servers by comma
_connection_pooling = false

# options for dalli client
_namespace  = 'nekonote.session'
_failover   = true
_threadsafe = true
_expires_in = 0
_compress   = false
_dalli_client_options = {
    :namespace  => _namespace,
    :failover   => _failover,
    :threadsafe => _threadsafe,
    :expires_in => _expires_in,
    :compress   => _compress
}

# options for connection pooling
# they will work when connection pooling is enbaled
_size    = 5
_timeout = 5
_dalli_pool_options = {
    :size    => _size,
    :timeout => _timeout
}

# set-up dalli
require 'dalli'

if _connection_pooling
    # enabling connection pooling
    _dalli_client = ::Dalli::Client.new _servers, _dalli_client_options
    _cache = ::ConnectionPool::Wrapper.new(_pool_options) {_dalli_client}
else
    # disable connection pooling
    _cache = ::Dalli::Client.new _servers, _dalli_client_options
end

_dalli_options = {
    :cache => _cache
}
use Rack::Session::Dalli, _session_options.merge(_dalli_options)
```

# Weight

You may specify any number of memcached servers to `server` directive. When you set two or more memcached servers there, you may specify its weight.

Weight means which memcached server is supposed to be used more frequently. Bigger number one is used more frequently than smaller number one.

### preference/your_environment/middlewares.rb
{:class="cb-title"}
```rb
_servers = ['127.0.0.1:11211:70', '127.0.0.1:1122:30']
```

The example above says, `127.0.0.1:1121` is supposed to be used more frequently than `127.0.0.1:1122`.

# Failover

When you set multiple memcached servers, you'd better enable the failover system.

Failover is enabled by default.
If you need to disable failover you should set `_failover` to `false` in your `middlewares.rb`.

Stored data in memecached server will be lost when server has gone, but failover feature can keep using available memcached server even if some of servers has gone.

# The Object For Session Management

`Rack::Session::Abstract::SessionHash` object is supposed to be set to an instance variable called `@session` in Handler classes.

When any middleware isn't enabled which about session management, `nil` is set to `@session` instead.

Session won't start automatically. Session starts when the following methods are called:
`@session[key] = value`, `@session.clear`, `@session.destroy`, `@session.update(hash)`, `@session.replace(hash)`, `@session.delete(key)`.

# Instance Methods For Rack::Session::Abstract::SessionHash

The following are methods which can be called through the value of `@session`.

method|description
:-|:-
@session.id              | Returns the session ID. It returns nil when session has not started.
@session[:key] = 'value' | Sets a value to the session data with a specified key.
@session[:key]           | Returns the value from the session data by a specific key.
@session.has_key? :key   | Returns true if a specific key exists in the session data, in other case returns false.
@session.empty?          | Returns true if the session data is empty, in other case returns false.
@session.to_hash         | Returns the all session data as hash: {key => value}. When session has not started returns empty hash.
@session.clear           | Clears the all session data.
@session.delete :key     | Deletes the data from the session data by a specific key.
@session.update hash     | Adds the given hash to session data.
@session.replace hash    | Replaces the session data with a specific hash.

In order to get more information, Please visit <a href="http://www.rubydoc.info/github/rack/rack/Rack/Session/Abstract/SessionHash" target="_blank">http://www.rubydoc.info/github/rack/rack/Rack/Session/Abstract/SessionHash</a>

## Example

This is the usage example for session management.

```ruby
class Friend
    def get_friend_id_list_from_db
        # implement me!
        friend_id_list = [1, 5, 17, 28]

        return friend_id_list
    end
end
```
```ruby
class ExampleHandler < BaseHandler
    def index
        if @session.has_key? :friend
            # get the list of friend id from the session data
            friend_id_list = @session[:friend]

            logwrite 'get friend_id_list from session data'

        else
            # get the list of friend id from the database
            friend = ::Friend.new
            friend_id_list = friend.get_friend_id_list_from_db

            # cache data in the session data
            @session[:friend] = friend_id_list

            logwrite 'get friend_id_list from database'
        end

        logwrite friend_id_list
    end
end
```

{% include doc-nav-link.html prev=site.data.document.contents.middleware %}
