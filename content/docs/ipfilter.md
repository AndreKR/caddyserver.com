---
title: ipfilter
type: docs
directive: true
plugin: true
link: https://github.com/pyed/ipfilter
---

ipfilter blocks or allows requests based on the client's IP.

**Requirements:** To filter clients based on countries codes, This directive will require having a local copy of [MaxMind's GeoLite2 Country database](https://dev.maxmind.com/geoip/geoip2/geolite2/) (it's free).

### Syntax

<code class="block"><span class="hl-directive">ipfilter</span> <span class="hl-arg"><i>paths...</i></span> {
	<span class="hl-subdirective">rule</span>       <i>block | allow</i>
	<span class="hl-subdirective">ip</span>         <i>list or/and range of IPs...</i>
	<span class="hl-subdirective">country</span>    <i>countries ISO codes...</i>
	<span class="hl-subdirective">database</span>   <i>db_path</i>
	<span class="hl-subdirective">blockpage</span>  <i>block_page</i>
	<span class="hl-subdirective">strict</span>
}</code>

*   **paths...** is a list of space-separated base paths to which to apply this filter rule (use / to match everything).
*   **rule** either block or allow.
*   **ip...** space-seperated IPs or ranges of IPs in [CIDR notation](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).
*   **country...** list of space-separated country ISO codes. For a complete list of country codes, refer to [Wikipedia](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2#Officially_assign).
*   **db_path** is the path to the geo-IP database (relative to working directory). required only if the subdirective 'country' is used.
*   **block_page** is an optional file that will be shown to blocked clients (relative to working directory).
*   **strict** if specified ipfilter will not look into `X-Forwarded-For` header, this flag is essential if you are using [realip](/docs/realip).

### Examples

Only serve one IP address, and block everyone else.

<code class="block"><span class="hl-directive">ipfilter</span> <span class="hl-arg">/</span> {
	<span class="hl-subdirective">rule</span> allow
	<span class="hl-subdirective">ip</span>   93.168.247.245
}</code>

Block two ranges of IPs as well as one specific IP, and show them default.html instead.

<code class="block"><span class="hl-directive">ipfilter</span> <span class="hl-arg">/</span> {
	<span class="hl-subdirective">rule</span>       block
	<span class="hl-subdirective">ip</span>         192.168.0.0/16 2E80::20:F8FF:FE31:77CF/16 5.23.4.24
	<span class="hl-subdirective">blockpage</span>  /local/data/default.html
}</code>

Only allow clients from France as well as two specific IP addresses

<code class="block"><span class="hl-directive">ipfilter</span> <span class="hl-arg">/</span> {
	<span class="hl-subdirective">rule</span>      allow
	<span class="hl-subdirective">country</span>   FR
	<span class="hl-subdirective">database</span>  /local/data/GeoLite2-Country.mmdb
	<span class="hl-subdirective">ip</span>        99.23.4.24 2E80::20::FEF1:91C4
}</code>

Only serve clients from the United States or Japan:

<code class="block"><span class="hl-directive">ipfilter</span> <span class="hl-arg">/</span> {
	<span class="hl-subdirective">rule</span>      allow
	<span class="hl-subdirective">country</span>   US JP
	<span class="hl-subdirective">database</span>  /local/data/GeoLite2-Country.mmdb
}</code>

Block requests coming from the US or Japan but only to /notglobal and /secret, showing them default.html instead:

<code class="block"><span class="hl-directive">ipfilter</span> <span class="hl-arg">/notglobal /secret</span> {
	<span class="hl-subdirective">rule</span>       block
	<span class="hl-subdirective">country</span>    US JP
	<span class="hl-subdirective">database</span>   /local/data/GeoLite2-Country.mmdb
	<span class="hl-subdirective">blockpage</span>  /local/data/default.html
}</code>

### Backward compatibility

ipfilter used to support different ways of specifiying ranges, CIDR now is the only notation supported, The old notations will get converted automatically, Although re-writing them in CIDR is highly recommended for efficiency.
