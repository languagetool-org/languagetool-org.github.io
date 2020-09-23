# How to avoid mistakes when writing rules

There are many ways you can make mistakes in grammar rules. In case of XML-formatted rule files, there are recurrent mistakes such as:

* Hasty generalization
* Bad suggestions
* Errors in regular expressions
* Badly encoded exceptions
* Untested corrections
* Lack of exceptions for skipping

## Hasty generalization

Hasty generalization creates false positives (or reduces precision) of 
rules. It's advisable to look at community reports at [community 
website](http://community.languagetool.org) and try to remove 
over-general rules.

### How to fix:


* Try adding exceptions.
* If this doesn't help, you could add disambiguating rules (if the 
  language has a disambiguator). Or add a disambiguator.

## Bad suggestions

Sometimes, instead of correct forms, suggestions contain only messages 
that explain the kind of error. Note that OpenOffice.org will offer 
these suggestions as replacement for the matched error in the document.

### How to fix:

* Offer only real suggestions in the `<suggestion/>` tag.
* Add `correction` attribute to the `example` marked as 
  `incorrect="yes"` to test whether the suggestion offered is correct 
  during JUnit tests.

## Errors in regular expressions


One of the common problems is not using parentheses () to group 
disjunctive groups in case of regular expressions with spaces. For 
example, "A" would match any POS tag in case it contains "A". But when 
used in an exception, you want to exclude exactly A. This is a bad way:


    <exception postag_regexp="yes" postag="!A"/>


The correct form:


    <exception postag="A"/>


This way it would match the POS tag as a whole string – this is what 
you actually want. Regular expressions have limited ways of expressing 
negation (via sets like this: `[^A]`) but using something inside an 
exception enables you to negate the POS tag. In normal tokens, you can 
use `negate_pos="yes"` as a negation operator, like here:


    <token negate_pos="yes" postag="A"/>


### How to fix:


* Test your regular expressions. There is a great plugin for Eclipse 
  called 
  [QuickREx](http://www.bastian-bergerhoff.com/eclipse/features/web/QuickREx/toc.html) 
  and other similar tools.
* Look out for spaces!
* Try to put into examples the forms matched by many different parts of 
  the regular expression so that they will be automatically tested.

## Badly encoded exceptions

Exceptions in the rules can remain untested if they are not accompanied 
by `example` marked as `correct`. Otherwise, you don't really know if 
the exception does work.

### How to fix:

* Add real-world examples which were intended to be excluded by 
  exceptions as correct examples. There can be many correct examples for 
  a rule, use it.

## Untested corrections

Sometimes the corrections are quite not like the author intended - the 
ordering of tokens encoded as `\1`, `\2` or `<match no="1".../>` can be 
broken, etc.

### How to fix:

* Make an incorrect example and add `correction` attribute to it to see 
if what your suggestion produces, especially if you're creating a 
complex suggestion that uses existing tokens, changes their grammatical 
form etc.

## Lack of exceptions for skipping

Skipping enables matching non-contiguous sequences of tokens. However, 
some sequences (such as noun phrases or verb phrases) might be broken 
by punctuation characters, intervening connectives, other verb forms 
etc. In Constraint Grammar, there is a notion of Barrier that specifies 
such breaking-elements. In LanguageTool, we use exceptions for skipping 
(with `scope="next"`). Add as many exceptions as necessary.

### How to fix:

* Don't leave `skip="1"` on a token without an accompanying exception 
  with `scope="next"` (default value of the `scope` attribute). This 
  exception will be matched over the skipped tokens.

