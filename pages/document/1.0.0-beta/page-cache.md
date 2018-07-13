---
title: 14. Page Caching
permalink: /document/1.0.0-beta/page-cache
layout: article
is_doc: true
---

**Page Caching** is a mechanism to store the HTTP response code and the header and the body for using later.

# About

Page Caching is file-based caching system for static pages.

Serialized HTTP responses are written with a timestamp into the files in `cache` directory.

<p class="tip">You are not supponsed to use page caching for pages using session management. Page caching is supposed to used for static pages.</p>

# Turning On Page Caching

Page Caching is disabled by default. So you need to turn it on in each route by `page_cache_time` directive.

### preference/your_environment/route.yml
{:class="cb-title"}
```bash
ExampleHandler:
    path: /example
    execute: index
    template: example/index
    page_cache_time: 60
```

Page caching is enabled when some number is set to the value of `page_cache_time` directive.
`page_cache_time` needs to be set **each of routes**.

The above configuration says page caching turns on, its cache time is 60 seconds.

After 60 seconds passed, the cache file for that route will be renewed when the web server received a request.

# Page Cache Busting

As previously mentioned, cache files are bustered automatically after its cache time has passed.

In case of there is a need to reflesh the cache data, the following solutions must be helpful.

## Cache-Busting By URL

The cache file will be used when URL matched completely.

When some route which page caching is enabled matched the URL, the cache file will be used in the next request to that route.
But, if there is a little bit difference in that URL, such as the different query string is there, the cache file will not be used.

For instance, when accessing the URL `http://example.com/path/to/static`, the cache file will be created and it will be used in requests after.

When accessing the URL `http://example.com/path/to/static?foo`, the web server returns no cached response.
Because there is the cache file for `http://example.com/path/to/static` but, there is no cache file for `http://example.com/path/to/static?foo` yet.

The URL `http://example.com/path/to/static?foo` will return the cached response after the first time.

## Cache-Busting By Removing Cache Files

The cache files are created in `cache` directory and are named as SHA-256 hash value.

Typing `nekonote page_cache_clear` or its abbreviation `nekonote pcc` on your terminal are the way to remove the cache files.

You may remove the cache files directly by `rm /path/to/app/root/cache/*`.

{% include doc-nav-link.html prev=site.data.document.contents.params next=site.data.document.contents.exception %}
