
### How does it work?
- tells search engine crawlers which URLs the crawler can access on the site. 
- prevents overloading the site with requests
- the file isn't accessible through a search engine
- robots.txt can be used to block resource files if you think that pages loaded without the resources will not be affected by the loss of its presence
- some crawlers refer to the robots.txt to know what they're allowed to touch, others just completely ignore the file and scrape whatever they can
- all subdomains need their own robots.txt file

### What protocols are used in a robots.txt file?
- main protocol is **Robots Exclusion Protocol** which tells bots which webpages and resources to avoid. Instructions formatted for this protocol are in the robots.txt file
- the other protocol is **Sitemaps Protocol** (aka **Robots Inclusion Protocol**). shows the web crawler which pages they can crawl

### What is a User Agent? What does 'User-agent: \*' mean?

- anything active on the internet will have a **user agent**, or a name assigned to them.
	- for human users, this includes information like the browser type and the OS version, but doesn't include any personally identifiable information.
	- for bots, the user agent helps website administrators know what bots are crawling their site
- shows websites content that is compatible with the user's system.
- the syntax 'User-agent: \*' means that the rules below apply to all bots

### How do 'Disallow' commands work in the file?

- Most common in the robots exclusion protocol
- Tells bots not to access the webpage or set of webpages that come after the command


| Action                          | Robots Exclusion Protocol Example             | Description                                                                  |
| ------------------------------- | --------------------------------------------- | ---------------------------------------------------------------------------- |
| Blocking one file (one webpage) | ```Disallow: /learning/bots/what-is-a-bot/``` | prevents the bots from accessing the webpage mentioned                       |
| Blocking one directory          | ```Disallow: /_mesa/```                       | all pages contained in the /\_mesa/ directory shouldn't be crawled           |
| Allowing full access            | ```Disallow:```                               | tells bots they can browse the entire website, because nothing is disallowed |












Just adding this to the bottom, but I definitely want to build a robots-compliant bot. Refer to "[[Robots-compliant Guidelines]]" to make sure that the code remains ethical