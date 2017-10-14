---
title: 15. Exception Handling
permalink: /document/1.0.0-beta/exception-handling
layout: article
is_doc: true
---

**Exception Handling** is the one of most important things to develpment web applications.

{{site.product}} have the three ways to see some exception raised in your program.

# Nekonote::Logger

There is the logger named `Nekonote::Logger` on {{site.product}}.

`Nekonote::Logger` writes the exception details to a specific logfile when it turned on.

But when `write_exception` directive in `logger.yml` is set `false`, the logger will not write any exception detail to the logfile.

# GUI-based Debuger

`Rack::ShowExceptions` is the middleware to display exception details on web blowser.

It's turned on by default.
When you need to turn off `Rack::ShowExceptions` you must edit `middlewares.rb`.

### preference/your_environment/middlewares.rb
{:class="cb-title"}

```rb
# You may comment out something by putting #
# use Rack::ShowExceptions
```

<p class="tip">While the route named `fatal` is defined in <code>route_error.yml</code>, <code>ShowExceptions</code> doesn't work, because `fatal` route takes precedence.<br />
In that case, It will be good if you turn on the logger through <code>logger.yml</code> to track exceptions.</p>

<p class="tip" style="color: red;"><code>ShowExceptions</code> is supposed to be disabled in productional environment for security reason.</p>

# Exception Objects

The exception object will be set to the property named `@error` in the Handler class on `fatal` route in `route_error.yml`.
It's possible to know the exception details by seeing `@error`.

{% include doc-nav-link.html prev=site.data.document.contents.page_cache next=site.data.document.contents.logger %}
