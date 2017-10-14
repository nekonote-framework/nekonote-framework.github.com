---
title: 2. Console Commands
permalink: /document/1.0.0-beta/command
layout: article
is_doc: true
---

`nekonote` command is enabled on terminal after installing {{site.product}}.

You can see the usage of `nekonote` command by just typing `nekonote` or typing `nekonote -h`.

{% include toc.html %}

# Most Common Option

You may use the folloing option in most cases.

option|description|e.g.
:-|:-|:-|:-
**--root** path|Declares an application root. The default is your current directory.|--root /path/to/app/root

# server
`nekonote server` controls the web server. Its abbreviation is `s`. It accepts `--root` option.

You can see the usage by just typing `nekonote server`.
You must type the sub command after it from the list below.

## Sub Commmands

sub command|abbreviation|description
:-|:-|:-
start|st|Starts the web server.
stop||Stop the web server.
halt||Halts the web server.
restart||Restarts the web server.
phased_restart||Reqests phased restart to puma web server.
status||Displays status of the web server.
config|conf|Displays a path to the configuration file.

# new

`new` command creates something such as application structures, template files, environments.
It accepts `--root` option.

You can see the details by just typing `nekonote new`. This requires the sub command which listed below.

## Sub Commands

sub command|abbreviation|description
:-|:-|:-
application &lt;application_name>|app|Generates a new nekonote application structure.
env &lt;environment_name>||Creates a new environment for an existing application structure.
handler &lt;handler_name>||Creates a new handler for an existing application structure. You may pass a relative path.
template &lt;template_name>|tpl|Creates a new template file for an existing application structure. You may pass a relative path.
layout &lt;layout_name>||Creates a new layout file for an existing application structure. You may pass a relative path.

# reload_pref
`nekonote reload_pref` reloads preferences without restarting nor reloading the web server.
Its abbreviation is `rp`. It accepts `--root` option.

<p class='tip'>Only the files under <code>setting/</code> or <code>logger.yml</code> are reloadable.</p>

# page_cache_clear
`nekonote page_cache_clear` removes the page cache files in the `cache` directory.
Its abbreviation is `pcc`. It accepts `--root` option.

# env
`nekonote env` shows the current application environment.

# info
`nekonote info` shows some information.

# Other Usage

## nekonote -h, --help
Displays the usage of `nekonote` command. Just typing `nekonote` displays the same result.

## nekonote -v, --version
Shows the version of your {{site.product}}.

{% include doc-nav-link.html prev=site.data.document.contents.getting_started next=site.data.document.contents.structure %}
