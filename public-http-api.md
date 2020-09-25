# Public HTTP Proofreading API

We offer a simple HTTPS REST-style service that anybody can use to check texts
with [LanguageTool](https://languagetool.org). The only public endpoint is the
following one - do not send your requests to any other endpoints you might find in the homepage's HTML code
or elsewhere:

```
https://languagetool.org/api/v2/check
```

When using it, please keep the following rules in mind:

* Do not send automated requests. For that, set up [your own instance of LanguageTool](http-server) or
  get [an account for Enterprise use](https://languagetoolplus.com/#premium).
* Only send POST requests, not GET requests.
* Access is currently limited to:
  * 20 requests per IP per minute (this is supposed to be a peak value - don't constantly send this many requests or we would have to block you)
  * 75KB text per IP per minute
  * 20KB text per request
  * Only up to 30 misspelled words will have suggestions.
  * These limits may change anytime.
* Only HTTPS is supported, not HTTP.
* This is a free service, thus there are no guarantees about performance or availability.
* The LanguageTool version installed may be the latest official release or some snapshot. We will simply
  deploy new versions, thus the behavior might change without any warning.
* Read [our privacy policy](https://www.languagetool.org/privacy) to see how we handle
  your texts. You are responsible for giving your users information about how their data is handled.
* **We expect you to add a link back to <https://languagetool.org> that's clearly visible.**

### API documentation

**See [the JSON API](https://languagetool.org/http-api/swagger-ui/#/default).**

### Libraries to simplify usage

* Java: LanguageTool comes with [RemoteLanguageTool](https://languagetool.org/development/api/org/languagetool/remote/RemoteLanguageTool.html)
  in the [languagetool-http-client](https://search.maven.org/search?q=a:languagetool-http-client) module
* Python: [pyLanguagetool](https://github.com/Findus23/pyLanguagetool)
