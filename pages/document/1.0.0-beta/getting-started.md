---
permalink: /document/1.0.0-beta/getting-started
title: 1. Getting Started
layout: article
is_doc: true
---

{% include toc.html %}

# Install {{site.product}} from RubyGems.org

It's the simplest way installing it from _RubyGems.org_.

### on terminal
{:class="cb-title"}
```bash
# need --pre option, because Nekonote Framework is under beta version
gem install nekonote-framework --pre
```

After installing {{site.product}}, it's possible to run `nekonote` command on your teminal.

<p class="tip">In case of using <code>rbenv</code> and missing <code>nekonote</code> command on your terminal, typing <code>rbenv rehash</code> might help you.</p>

# Server Requirements

name|version
:-|:-
**Ruby**|>= {{site.spec.compliant_ruby_version}}

name|version|description
:-|:-
**rack**|>= 2.0|Well-known the common interface between web servers which support Ruby.
**liquid**|>= 4.0|Well-known poweful template engine for Ruby.
**puma**|>= 3.8|Faster web server for Ruby.
**color_echo**|>= 2.0|Colorizing output on terminal
**simple_rotate**|>= 1.2|Thread and multi-process safety simple logger. * Tring to remove from the dependencies.

{% include doc-nav-link.html next=site.data.document.contents.command %}
