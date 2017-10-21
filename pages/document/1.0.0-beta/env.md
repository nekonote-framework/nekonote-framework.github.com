---
title: 4. Environments
permalink: /document/1.0.0-beta/env
layout: article
is_doc: true
---
The configuration files in {{site.product}} are separated by each of **Environments**.

You may declare that which environment should be used by declaring a shell variable called `NAKONOTE_ENV`.

{% include toc.html %}

# Checking The Current Environment

You can see the current environment by typing `nekonote env` on your terminal.

But the result will say 'Missing Environment'. That's because the shell variable `NAKONOTE_ENV` is not set in your shell.

# Declaring The Environment

You need to set some environment for your application by defining a shell variable called `NEKONOTE_ENV`.

Typically, you can set it by the following command `export NEKONOTE_ENV=development`.
It will be conforable if `NEKONOTE_ENV` is set automatically. You may run `echo "NEKONOTE_ENV=development" >> ~/.bash_profile` for it if your shell is bash.

<p class="tip">If your shell is not Bash, please change the above commands in accordance with your shell.</p>

# Showing Environments
After generating application structure, the structure has an environment named 'development'.

You can see the list of the existing environments by typing `ls /path/to/app/root/preference/` on your shell.
I guess you will find just one environment named 'development'.

<p class="tip">Please replace <code>/path/to/app/root</code> to the actual path to your application strcuture.</p>

# Creating Environments

There is a command to create a new environment:

### on terminal
{:class="cb-title"}
```bash
nekonote new env staging --root /path/to/app/root
```
{:class="code-block-command"}

```
    Success!
      Created a new environment 'staging' on the application /path/to/app/root/my-project/

    Note:
      In order to use the environment you created, you need to set 'staging' to a shell variable called NEKONOTE_ENV.
```
{:class="code-block-result"}

```bash
# see available environments
ls /path/to/app/root/preference
```
{:class="code-block-command"}

```
development staging
```
{:class="code-block-result"}

<p class="tip">When you type the command in application root, there is no need to give <code>--root</code> option.</p>

After that, your application will be having the environment named `staging`.

The way to switch the environment to `staging` is running `export NEKONOTE_ENV=staging`.

### on terminal
{:class="cb-title"}
```bash
# change the current environment
export NEKONOTE_ENV=staging

# display the current environment
nekonote env
```
{:class="code-block-command"}

```
staging
```
{:class="code-block-result"}

It's ok to type `NEKONOTE_ENV=staging` to specify some environment like following `NEKONOTE_ENV=staging nekonote server start`.



{% include doc-nav-link.html prev=site.data.document.contents.structure next=site.data.document.contents.configuration %}
