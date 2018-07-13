---
title: 9. Handlers
permalink: /document/1.0.0-beta/handler
layout: article
is_doc: true
---

**Handlers** are classes for business logic.

When a route matches a request, the method will be called which set to `execute` directive in `route.yml`
The method must be declared in the Handler class in the route.
It works as Model part.

{% include toc.html %}

# The Flow Of Processing

1. Some route matches against a URL.
1. A Handler class which set in that route is chosen.
1. When the Handler class or its parent `BaseHandler` class has `__pre` method, it's called at first.
1. When some value is set to `execute` directive, that method is called as defined method of the Handler class.
1. When the Handler class or its parent `BaseHandler` class has `__post` method, it's called at last.

# Creating Handlers

You can create a new Handler by `nekonote` command.

### Creating HelloWorld Handler
{:class="cb-title"}

```bash
$ nekonote new handler hello_world
```
{:class="code-block-command"}

```
    Success!
      Createed a new Handler 'HelloWorldHandler',  '/path/to/app/root/handler/hello_world.rb'

    Note:
      In order to use the crated Handler, there is a need to configure routes as:

      e.g.
      HelloWorldHandler:
         path: /hello_world
         execute: index
```
{:class="code-block-result"}

A new Handler named `HelloWorldHandler` was created into `handler/hello_world.rb`.

<p class="tip">Handler classses are supposed to be located under <code>/path/to/app/root/handler/</code> directory.</p>

There is a need to configure routes to use the created Handler.

### preference/your_environment/route.yml
{:class="cb-title"}

```
HelloWorldHandler:
    path: /hello-world
    execute: index
```

Next, I'd like to make some touches to the Handler.

### handler/hello_world.rb
{:class="cb-title"}

```ruby
class HelloWorldHandler < BaseHandler
    def __pre
        puts 'This method will be called at first.'
    end

    def __post
        puts 'This method will be called at last.'
    end

    def index
        puts 'Your program here.'
    end
end
```

After starting the web server by running `nekonote server start`, it's possible to access to URL `http://example.com:2002/hello-world`.

But even if you accessed it, The result must be nothing diplayed on your browser, because any template has not been set in that route.

Anyway the following output must be displayed in your terminal.

```
This method will be called at first.
Your program here.
This method will be called at last.
```

The `index` method in `HelloWorldHandler` was called. That's because the value `index` is set in `execute` directive in that route.

`__pre` method was called before the `index` method. And after `index` methods was called, at last `__post` method was called.
That's because `__pre` and `__post` methods are magical.

# BaseHandler

All Handler classes are inherited from **BaseHandler** class.
BaseHandler class is a mechanism for code reuse in each Handler classes.

You may add program to BaseHandler class. BaseHandler class is already created to `/path/to/app/root/handler/base.rb`.

# Implementing The Magical Methods

### handler/base.rb
{:class="cb-title"}

```ruby
class BaseHandler < Nekonote::Handler
    # This method run at first
    def __pre
        puts "BaseHandler#__pre is called."
    end
end
```

The above `__pre` method will be called at first.

The magical method `__pre` or `__post` can be defined in you concreate Handlers as well.
When there is a need to overwrite the magic methods and you need to call it as well which defined in the super class BaseHandler,
In that case you should call it by `super` keyword.

### handler/hello_world.rb
{:class="cb-title"}

```ruby
class HelloWorldHandler < BaseHandler
    def __pre
        super
        puts 'This method will be called at first.'
    end

    def __post
        puts 'This method will be called at last.'
    end

    def index
        puts 'Your program here.'
    end
end
```

Then the below must be output to your terminal after you start the web server and access to the URL.

The result will say,

```
BaseHandler#__pre is called.
This method will be called at first.
Your program here.
This method will be called at last.
```

# Implementing Methods For Code-Reuse

### handler/base.rb
{:class="cb-title"}

```ruby
class BaseHandler < Nekonote::Handler
    def reusable_method(name)
        puts "Reuseable code was called from #{name}."
    end
end
```

### handler/hello_world.rb
{:class="cb-title"}

```ruby
class HelloWorldHandler < BaseHandler
    def index
        puts 'Your program here.'

        # call the reusable code defined in the super class
        reusable_method self.class.to_s
    end
end
```

The result must be as following,

```
Your program here.
Reuseable code was called from HelloWorldHandler.
```

# Exiting From Program
When you need to exit in the middle of business logic, you must make your methods returning `::Nekonote::Handler::EXIT`.

For instance, when `__pre` method returns `::Nekonote::Handler::EXIT`, the methods which are supposed to be called after it will be ignored.

### handler/base.rb
{:class="cb-title"}
```ruby
class BaseHandler < Nekonote::Handler
    def __pre
        if @request.params['id'] == nil
            return ::Nekonote::Handler::EXIT
        end
    end
end
```

### handler/example.rb
{:class="cb-title"}
```ruby
class ExampleHandler < BaseHandler
    def __post
        logwrite 'execute __post'
    end

    def task
        logwrite 'execute task' 
    end
end
```

### preference/your_environment/route.yml
{:class="cb-title"}
```yaml
ExampleHandler:
    path: /example
    execute: task
    params: id=int
    method: GET
```

When you accessed to `http://example.com/example`, `BaseHandler#__pre` will return `::Nekonote::Handler::EXIT`.

Both `ExampleHandler#task` or `ExampleHandler#__post` will not be called.

When `__pre` method in BaseHandler has been overwitten and you call it by `super`, `__pre` method in Handlers need to return `::Nekonote::Handler::EXIT` as followng:

### handler/example.rb
{:class="cb-title"}
```ruby
class ExampleHandler < BaseHandler
    def __pre
        return ::Nekonote::Handler::EXIT if super == ::Nekonote::Handler::EXIT
        logwrite 'execute __pre'
    end

    def __post
        logwrite 'execute __post'
    end

    def task
        logwrite 'execute task'
    end
end
```

{% include doc-nav-link.html prev=site.data.document.contents.routing_err_page next=site.data.document.contents.business_logic %}
