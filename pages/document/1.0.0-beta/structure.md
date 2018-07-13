---
title: 3. Structuring Application
permalink: /document/1.0.0-beta/structure
layout: article
is_doc: true
---
`nekonote` command can generate an application structure easily.

{% include toc.html %}

# Generating Application Structure
You can generate an application strucutre by just running `nekonote new app application_name` on your terminal.

### on terminal
{:class='cb-title'}
```bash
nekonote new app my_project
```

The directory *my_project* will be created on the current directory.

Would you prefer to create it to onto some specific directory? You may give application name as absolute path by `--root` option.

# Installing The Dependencies

Installing the dependencies is not completed yet. You have to install the dependent gem libraries via Bundler.

There's two ways installing the dependencies.

## Installing the dependencies into your system gems

### on terminal
{:class='cb-title'}
```bash
nekonote new app my_project
cd my_project

# install the dependencies described in the Gemfile
bundle install
```

<p class="tip">No such command <code>bundle</code> in your terminal? Please get Bundler by running <code>gem install bundler</code>.</p>

## Installing the dependencies into some specified directory

It's good way in case of the some of dependencies are already installed in your system gems and they are different version.

### on terminal
{:class='cb-title'}
```bash
nekonote new app my_project
cd my_project

# install the dependencies described in the Gemfile
bundle install --path vendor/bundle
```

After ran the above commands, The dependencies will be installed under `vendor/bundle`.

You don't have to put `bundle exec` to beginning of `nekonote` command.

Because you have already installed {{site.product}} by `gem` command, and {{site.product}} aims for the directory installed the dependencies by Bundler.

<p class="tip">When you didn't installed {{site.product}} to system gems, you must prepend <code>bundle exec</code>.</p>

# The Application Structure

After you ran `nekonote new app` on terminal you will get an application structure that are as follows.

|**config.ru**|A Rackup file.
|**Gemfile**|It's a well-known file.
|**preference**|A directory contains your configuration files.
|**handler**|A directory to place Handler class files.
|**lib**|A directory for libraries. It's empty for now. * This directory might be removed
|**public**|A directory to place static files (e.g. css, js, img).
|**template**|A directory to place templates.
|**template/layout**|A directory to specifically place kind of layout files.
|**cache**|A directory to put cache files which will be used on <a href="{{site.data.document.contents.page_cache.path}}">{{site.data.document.contents.page_cache.title}}.
|**log**|A directory to put logfiles.
|**tmp**|Just puma's pid file will be put here.

{% include doc-nav-link.html prev=site.data.document.contents.command next=site.data.document.contents.env %}
