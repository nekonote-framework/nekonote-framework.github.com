---
title: 16. Logger
permalink: /document/1.0.0-beta/logger
layout: article
is_doc: true
---

Logging data in {{site.product}} is multiprocess-safety and thread-safety.
When you trun on the logger in `logger.yml`, `logwrite` method is callable in top level.


{% include toc.html %}

# Configuring the Logger

It's possible to configure the logger through `logger.yml`.

name|parent|description|default value
:-|:-|:-|:-
**enabled**|-|false to disable logger.<br />true to enable logger.|true
**log file**|-|Path to a log file which is supposed to be written log messages.<br>It's possible to specific relative path from application root or absolute path. <code>%s</code> in the value can be replaced with the current environment name.|log/%s.log
**write_exception**|-|Configures if the logger writes the exception details to the log file when some exception raised.<br>false to the logger never writes the exception details to the log file.<br>true to the logger writes the exception details to the log file.|true
**format**|-|Configures a log message format.|-
**log**|format|The log message format.|"$DATE [$LEVEL] $LOG"
**datetime**|format|The date format for the variable $DATE in 'log' directive. It can be configured as Date#strftime.|"%a %d %b %H:%M:%S"
**threshold**|-|Sets from which log severity levels are should be reported to the log file.|debug
**default_log_level**|-|The default log severity level for log messages which reported through 'logwrite' method.|info
**limit**|-|Sets the threshold to rotate log files.|1G
**keep**|-|Sets the maximum number of old log files.<br>0 to keeping all old log files.<br>Positive intergers to keeping old log files as the number set here.|0
**compress**|-|Configures if old log files are compressed when rotation is done.|-
**enabled**|compress|false to old log files will no be compressed.<br>true to old log files will be compressed by gzip.|false
**level**|compress|The compression level for gzip. It's supposed to be set an integer in range of 1 to 9.|6
**with_stdout**|-|false to log messages are written to only log files.<br>true to log messages are written to stdout as well.|false
**verbose**|-|false to the warning messsages are ignored.<br>ture to the warning messages are written to the log files.|true
**psync**|-|When this directive is set 'true', the logger can be multi-processes safe. The 'flush' directive is enabled forcibly when it's enabled.<br>false to disable syncronization with other processes.|true
**sleeptime**|-|Program will wait for this seconds after rotation.|0.1
**flush**|-|false to disbale flushing buffered data after writing a log message to the log file.<br>true to enable flushing buffered data.|true

# Turning on the Logger

You need to turn on the logger by `logger.yml` if the logger is disabled.
The logger will be enabled by default after lintalling {{site.product}}. So there is no need to do for using the logger.

There is the directive in `logger.yml` to turn on/off the logger.

### preference/your_environment/logger.yml
{:class="cb-title"}

```
enabled: true
```

When `true` is set to `enabled` directive `logwrite` method will be defined as public instance method of Object class.
When there is a need to turn off the logger, you need to set `false` to the 'enabled' directive.

# Logging Data

When the logger is enabled, it's possible to call `logwrite` method anywhere you want because `logwrite` is a public instance method of Object class.

Here is the reference to `logwrite` method.

### logwrite(msg, level = nil)
{:class="ck-title"}

parameter|type|required|description
:-|:-|:-|
**msg**|mixed|yes|A log message to report.
**level**|int|no|Some log severity level for the log message.

When `log file: log/%s.log` is set in `logger.yml`, the log file will be created to `/path/to/app/root/log/your_environment.log` when `logwrite` is called.

## Logging Data Example

```rb
logwrite 'log message'
```

The above example will write `log message` similar into the log file as `Thu 04 May 06:09:19 [INFO] log message`.

`$LEVEL` was replaced `INFO` because the default log severity level is set to `info`.
You may change the default log severity level by `default_log_level` directive in `logger.yml`.

The second argument for `logwrite` sets the log severity level for to its value which for the given log message.

Let's change the log severity level to `fatal`.
The following example reports the log message as `fatal` level.

```rb
logwrite 'log message', Nekonote::Logger::FATAL
```

The above example will write `Thu 04 May 06:09:19 [FATAL] log message` similar into the log file.

<p class="tip">When the second argument passed to <code>logwrite</code> method, the default log severity level is set to the passed value automatically.</p>

These are the specifiable constant values for the second argument of `logwrite` (log severity levels).

Here is the list of constants which can be passed to the second argument to `logwrite`.

constant name|value|log severity level
:-|:-|:-
Nekonote::Logger::DEBUG|0|debug
Nekonote::Logger::INFO|1|info
Nekonote::Logger::WARN|2|warn
Nekonote::Logger::ERROR|3|error
Nekonote::Logger::FATAL|4|fatal

# Ignoring Uninportant Logs

Each of log messages has a severity level.

The following lists all severity levels in order of precedence: `fatal`, `error`, `warn`, `info`, `debug`.

When you don't need to be reported `debug` or `info` level log messages, you need to configure as:

### preference/your_environment/logger.yml
{:class="cb-title"}

```
threshold: warn
```

The above exmaple says log messages which log severity levels are `warn` or `error` or `fatal` will be written to the log file.
But log messages which are `debug` or `info` level will not be written to the log file.

```rb
logwrite 'DEBUG', Nekonote::Logger::DEBUG
logwrite 'INFO',  Nekonote::Logger::INFO
logwrite 'WARN',  Nekonote::Logger::WARN
logwrite 'ERROR', Nekonote::Logger::ERROR
logwrite 'FATAL', Nekonote::Logger::FATAL
```

The following result will be written similar to the log file.

```
Thu 04 May 07:24:34 [WARN] WARN
Thu 04 May 07:24:34 [ERROR] ERROR
Thu 04 May 07:24:34 [FATAL] FATAL
```

The result says `debug` or `info` levels were not reported.

# Formatting Log Messages

It's possible to customize the log message format by `log > format` directive in `logger.yml`.

There are the some special variables for formatting log messages. They will be replaced as the following list:

variable name|it will be replaced to
:-|:-
$DATE|The date and time.
$PID|The process id.
$LEVEL|The log severity level.
$LOG|The log message which passed by the first argument of 'logwrite' method.

To change the default log message format, you need to configure as following exmaple:

### preference/your_environment/logger.yml
{:class="cb-title"}

```
format:
    log: "$LEVEL $DATE $LOG $PID"
    datetime: "%a %d %b %H:%M:%S"
```

The default format for `$DATE` is `"%a %d %b %H:%M:%S"`. Let's change it to _RFC 822_ date-time.

```
format:
    log: "$LEVEL $DATE $LOG $PID"
    datetime: "%a, %d %b %Y %H:%M:%S %z"
```

After editing `logger.yml`, you need to restart the web server to reload the new configuration file.

```rb
logwrite 'log message'
```

The result will say similar:

```
INFO Fri, 05 May 2017 06:14:39 +0000 log message 10248
```

# Managing Log Files

The logger helps to manage your log files.

It can prevent eating up available hard drive space by single log file.

The logger can rotate your log files by removing the oldest ones and creating new ones.
That will be done automatically when the threshold reached which set in `logger.yml`.

## Rotation by File-Size

If some value is set to `limit` directive in `logger.yml`,
The logger rotates files when file size of the log file reached the threshold set in `logger.yml`.

The value of `limit` directive needs to be set as an integer, but it can be put `K`, `M`, `G` at the end of the value.
`K` means 'kilobytes', `M` means 'megabytes', `G` means 'gigabytes'.

No `K`, `M`, `G` at the end of the value is assumed that it's just 'byte'.

### preference/your_environment/logger.yml
{:class="cb-title"}

```bash
limit: 500M #=> log files are rotated only if their file-size is bigger than 500 megabytes
```

## Rotating Files Periodically

When `"DAILY"` or `"WEEKLY"` or `"MONTHLY"` is set in `limit` directive, log files are rotated daily or weekly or monthly.

```bash
limit: WEEKLY #=> log files are rotated weekly
```

## Keeping Log Files

After rotation done, the log file which reached threshold is keeped as an old log file.
You may configure that how many old log files are supposed to be keeped in your server.

When the logger rotated log files, 

The logger creates a new file and log messages will be written to the new one after rotating.
Typically The previous one is retained as old file.

You may configure how many old log files are supposed to be keeping.

The default value of `keep` directive is set `0`. The meaning of `0` is keeping all old log files and any log files are not removed by rotation.

### example

When you need to be kept four of old log files, you need to set `4` to `keep` directive in `logger.yml`.

Old log files can be compressed as well.
You need to set as following:

### preference/your_environment/logger.yml
{:class="cb-title"}

```
keep: 4

compress:
    enabled: true # set compression enabled
    level: 6
```

The above `level` directive means compression level for gzip. Old log files are compressed as this gzip compression level.

You may change it in the range from `1` to `9`.
Larger numbers mean higher compression but it will take more time to compress files than smaller number.

{% include doc-nav-link.html prev=site.data.document.contents.exception next=site.data.document.contents.template %}
