---
title: 17. Templates
permalink: /document/1.0.0-beta/template
layout: article
is_doc: true
---

**Templates** and **Layouts** are template files which located in the `static/` directory. Their file extension is `.tpl`.

You are supposed to write your document such as `HTML`, `XML`, `JSON` in there.

{% include toc.html %}

# Web Template System

Touse templates are rendered by a template system called <a href="https://shopify.github.io/liquid/" target="_blank">_Liquid_</a>.

You may use _Liquid_'s features such as operators, variables, comment, control structures, filters, tags, more in your templates.

# Directory for Templates and Layouts

Templates and layouts files are supposed to be placed to the directories below.

Template files
: `static/template/`

Layout files
: `static/layout/`

Template files are rendered into the placement '{% raw %}{{content}}{% endraw %}' that's supposed to be wrriten in layout files.

# Creating Template or Layout Files

## Creating a Template File

Creating a template file is just typing `nekonote new template <template_name>` on your teminal.

```bash
nekonote new template example/top --root /path/to/app/root
```
{:class="code-block-command"}

{% raw %}
```
    * Created a directory -> /path/to/app/root/static/template/example

    Success!
      Created a new template '/path/to/app/root/static/template/example/top.tpl'

    Note:
      To use the template file you created, you need to set 'template' directive in your route as:

      e.g.
      ExampleHandler
         path: /example
         execute: index
         template: example/top
```
{:class="code-block-result"}
{% endraw %}

The template file you created is just a empty file. You are supposed to write your document such as `HTML`, `XML`, `JSON` in them.

<p class="tip">If you specified a relative path to <code>&lt;template_name></code> such as <code>example/top</code>, and if <code>static/template/example</code> directory doesn't exist,
it will be created automatically and <code>top.tpl</code> is created in that directory.</p>

<p class="tip">You may use an abbreviation <code>tpl</code> like <code>nekonote new tpl &lt;template_name></code>.</p>

## Creating a Layout Files

It's possible to create a layout file as well by typing `nekonote new layout <layout_name>`.


```bash
nekonote new layout example/layout --root /path/to/app/root
```
{:class="code-block-command"}

{% raw %}
```
    * Created a directory -> /path/to/app/root/static/layout/example

    Success!
      Created a new layout '/path/to/app/root/static/layout/example/layout.tpl'

    Note:
      To use the layout file you created, you need to set 'layout' directive in your route as:

      e.g.
      ExampleHandler
         path: /example
         execute: index
         layout: example/layout
```
{:class="code-block-result"}
{% endraw %}

# Assign Variables

You may assign variables to your templates from Handler classes.

As previously mentioned at [{{site.data.document.contents.business_logic.num}}. {{site.data.document.contents.business_logic.title}}]({{site.data.document.contents.business_logic.path}}) there is the method to assign variables to templates.

## Example for Assigning Variables

### Creating Necessary Parts

### on Terminal
{:class="cb-title"}

```bash
cd /path/to/app/root
nekonote new tpl example
nekonote new layout common
nekonote new handler example
```

### Defining a Route

### preference/your_environment/route.yml
{:class="cb-title"}
```
ExampleHandler:
    path: /example
    execute: index
    template: example
    layout: common
    title: example page
```

### Editing Layout

It's good for writing some structure such as `<html>`, `<head>`, `<body>` elements.

### static/layout/common.tpl
{:class="cb-title"}

{% raw %}
```html
<!DOCTYPE html>
<html>

<head>
  <title>{{title}}</title>
</head>

<body>
{{content}}
</body>

</html>
```
{% endraw %}

As previously mentioned, '{% raw %}{{content}}{% endraw %}' is replaced with the template file which you specified.
If you didn't set `template` directive, the default one will be used instead.

### Editing Template

The next point is editing the template you created.

### static/template/example.tpl
{:class="cb-title"}

{% raw %}
```html
This page is an example for assigning some variables into templates.<br />
string: {{string}}<br />
int: {{int}}<br />
float: {{float}}<br />
array: {{array}}<br />
hash: {{hash}}<br />
bool: {{bool}}<br />
nil: {{nil}}
```
{% endraw %}

As you might know, the above variables are not assigned yet.

There is a need to assign the variables manually as:

### handler/example.rb
{:class="cb-title"}

```rb
class ExampleHandler < BaseHandler
    def index
        list = {
            :string => 'STRING',
            :int    => 2002,
            :float  => 2.002,
            :array  => ['N', 'E', 'K', 'O', 'N', 'O', 'T', 'E'],
            :hash   => {:type => 'kitten', :id => 2002, :is_cat => true},
            :bool   => false,
            :nil    => nil
        }

        __assign list
    end
end
```

Now everything is prepared. It's time to start the web server and access to URL `http://example.com/example`.

The Rendered HTML must be following.

The response body will be rendered as HTML below:

```html
<!DOCTYPE html>
<html>

<head>
  <title>example page</title>
</head>

<body>
This page is for example of assigning variables into template.<br />
string: STRING<br />
int: 2002<br />
float: 2.002<br />
array: NEKONOTE<br />
hash: {:type=>"kitten", :id=>2002, :is_cat=>true}<br />
bool: false<br />
nil: 

</body>

</html>
```

# Custom Tags for {{site.product}}

There are the special tags for {{site.product}}. You may use them in your templates.

tag name|description
:-|:-
__setting_get|Returns values from user configuration files by some specific field names. This is the same as Nekonote::Setting.get.
__env_get|Returns the value by a specific key_name. This is the same as Nekonote::Env.get.

{% include doc-nav-link.html prev=site.data.document.contents.logger next=site.data.document.contents.static_file %}