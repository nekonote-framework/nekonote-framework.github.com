---
title: 18. Publishing Static Files
permalink: /document/1.0.0-beta/static-file
layout: article
is_doc: true
---

The files under `public` directory are will not published automatically. You need to edit the configuration file for it which called `public.yml`.

{% include toc.html %}

# Publishing Static Files

`public.yml` is used to publish static files and is also used to set HTTP response headers for them.

After generating an application structure, there will be some directories `css`, `js`, `img` and a file named `favicon.ico` in the `public` directory.

On top of that, they are already published by the default configuration of `public.yml`. That's as follows:

### preference/your_environment/public.yml
{:class="cb-title"}
```yaml
published_directory:
    - css  # public/css/* will be published recursively
    - js   # public/js/*  will be published recursively
    - img  # public/img/* will be published recursively
```

Files under the directories which declared in `published_directory` are published recursively.

You may add any number of directories in here when you need to publish anything else.

```yaml
# The files set here are published.
# They need to be located under 'public' directory and they are not searched out recursively.
published_file:
    - favicon.ico # public/favicon.ico will be published
```

Files declared in `published_file` directive are published.
You may add some files here when you need.

<p class="tip">The files located under 'public' directory are not searched out recursively. You are supposed to set file name with directory.</p>

# HTTP Response Headers

You may arrange HTTP response headers for static files. Whenever that configuration will take precedence.

<p class="tip"><code>Content-Type</code> header will be set automatically by their file name extension.</p>
<p class="tip"><code>Content-Type: text/plain</code> to forces file to download.</p>

There are the five directives in `public.yml` to set HTTP response headers to static files.

name|description
:-|:-
[**all**](#all)|Sets HTTP headers to all files located in the 'public' directory.
[**directory**](#directory)|Sets HTTP headers to files located under a specific directory.
[**extension**](#extension)|Sets HTTP headers to files with a specific file name extension.
[**regexp**](#regexp)|Sets HTTP headers to files matched a specific regular expression.
[**fonts**](#fonts)|Sets HTTP headers to most common web font formats.

## all

`custom_header` > `all` directive is used to set HTTP headers to all files located in the `public` directory.

### preference/your_environment/public.yml
{:class="cb-title"}
```yaml
custom_header:
    all:
        name: CustomeHeaderAll
        value: all
```

The above example says HTTP header `CustomeHeaderAll: all` will be set to any requests to static files.

If you need to add more HTTP headers, that's possible by using array as following:

```yaml
custom_header:
    all:
        -
            name: CustomeHeaderAll1
            value: all 1
        -
            name: CustomeHeaderAll2
            value: all 2
```

## directory

`custom_header` > `directory` directive is used to set HTTP headers to files located under a specific directory.

The `target` directive in `directory` directive is supposed to be set some directory and the files under it will be affected.

### preference/your_environment/public.yml
{:class="cb-title"}

```yaml
published_directory:
    - foo/bar

custom_header:
    directory:
        target: foo/bar
        name: CustomeHeaderDirectory
        value: directory foo/bar
```

The response for a request to the URL `/foo/bar/exmaple.css` will contain the HTTP header `CustomeHeaderDirectory: directory foo/bar`.

If you need to add more HTTP headers, that's possible by using array as following:

```yaml
published_directory:
    - foo
    - bar

custom_header:
    directory:
        -
            target: foo
            name: CustomeHeaderDirectory
            value: directory foo
        -
            target: bar
            name: CustomeHeaderDirectory
            value: directory bar
```

The response for a request to the URL `/bar/exmaple.css` will contain the HTTP header `CustomeHeaderDirectory: directory bar`.

It's possible to set any number of headers to the same directory.

```yaml
published_directory:
    - foo

custom_header:
    directory:
        -
            target: foo
            name: CustomeHeaderDirectory1
            value: value 1
        -
            target: foo
            name: CustomeHeaderDirectory2
            value: value 2
```
The response for a request to the URL `/foo/exmaple.css` will contain the HTTP headers `CustomeHeaderDirectory1: value 1` and `CustomeHeaderDirectory2: value 2`.

## extension

`custom_header` > `extension` directive is used to set HTTP headers to files with a specific file name extension.

The `target` directive in `extension` directive is supposed to be set a file name extension to set HTTP headers.

### preference/your_environment/public.yml
{:class="cb-title"}

```yaml
published_directory:
    - js

custom_header:
    extension:
        target: js
        name: CustomeHeaderFileNameExtension
        value: js file
```

The response for a request to the URL `/js/example.js` will contain the HTTP header `CustomeHeaderFileNameExtension:js file`.

If you need to add more HTTP headers, that's possible by using array as following:

```yaml
published_directory:
    - js
    - css

custom_header:
    extension:
        -
            target: js
            name: CustomeHeaderFileNameExtension
            value: js file
        -
            target: css
            name: CustomeHeaderFileNameExtension
            value: css file
```

The response for a request to the URL `/css/example.css` will contain the HTTP header `CustomeHeaderFileNameExtension:css file`.

It's possible to set any number of file name extensions by comma like:


```yaml
custom_header:
    extension:
        target: jpg, png, gif
        name: CustomeHeaderFileNameExtension
        value: image files
```

The response for a request to the files will contain the HTTP header `CustomeHeaderFileNameExtension:image files` which its file name extension is `jpg` or `png` or `gif`.

## regexp

`custom_header` > `regexp` directive is used to set HTTP headers to files matched a specific regular expression.

The `target` directive in `regexp` directive is supposed to be set a regular expression to match static file names.

### preference/your_environment/public.yml
{:class="cb-title"}

```yaml
custom_header:
    regexp:
        target: ^/foo.*-bar$
        name: CustomeHeaderRegexp
        value: regexp ^/foo.*-bar$
        ignore_case: true
```

The `ignore_case` directive is optional. If `ignore_case` is set to `true`, the regular expression matchs as not case-sensitive against file names.

If you need to add more HTTP headers, that's possible by using array.

## fonts

The `custom_header` > `fonts` directive is used to sets HTTP headers to most common web font formats.

Typically, font files are named with the file extension name such as `.eot, .ttf, .otf, .woff2, .woff, .svg`.

### preference/your_environment/public.yml
{:class="cb-title"}

```yaml
custom_header:
    fonts:
        name: CustomeHeaderFont
        value: font file
```

The response for a request to the URL /font/exmaple.ttf will contain the HTTP header `CustomeHeaderFont: font file`.

If you need to add more HTTP headers, that's possible by using array.

{% include doc-nav-link.html prev=site.data.document.contents.template next=site.data.document.contents.middleware %}
