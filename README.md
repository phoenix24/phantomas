phantomas
=========

![GitHub Logo](http://upload.wikimedia.org/wikipedia/en/a/a5/Fantomas.jpg)

PhantomJS-based modular web performance metrics collector. And why phantomas? Well, [because](http://en.wikipedia.org/wiki/Fantômas) :)

[![NPM version](https://badge.fury.io/js/phantomas.png)](http://badge.fury.io/js/phantomas)
[![Build Status](https://api.travis-ci.org/macbre/phantomas.png)](http://travis-ci.org/macbre/phantomas)
[![Bitdeli Badge](https://d2weczhvl823v0.cloudfront.net/macbre/phantomas/trend.png)](https://bitdeli.com/free "Bitdeli Badge")

## Requirements

* [NodeJS](http://nodejs.org)
* [PhantomJS 1.9+](http://phantomjs.org/)

## Installation

```
npm install --global phantomas
```

> This will install the latest version of PhantomJS and add a symlink called ``phantomas`` (pointing to ``./bin/phantomas.js``) to your system's ``PATH``

## Features

* Modular approach - each metric is generated by a separate "module"
* phantomas "core" acts as an [events emitter](https://github.com/macbre/phantomas/wiki/Events) that each module can hook into
* in-depth metrics such as: number of events bound via jQuery, calls to ``window.write``or [complex and duplicated CSS selectors (via analyze-css)](https://github.com/macbre/analyze-css)
* JSON and CSV as available output formats for easy integration with automated reporting / monitoring tools
* easy integration with Continous Integration tools via TAP format and assertions handling
* easy integration with other nodejs projects via CommonJS module ([see API docs](https://github.com/macbre/phantomas/wiki/npm-module))
* metrics can be emitted from JavaScript code of the page phantomas is run against (thanks to [helper functions available in window.__phantomas](https://github.com/macbre/phantomas/wiki/Phantomas-scope))

## Contributors

* [macbre](https://github.com/macbre)
* [jmervine](https://github.com/jmervine)
* [jmosney](https://github.com/jmosney)
* [umaar](https://github.com/umaar)
* [sjhcockrell](https://github.com/sjhcockrell)
* [cphoover](https://github.com/cphoover)
* [LaurentGoderre](https://github.com/LaurentGoderre)
* [kennydee](https://github.com/kennydee)
* [iNem0o](https://github.com/iNem0o)
* All the [contributors](https://github.com/macbre/phantomas/graphs/contributors)

## Usage

> phantomas comes as both command line tool and CommonJS module ([see API docs](https://github.com/macbre/phantomas/wiki/npm-module)) that you can use in your nodejs projects.

### Single run

``` bash
phantomas --url https://github.com/macbre/phantomas --verbose
```

You can measure the performance of your site without requests to 3rd party domains (but allowing CDN that serves your static assets):

```bash
phantomas --url https://github.com/macbre/phantomas --verbose --no-externals --allow-domain .fastly.net
```

#### Parameters

* `--url` URL of the page to generate metrics for (required)
* `--format=[json|csv|tap|plain]` output format (``plain`` is the default one)
* `--timeout=[seconds]` timeout for phantomas run (defaults to 15 seconds)
* `--viewport=[width]x[height]` phantomJS viewport dimensions (1280x1024 is the default)
* `--verbose` writes debug messages to the console
* `--silent` don't write anything to the console
* `--log=[log file]` log to a given file
* `--modules=[moduleOne],[moduleTwo]` run only selected modules
* `--skip-modules=[moduleOne],[moduleTwo]` skip selected modules
* `--user-agent='Custom user agent'` provide a custom user agent (will default to something similar to ``phantomas/0.6.0 (PhantomJS/1.9.0; linux 64bit)``)
* `--config=[JSON config file]` uses JSON-formatted config file to set parameters
* `--cookie='bar=foo;domain=url'` document.cookie formatted string for setting a single cookie
* `--cookies-file=[JAR file]` specifies the file name to store the persistent Cookies
* `--no-externals` block requests to 3rd party domains
* `--allow-domain=[domain],[domain]` allow requests to given domain(s) - aka whitelist
* `--block-domain=[domain],[domain]` disallow requests to given domain(s) - aka blacklist
* `--disable-js` disable JavaScript on the page that will be loaded
* `--analyze-css` emit in-depth CSS metrics **experimental**
* `--film-strip` register film strip when page is loading **experimental**
* `--film-strip-dir=[dir path]` folder path to output film strip (default is ``./filmstrip`` directory) **experimental**
* `--assert-[metric-name]=value` assert that given metric should be less or equal the value
* `--screenshot=[file name]` render fully loaded page to a given file
* `--wait-for-selector=[CSS selector` wait for an element matching given CSS selector before generating a report, timeout setting still applies (e.g. ``--wait-for-selector "body.loaded"``)
* `--post-load-delay=[seconds]` wait X seconds before generating a report, timeout setting still applies
* `--ignore-ssl-errors` ignores SSL errors, such as expired or self-signed certificate errors
* `--proxy=[host:port]` specifies the proxy server to use
* `--proxy-auth=[username:password]` specifies the authentication information for the proxy
* `--proxy-type=[http|socks5|none]` specifies the type of the proxy server (default is http)

### Multiple runs

``` bash
./run-multiple.js --url=https://github.com/macbre/phantomas  --runs=5
```

#### Parameters

* `--url` URL of the page to generate metrics for (required)
* `--runs` number of runs to perform (defaults to 3)
* `--modules=[moduleOne],[moduleTwo]` run only selected modules
* `--skip-modules=[moduleOne],[moduleTwo]` skip selected modules

## Metrics

_Current number of metrics: 96_

Units:

* ms for time
* bytes for size

### Requests monitor (core module)

> Due to [PhantomJS issue #10156](https://github.com/ariya/phantomjs/issues/10156) **body size related metrics are not reliable**

* requests: total number of HTTP requests made
* gzipRequests: number of gzipped HTTP responses
* postRequests: number of POST requests
* httpsRequests: number of HTTPS requests
* redirects: number of HTTP redirects (either 301 or 302)
* notFound: number of HTTP 404 responses
* timeToFirstByte: time it took to receive the first byte of the first response (that was not a redirect)
* timeToLastByte: time it took to receive the last byte of the first response (that was not a redirect)
* bodySize: size of the content of all responses
* contentLength: size of the content of all responses (based on ``Content-Length`` header)
* httpTrafficCompleted: time it took to receive the last byte of the last HTTP response

### AJAX requests

* ajaxRequests: number of AJAX requests

### Assets types

> Due to [PhantomJS issue #10156](https://github.com/ariya/phantomjs/issues/10156) **body size related metrics are not reliable**

* htmlCount: number of HTML responses
* htmlSize: size of HTML responses
* cssCount: number of CSS responses
* cssSize: size of CSS responses
* jsCount: number of JS responses
* jsSize: size of JS responses
* jsonCount: number of JSON responses
* jsonSize: size of JSON responses
* imageCount: number of image responses
* imageSize: size of image responses
* webfontCount: number of web font responses
* webfontSize: size of web font responses
* base64Count: number of base64 encoded "responses" (no HTTP request was actually made)
* base64Size: size of base64 encoded "responses"
* otherCount: number of other responses
* otherSize: size of other responses

### Cache Hits

> Metrics are calculated based on ``X-Cache`` header added by Varnish  / Squid servers

* cacheHits: number of cache hits
* cacheMisses: number of cache misses
* cachePasses: number of cache passes

### Headers

* headersCount: number of requests and responses headers
* headersSentCount: number of headers sent in requests
* headersRecvCount: number of headers received in responses
* headersSize: size of all headers
* headersSentSize: size of sent headers
* headersRecvSize: size of received headers

### Domains

* domains: number of domains used to fetch the page
* maxRequestsPerDomain: maximum number of requests fetched from a single domain
* medianRequestsPerDomain: median of requests fetched from each domain

### Cookies

* cookiesSent: length of cookies sent in HTTP requests
* cookiesRecv: length of cookies received in HTTP responses
* domainsWithCookies: number of domains with cookies set
* documentCookiesLength: length of `document.cookie`
* documentCookiesCount: number of cookies in `document.cookie`

### DOM complexity

> Metrics listed below are generated after the full page load

* globalVariables: number of JS globals variables
* bodyHTMLSize: the size of body tag content (``document.body.innerHTML.length``)
* commentsSize: the size of HTML comments on the page
* hiddenContentSize: the size of content of hidden elements on the page (with CSS ``display: none``)
* whiteSpacesSize: the size of text nodes with whitespaces only
* DOMelementsCount: total number of HTML element nodes
* DOMelementMaxDepth: maximum level on nesting of HTML element node
* iframesCount: number of iframe nodes
* nodesWithInlineCSS: number of nodes with inline CSS styling (with `style` attribute)
* imagesWithoutDimensions: number of ``<img>`` nodes without both ``width`` and ``height`` attribute

### DOM queries

* DOMqueries: the sum of all four metrics below
* DOMqueriesById: number of `document.getElementById` calls
* DOMqueriesByClassName: number of `document.getElementsByClassName` calls
* DOMqueriesByTagName: number of `document.getElementsByTagName` calls
* DOMqueriesByQuerySelectorAll: number of `document.querySelectorAll` calls
* DOMinserts: number of DOM nodes inserts
* DOMqueriesDuplicated: number of duplicated DOM queries

### Event listeners

* eventsBound: number of ``EventTarget.addEventListener`` calls

### Window performance

> Times below are relative to ``responseEnd`` entry in NavigationTiming (represented by ``timeToLastByte`` metric). See [NavigationTiming spec](http://w3c-test.org/webperf/specs/NavigationTiming/) for more information.

* onDOMReadyTime: time it took to fire onDOMready event
* onDOMReadyTimeEnd: time it took to finish processing onDOMready event **experimental**
* windowOnLoadTime: time it took to fire window.load event
* windowOnLoadTimeEnd: time it took to finish processing window.load event **experimental**

### Requests statistics

> **Time** is total duration, from the start of the request to the receipt of the final byte in the response. **Latency** is the time to load the first byte in the response.
> https://developers.google.com/chrome-developer-tools/docs/network
>
> Includes ``HTTP 200`` responses only

* smallestResponse: the size of the smallest response
* biggestResponse: the size of the biggest response
* fastestResponse: the time to the last byte of the fastest response
* slowestResponse: the time to the last byte of the slowest response
* smallestLatency: the time to the first byte of the fastest response
* biggestLatency: the time to the first byte of the slowest response
* medianResponse: median value of time to the last byte for all responses
* medianLatency: median value of time to the first byte for all responses

### localStorage

* localStorageEntries: number of entries in local storage

### jQuery

> Requires jQuery 1.8.0+

* jQueryVersion: version of jQuery framework (if loaded)
* jQueryOnDOMReadyFunctions: number of functions bound to onDOMReady event
* jQuerySizzleCalls: number of calls to [Sizzle](http://sizzlejs.com/) (including those that will be resolved using ``querySelectorAll``)

### Static assets

* assetsNotGzipped: static assets that were not gzipped
* assetsWithQueryString: static assets requested with query string (e.g. ?foo) in URL
* smallImages: images smaller than 2 kB that can be base64 encoded
* multipleRequests: number of static assets that are requested more than once

### Caching

* cachingNotSpecified: responses with no caching header sent (either `Cache-Control` or `Expires`)
* cachingTooShort: responses with too short (less than a week) caching time
* cachingDisabled: responses with caching disabled (`max-age=0`)

### Time to first asset

* timeToFirstCss: time it took to receive the last byte of the first CSS
* timeToFirstJs: time it took to receive the last byte of the first JS
* timeToFirstImage: time it took to receive the last byte of the first image

### JavaScript bottlenecks

* documentWriteCalls: number of calls to either ``document.write`` or ``document.writeln``
* evalCalls: number of calls to ``eval`` (either direct or via ``setTimeout`` / ``setInterval``)

### JavaScript errors

* jsErrors: number of JavaScript errors

### JavaScript console and alert

* windowAlerts: number of calls to ``alert``
* windowConfirms: number of calls to ``confirm``
* windowPrompts: number of calls to ``prompt``
* consoleMessages: number of calls to ``console.*`` functions

### CSS metrics

> This is an experimental feature. Use `--analyze-css` option to enable it.

Take a look at [analyze-css README](https://github.com/macbre/analyze-css) for the full list of metrics.

## For developers

* [Project's wiki](https://github.com/macbre/phantomas/wiki)
* Description of [events fired by phantomas core](https://github.com/macbre/phantomas/wiki/Events)
* Description of [helper functions available to the browser in window.__phantomas](https://github.com/macbre/phantomas/wiki/Phantomas-scope)

## Let's make Web a bit faster!

* [Best Practices for Speeding Up Your Web Site](http://developer.yahoo.com/performance/rules.html) (by Yahoo!)
* [Web Performance Best Practices](https://developers.google.com/speed/docs/best-practices/rules_intro) (by Google)
* [Writing Efficient CSS](http://developer.mozilla.org/en/Writing_Efficient_CSS) (by Mozilla)
* [Planet Performance](http://www.perfplanet.com/) - news and views from the web performance blogosphere
* [Performance of 3rd Party Content](http://stevesouders.com/p3pc/) (by Steve Souders)
* [Profiling CSS for fun and profit. Optimization notes.](http://perfectionkills.com/profiling-css-for-fun-and-profit-optimization-notes/)
* [phantomas – PhantomJS based, modular web performance metrics generator](http://calendar.perfplanet.com/2013/phantomas/) (an article for Performance Calendar)
* [How to Measure Frontend Performance With Grunt](http://4waisenkinder.de/blog/2013/12/22/how-to-measure-frontend-performance-with-phantomas-and-grunt/)

### Slides

* [Know Your Engines: How to Make Your JavaScript Fast](http://cdn.oreillystatic.com/en/assets/1/event/60/Know%20Your%20Engines_%20How%20to%20Make%20Your%20JavaScript%20Fast%20Presentation%201.pdf) (by David Mandelin from Mozilla)
* [Velocity Conf 2013 Workshop: Avoiding Web Performance Regression](http://www.slideshare.net/marcelduran/velocity-conf-2013) (by Marcel Duran from Twitter)

## Utilities

* [grunt-phantomas](https://github.com/stefanjudis/grunt-phantomas)
