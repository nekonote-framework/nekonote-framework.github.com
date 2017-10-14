---
title: 6. Controlling Web Server
permalink: /document/1.0.0-beta/controlling-server
layout: article
is_doc: true
---
{{site.product}} provides CLI tool for controlling the web server.

{% include toc.html %}

# Starting Web Server

As mentioned earlier, you can use `nekonote` command after installing {{site.product}}.

<p class="tip">Not found <code>nekonote</code> command? Please run it with <code>bundle exec</code> or run <code>rbenv rehash</code> when using rbenv.</p>

### on terminal
{:class='cb-title'}
```bash
nekonote new app my_project
cd my_project
bundle

# launch the web server
NEKONOTE_ENV=development nekonote server start
```

After running the above command, the web server will launch on your machine.

<p class="tip">When <code>daemonize</code> is set <code>false</code>, you must hit <code>ctrl + C</code> to stop the web server.</p>

# The configuration
It's possible to change the listening port or document root or more preferences for the web server through the configuration file.

The path to configuration file is `preference/your_environment/server/puma.rb`. You can find it by running `nekonote server conf`.

As you may know, the web server that launched by `nekonote server start` is *Puma* web server.
*Puma* is fast web server for Ruby and it supports Rack application.

The way to configure *Puma* is detailed on *Puma*'s reference manual. Please take a look it for getting more details.

# Preference Examples

I'll show you some examples.

## Changing the port to listen to 80 

### preference/your_environment/server/puma.rb
{:class="cb-title"}
```ruby
# host address to bind
_host = '0.0.0.0'

# listen port
_port = '80'

bind "tcp://#{_host}:#{_port}"
```

<p class="tip"><code>_host</code> is not allowed IP address nor FQDN. <code>__host</code> is a bind address.</p>

## Changing the web server to work on foreground

### preference/your_environment/server/puma.rb
{:class="cb-title"}
```ruby
# true to server will be daemonize
daemonize false
```

## Changing the document root
You must update the document root in the configuration file after moving your application structure to other directory.

### on terminal
{:class="cb-title"}
```bash
# move application directory
mv /path/to/app/root /path/to/after/app/root
```

### preference/your_environment/server/puma.rb
{:class="cb-title"}
```ruby
#_app_root = '/path/to/app/root'
_app_root = '/path/to/new/app/root'
```


# Using Other Web Servers

When you are not welcomed using *Puma*, you may use other one. A web server you will choose must suport Rack application such as `unicorn`, `thin`, `mongrel`.


## Starting with thin web server

### on terminal
{:class="cb-title"}
```bash
# move to your application root
cd /path/to/app/root

# adding thin to your Gemfile
echo "gem 'thin'" >> Gemfile

# installing thin
bundle install

# launch the web server
thin start
```

# Setting Up SSL

Setting up SSL on your website is very easy.
It's just edit the server configuration file as follows.

### preference/your_environment/server/puma.rb
{:class="cb-title"}
```rb
ssl_bind _host, _port, {
   key: '/path/to/your/private.key',
   cert: '/path/to/your/certificate.crt'
}
```

or

```rb
bind "ssl://#{_host}:#{_port}?key=/path/to/your/private.key&cert=/path/to/your/certificate.crt"
```

Running `nekonote server start` will launch the web server with enabling SSL.
Clients must request the server with `https://`.

# Debugging for Deamonized Server

When `daemonize true` is set in `preference/your_environment/server/puma.rb` you can't see any messages on STDOUT nor STDERR.
That's because file descriptors are closed.

In that case messages written to `log/puma.stderr.log` or `log/puma.stdout.log` by default.

Did you change the configuration and you can't find such files? Then you must add the directive below.

### preference/your_environment/server/puma.rb
{:class="cb-title"}
```rb
stdout_redirect "#{_app_root}/log/puma.stdout.log", "#{_app_root}/log/puma.stderr.log"
```

The other way to see exception details about launching the web server is changing `daemonize` to `false` temporarily.

{% include doc-nav-link.html prev=site.data.document.contents.configuration next=site.data.document.contents.routing %}
