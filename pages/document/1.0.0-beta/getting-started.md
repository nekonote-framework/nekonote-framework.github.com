---
permalink: /document/1.0.0-beta/getting-started
title: 1. Getting Started
layout: article
is_doc: true
---

{% include toc.html %}

# Installing From RubyGems.org

It's the simplest way installing {{site.product}} from _RubyGems.org_.

### on terminal
{:class="cb-title"}
```bash
# need --pre option, because Nekonote Framework is under beta version
gem install nekonote-framework --pre
```

`nekonote` command will be available on your terminal after installing {{site.product}}.

<p class="tip">In case of using <code>rbenv</code> and missing <code>nekonote</code> command on your terminal, typing <code>rbenv rehash</code> might help you.</p>

# Server Requirements

name|version
:-|:-
**Ruby**|>= {{site.spec.compliant_ruby_version}}

name|version|description
:-|:-
**rack**|>= 2.0|A Ruby Webserver Interface.
**liquid**|>= 4.0|Liquid markup language.
**puma**|>= 3.8|A Ruby/Rack web server built for concurrency.
**color_echo**|>= 2.0|Colorizing output on terminal.
**simple_rotate**|>= 1.2|Thread safety/multiprocess-safe Logger. * Tring to remove from the dependencies.

{% include doc-nav-link.html next=site.data.document.contents.command %}
