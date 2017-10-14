---
title: 5. Configuration Files
permalink: /document/1.0.0-beta/configuration
layout: article
is_doc: true
---
All configuration files are located in `preference/your_environment/` directory.
As shown below, there are configuration files which are separated by environments.

{% include toc.html %}

## Overview of configuration files

name|description|link
:-|:-
**server/puma.rb**|For configuring puma web server that launched by 'nekonote server start'.|<a href="{{site.data.document.contents.controlling_server.path}}#the-configuration">{{site.data.document.contents.controlling_server.num}}. {{site.data.document.contents.controlling_server.title}}</a>
**route.yml**|For configuring routes.|<a href="{{site.data.document.contents.routing.path}}">{{site.data.document.contents.routing.num}}. {{site.data.document.contents.routing.title}}</a>
**route_include.yml**|For configuring common directives to reuse in route.yml.|<a href="{{site.data.document.contents.routing.path}}">{{site.data.document.contents.routing.num}}. {{site.data.document.contents.routing.title}}</a>
**route_error.yml**|For configuring routes for error pages.|<a href="{{site.data.document.contents.routing_err_page.path}}">{{site.data.document.contents.routing_err_page.num}}. {{site.data.document.contents.routing_err_page.title}}</a>
**setting/**|For configuring static values. You may reference them in program.|<a href="{{site.data.document.contents.user_conf.path}}">{{site.data.document.contents.user_conf.num}}. {{site.data.document.contents.user_conf.title}}</a>
**logger.yml**|For configuring the logger.|<a href="{{site.data.document.contents.logger.path}}">{{site.data.document.contents.logger.num}}. {{site.data.document.contents.logger.title}}</a>
**middlewares.rb**|For configuring the Rack middlewares.|<a href="{{site.data.document.contents.middleware.path}}">{{site.data.document.contents.middleware.num}}. {{site.data.document.contents.middleware.title}}</a>
**public.yml**|For configuring static files under public directory.|<a href="{{site.data.document.contents.static_file.path}}">{{site.data.document.contents.static_file.num}}. {{site.data.document.contents.static_file.title}}</a>

{% include doc-nav-link.html prev=site.data.document.contents.env next=site.data.document.contents.controlling_server %}
