ThirdPartyCrawler
=================

Crawler for analysing third party referrals between websites.  

There are three components:
1. A PHP web application that co-ordinates multiple crawlers and stores crawl logs
2. A Python client that spawns Firefox instances to visit websites, plus a Firefox extension that records referer headers
3. A Javascript page that processes the stored logs to extract edge lists and other statistics


Crawls consist of a set of URLs.  Crawls are stored in the server component (in a MySQL database) and are handed out to crawlers by the server component.  Crawlers create instances of Firefox and then visit all the URLs in the crawl.  The included Firefox extension logs information about the crawl and, at the end of the crawl, the log is submitted to the server.

( The mix of languages is not intentional - We had a log processor in JS, so we re-used it and the selenium libraries for PHP weren't ready when we started so we used Python. )


Server Component
----------------

Available in /server/

Provides an API for the client and log processor.  Has two main roles:
1. Hand out crawl information to the crawlers
2. Receive and store crawl logs from the crawlers

The log processor also talks to the server component to retrieve and annotate the received logs.



Client Component
----------------

The python client spawns a number of Firefox processes and instructs them visit the pages in each crawl.  Browser automation is accomplished via the selenium framework ("pip install selenium" to get it)

The firefox extension collects information about cookies and referer headers.  This information is submitted, as plaintext, to the server at the end of each crawl.  The extension could be modified to collect other kinds of information with little or no modification to the server or client components. (The log processor would need to be updated to process the new logs, obviously...)


Log Processor
-------------

The log processor is a Javascript page (that we normally store alongside the PHP server component) which downloads logs from the server, processes them, and then sends the results of processing back to the server to be stored alongside the original logs.  It might be preferable to store the results elsewhere, or write an alternative processor.

The log processor can be left running in a browser on one of the machines.  In chrome it's pretty quick and a single instance can easily keep up with multiple machines running many simulataneous crawls.
