# Public HTTP Proofreading API

We offer a simple HTTPS REST-style service that anybody can use to check texts
with [LanguageTool](https://languagetool.org). The only public endpoint is the
following one - do not send your requests to any other endpoints you might find in the homepage's HTML code
or elsewhere:

```
https://api.languagetool.org/v2/check
```

Example usage:

```
curl -d "text=This is an test." -d "language=auto" https://api.languagetool.org/v2/check
```

When using it, please keep the following rules in mind:

* Do not send automated requests. For that, set up [your own instance of LanguageTool](http-server) or
  get [an account for Enterprise use](https://languagetool.org/proofreading-api).
* Only send POST requests, not GET requests.
* Access is currently limited to:
  * 20 requests per IP per minute (this is supposed to be a peak value - don't constantly send this many requests or we would have to block you)
  * 75KB text per IP per minute
  * 20KB text per request
  * Only up to 30 misspelled words will have suggestions.
* This is a free service, thus there are no guarantees about performance or availability. The limits may change anytime.
* The LanguageTool version installed may be the latest official release or some snapshot. We will simply
  deploy new versions, thus the behavior will change without any warning.
* Read [our privacy policy](https://languagetool.org/legal/privacy) to see how we handle
  your texts. You are responsible for giving your users information about how their data is handled.
* **We expect you to add a link back to <https://languagetool.org> that's clearly visible.**

### API documentation

**See [the JSON API](https://languagetool.org/http-api/swagger-ui/#/default).**

### Libraries to simplify usage

* Python: [pyLanguagetool](https://github.com/Findus23/pyLanguagetool)
* Rust: [LanguageTool-Rust](https://github.com/jeertmans/languagetool-rust)
* R: [ggspell](https://github.com/nicucalcea/ggspell)
