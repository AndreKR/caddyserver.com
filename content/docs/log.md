---
title: log
type: docs
directive: true
---

log enables request logging. The request log is also known from some vernaculars as an access log.

### Syntax

<code class="block"><span class="hl-directive">log</span></code>

*   With no arguments, an access log is written to access.log in the common log format for all requests (base path = /).

<code class="block"><span class="hl-directive">log</span> <span class="hl-arg"><i>file</i></span></code>

*   **file** is the log file to create (or append to). The base path is assumed to be /.

<code class="block"><span class="hl-directive">log</span> <span class="hl-arg"><i>path file </i>[<i>format</i>]</span></code>

*   **path** is the base path to match in order to be logged
*   **file** is the log file to create (or append to), relative to current working directory
*   **format** is the log format to use (default is Common Log Format)


### Log File

The log file can be any filename. It could also be `stdout` or `stderr` to write the log to the console, or `syslog` to write to a system log (see below). If the log file does not exist beforehand, Caddy will create it before appending to it.

If using **syslog** as _logfile_ to log to a system log (local or remote)

- `syslog` log to local system log (except on windows)
- `syslog://host[:port]` - logs via UDP to local or remote syslog server
- `syslog+udp://host[:port]` - logs via UDP to local or remote syslog server
- `syslog+tcp://host[:port]` - logs via TCP to local or remote syslog server


### Log Format

You can specify a custom log format with any [placeholder](/docs/placeholders) values. Log supports both request and response placeholders.

Currently there are two predefined formats.

* **{common}** (default) - `{remote} - {user} [{when}] "{method} {uri} {proto}" {status} {size}`
* **{combined}** - {common} appended with `"{>Referer}" "{>User-Agent}"`

### Log Rotation

Log rotation is enabled by default. Log files will be automatically rotated when they get large and deleted when they get old.

You can customize rotation by opening a block on your first line, which can be any of the variations described above:

<code class="block"><span class="hl-directive">log</span> <span class="hl-arg">...</span> {
    <span class="hl-subdirective">rotate_size</span> <i>maxsize</i>
    <span class="hl-subdirective">rotate_age</span>  <i>maxage</i>
    <span class="hl-subdirective">rotate_keep</span> <i>maxkeep</i>
}</code>

*   **maxsize** is the maximum size of a log file in megabytes (MB) before it gets rotated. Default is 100 MB.
*   **maxage** is the maximum age of a rotated log file in days, after which it will be deleted. Default is to delete old files after 14 days.
*   **maxkeep** is the maximum number of rotated log files to keep. Default is to retain up to 10 old log files.

### Examples

Log all requests to a file:

<code class="block"><span class="hl-directive">log</span> <span class="hl-arg">/var/log/access.log</span></code>

Custom log format:

<code class="block"><span class="hl-directive">log</span> <span class="hl-arg">/ ../access.log "{proto} Request: {method} {path}"</span></code>

Predefined format:

<code class="block"><span class="hl-directive">log</span> <span class="hl-arg">/ stdout "{combined}"</span></code>

With custom rotation:

<code class="block"><span class="hl-directive">log</span> <span class="hl-arg">access.log</span> {
    <span class="hl-subdirective">rotate_size</span> 200 <span class="hl-comment"># Rotate after 200 MB; the default is 100 MB</span>
    <span class="hl-subdirective">rotate_age</span>  30  <span class="hl-comment"># Keep old log files for 30 days; the default is 14 days</span>
    <span class="hl-subdirective">rotate_keep</span> 20  <span class="hl-comment"># Keep at most 20 log files; the default is 20 log files</span>
}</code>
