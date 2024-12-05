# Development Overview

This page has everything you need to know to teach [LanguageTool](https://languagetool.org) new error detection rules,
plus more. You don't even have to be a programmer for that.

# The three-minute introduction

This section tells you in a nutshell how to write your own LanguageTool rules for
detecting errors (see [this overview](https://languagetool.org/languages/) for
how many rules LanguageTool already knows for your language):

* Download [the stand-alone version of LanguageTool](https://languagetool.org/download/LanguageTool-stable.zip) and unzip it.
* Open `LanguageTool-x.y/org/languagetool/rules/en/grammar.xml` in your
  preferred text editor or in an XML editor, where `x.y` stands for the
  version number of the package you downloaded.
* Search for `name="Possible Typo"` and add this
  odd rule just after that `<category ...>` tag and save the file: 

```xml
    <rule id="EXAMPLE_RULE" name="My example rule">
        <pattern>
          <token>foo</token>
          <token>bar</token>
        </pattern>
        <message>Did you mean <suggestion>bicycle</suggestion>?</message>
        <example correction="bicycle">My <marker>foo bar</marker> is broken.</example>
        <example>My car is broken.</example>
    </rule>
```

* Run `languagetool.jar` by clicking it or by calling `java -jar languagetool.jar`
  on the command line.
* Select English as the text language and type something like *A foo bar test*,
  then start text checking.
* LanguageTool will now check your text and suggest *bicycle* as a replacement
  for *foo bar*, because that's what the rule which we just added says.
* Use our [online rule editor](http://community.languagetool.org/ruleEditor2/)
  to write rules if you don't want to edit the XML directly.

That's it! You have just added a new rule. Keep on reading to get a grasp on
what the elements of a rule mean and how to build more complex rules or
[use the rule creator](https://community.languagetool.org/ruleEditor2/index)
to build simple rules. Check out the
[text analyzer](http://community.languagetool.org/analysis/index?lang=en) to
get an understanding of how LanguageTool analyzes text internally.
[Send us](https://forum.languagetool.org/) your rules so we can add
them to the next release of LanguageTool. 

# Help wanted!

We're looking for people who support us writing new rules so LanguageTool
can detect more errors. Also see the
[list of supported languages](http://languagetool.org/languages/).

How can you help?

- Read this page (some features described here are quite advanced, so you won't need everything)
- Start writing rules for the error you'd like LanguageTool to detect
- See [our dev page](https://dev.languagetool.org/) for more tips and tricks
- Post your rules to [the forum](https://forum.languagetool.org) or [open an issue](https://github.com/languagetool-org/languagetool/issues) so we can include them in LanguageTool
- [Subscribe to our forum](https://forum.languagetool.org/t/how-to-use-this-forum-like-a-mailing-list/1067)

If your language isn't supported yet, you can add it by following the
[documentation in our wiki](/adding-a-new-language).

# Source code checkout

If you are a Java developer and you want to extend LanguageTool or if you want
to use the latest development version, check out LanguageTool from
[github](https://github.com/languagetool-org/languagetool/):

    git clone --depth=1 https://github.com/languagetool-org/languagetool.git

(omit the `--depth=1` switch if you're required to have the full git history
(>1.4 GB) repository, so cloning might take some time)

# Building the project

You can build the code with `mvn clean package` or just run the tests with 
`mvn clean test`. You need at least Java 17 for building LT. Maven's default 
memory settings are often too low, so you will probably need to set your 
environment variable `MAVEN_OPTS` to:

    -Xmx1550m

After the build, the LibreOffice/OpenOffice extension can be found in
`languagetool-office-extension/target` (named *.zip, rename it to *.oxt),
the stand-alone version in `languagetool-standalone/target` (in a sub
directory named e.g. `LanguageTool-5.0-SNAPSHOT/` - you cannot run
the *.jar directly in the `target` directory). See [Maven tips](/maven-tips)
for hints on how to build faster.

# Language checking process

This is what LanguageTool does when it analyzes a text for errors:

1. The text is split into sentences
2. Each sentence is split into words
3. Each word is assigned its part-of-speech tag(s) (e.g. *cars* = plural
   noun, *talked* = simple past verb)
4. The analyzed text is then matched against the built-in Java rules and
   against the rules loaded from the `grammar.xml` file

The most important thing you need to keep in mind is that LanguageTool's
rules describe what errors look like, not what correct sentences look
like (this is the opposite of how you learn a new language).

# Adding new XML rules

Most rules are contained in `rules/xx/grammar.xml`, whereas `xx` is a
language code like `en` or `de`. In the source code, this folder
will be found under `languagetool-language-modules/xx/src/main/resources/org/languagetool/`;
the standalone GUI version contains them under `org/languagetool/`.

A rule is basically a pattern which shows an error message to the
user if the pattern matches. A pattern can address words or
part-of-speech tags. Here are some examples of patterns that
can be used in that file: 

* `<token>think</token>`  
  matches the word *think*
* `<token>think</token> <token>about</token>`  
  Matches the phrase *think about* - as the text is split into words, you need to list
  each word separately as a token. This will not work:`<token>think about</token>`
* `<token regexp="yes">think|say</token>`  
  matches the regular expression `think|say`, i.e. the word *think* or the word *say*.
  You can write simple rules without knowing regular expressions, but if you want to
  learn more about them you can try [this tutorial](http://www.regular-expressions.info/tutorialcnt.html).
* `<token postag="VB" />`  
  matches a base form verb (like *walk* in *we have to walk*). See
  [resource/en/tagset.txt](https://github.com/languagetool-org/languagetool/blob/master/languagetool-language-modules/en/src/main/resources/org/languagetool/resource/en/tagset.txt)
  for a list of possible English part-of-speech tags.
* `<token postag="V.*" postag_regexp="yes" />`  
  matches a word whose part-of-speech tag starts with `V` (in English, these
  are all verbs). See [resource/en/tagset.txt](https://github.com/languagetool-org/languagetool/blob/master/languagetool-language-modules/en/src/main/resources/org/languagetool/resource/en/tagset.txt)
  for a list of possible English part-of-speech tags. Note that for tags with a
  special character like `PRP$` you need to escape the `$` with a backslash,
  as it has a special meaning in regular expressions: `PRP\$`.
* `<token>cause</token> <token regexp="yes" negate="yes">and|to</token>`  
  matches the word *cause* followed by any word that is not *and* or *to*
* `<token postag="SENT_START" /> <token>foobar</token>`  
  matches the word foobar only at the beginning of a sentence. The corresponding
  postag for the end of a sentence is `SENT_END`. 

A pattern's tokens are matched case-insensitively by default. This can be
changed for the whole `pattern` or for a single `token` by setting
`case_sensitive="yes"`.

Alternatively, case-sensitive matching can be turned on for single tokens
by using `(?-i)` in regular expressions (ex:
`<token regexp="yes">(?-i)Bill</token>` will match *Bill* but not *bill*). 

## A simple example

Here's an example of a complete rule that marks *bed English*, *bat attitude* etc as an error:

```xml
    <rule id="BED_ENGLISH" name="Possible typo 'bed/bat(bad) English/...'">
        <pattern>
          <marker>
            <token regexp="yes">bed|bat</token>
          </marker>
          <token regexp="yes">English|attitude</token>
        </pattern>
        <message>Did you mean <suggestion>bad</suggestion>?</message>
        <example correction="bad">Sorry for my <marker>bed</marker> English.</example>
    </rule>
```

## The basic elements of a rule

A short description of the elements and their attributes:

* element `rule`, attribute `id`: An internal identifier used to
  address this rule. This must be unique.
* element `rule`, attribute `name`: A short text displayed in the
  configuration, describing the rule.
* element `antipattern` (optional, may occur multiple times): A complex
  exception to the rule.
* element `pattern`, sub element `marker`: What part of the original text
  should be marked as an error. If all tokens are part of the error you
  can omit `marker`.
* element `token`, attribute `regexp`: if set to `yes`, interpret the
  given token as a regular expression
* element `message`: The text displayed to the user if this rule matches.
  Use sub-element `suggestion` to suggest a possible replacement that corrects
  the error, or use separate `suggestion` elements after the `message` element.
  * While there's no technical limit to the number of suggestions, most add-ons
    using LanguageTool limit the number of suggestions shown to 5. 
  * It is possible to conditionally suppress a suggestion if it is
    misspelled with the attribute `suppress_misspelled="yes"`. You can even
    suppress the whole rule from being matched if you use the same attribute
    in the message element.
  * Note: the tagger of the given language is used
    to make it work, so if you don't have a tagger yet, you cannot use
    this feature.
* element `url` (optional): An URL to a page that explains the rule leading
  to the error in more detail. If it contains symbol `&`, then it needs to
  be escaped as `&amp;`.
* element `short` (optional): A short description of the rule, displayed on
  the right-click menu in the GUI and in Libre/OpenOffice. It's supposed
  to be similar to `message`, but shorter.
* element `example`: At least one example with an incorrect
  sentence. The sentence with the attribute `correction="..."` is supposed
  to be matched by this rule's `pattern`. The position of the error must be
  marked up with the sub-element `marker`. Use the `correction` attribute
  to make the test check whether the correction suggested by LanguageTool
  is what you expect. These sentences are used by the automatic test cases
  that can be run using `sh testrules.sh` (on Linux), `testrules.bat`
  (on Windows), or `mvn clean test` (for Java developers).

## Testing rules

The LanguageTool user interface (`languagetool.jar`) needs to be
restarted if you have changed the `grammar.xml` file. Testing rules
is faster with our embedded test case feature: just call `sh testrules.sh en`
on Linux or `testrules.bat en` on Windows, using your language code instead of `en`.

This will test your rule with its `example` sentences: the incorrect
sentence is supposed to be detected by your rule, while the correct
sentence is not supposed to give an error. If that is not the case
you will get a message. In that case, either your rule or your
example sentences are not quite right yet.

Using `testrules.sh/bat` is not only much faster than manually
starting the user interface over and over again, it will always
test all rules, so we recommend you use that during rule development.

## Avoiding the disambiguator

If you want to match the original part-of-speech tags - those
not modified by the [disambiguator](/developing-a-disambiguator) -
use `raw_pos="yes"` on the `<pattern>`.

## Regular Expressions

Alternatively to `<pattern><token>...</token></pattern>` it's
sometimes easier to write a rule with a traditional regular expression:

```xml
    <rule ...>
      <regexp>half an our</regexp>
      <message>Did you mean <suggestion>half an hour</suggestion>?</message>
      <example>....</example>
    </rule>
```

It's important to note that this will not care about tokens,
it just searches the regular expression per sentence. So this example
would also match `behalf an our` and `half an ourselves` (which are
probably also errors, but not the ones the rule is looking for).
To avoid this, you'll need to specify boundaries with `\b`,
for example `<regexp>\bhalf an our\b</regexp>`.

Attributes:
* `case_sensitive`: Like `<token>`, `<regexp>` supports the `case_sensitive`
  attribute, and like those it is case-insensitive by default.
* `type`: Supports the values `smart` (the default) and `exact`.
  `smart` interprets spaces in a way that `<regexp>half an our</regexp>`
  will also be found if the input contains multiple spaces or other
  whitespaces between the words. If your rule does any whitespace
  checking, you'll need to use `type="exact"`.
* `mark`: Specifies which parts of the match will be underlined.
  By default, the whole match is underlined. Use `1` to underline
  only the first group of the match etc.

You cannot use `<regexp>` to look for specific POS tags. For that, you
can always use the `regexp="yes"` attribute on a `<token>` and combine
it with the `postag` or `postag_regexp` attribute.

You cannot use `<antipattern>` with `<regexp>`, but starting with
LanguageTool 5.2, you can use `RegexAntiPatternFilter` to avoid false
alarm like this:

```
<regexp mark="1">(fo.) (bar)</regexp>
<filter class="org.languagetool.rules.patterns.RegexAntiPatternFilter" args="antipatterns:fou"/>
```

This would match regexp `(fo.) (bar)`, but not `fou bar`. It’s somewhat limited,
as the regex cannot contain spaces. There can be more than one regex like this:
`args="antipatterns:regex1|regex2|regex3"` - i.e. the pipe (`|`) is used to
delimit several regex. For the antipatttern to match, it’s enough if it
partially overlaps the match of the `<regexp>`.


## Inflection

The `inflected` attribute of the `token` element is used to match not
only the given word but also all of its inflected forms. For
example `<token inflected="yes">bicycle</token>` will match
*bicycle*, *bicycles*, *bicycling* etc.

## Grouping rules

Sometimes it requires more than one rule to find all occurrences of
an error. You can put all those rules in one `rulegroup` element.
The rulegroup's `id` and `name` attribute will be used for all
the rules of that group. Overlapping matches for rules in the
same rulegroup are filtered out to avoid duplicate matches for the
same error.

## Whitespace

You cannot address spaces or whitespace directly in a `<pattern>`.
To do so, either use `<regexp>` (see "Regular Expressions" above),
or use `spacebefore="yes"` to check whether there's a space between
the previous and the current token.

## Categories

The rules are best put into categories that describe their purpose,
and allow to enable or disable a number of rules at the same time.
When creating a category, you can use the `type` attribute
to describe the type of the error according to the
[Quality Issue Type](http://www.w3.org/International/multilingualweb/lt/drafts/its20/its20.html#lqissue-typevalues)
from the W3 Internationalization Tag Set. This will make
integration of LT with other tools easier. 

## Turning rules off by default

Some rules can be optional, useful only in specific registers,
or very sensitive. You can turn them off by default by using
the attribute `default="off"`. The user can turn the rule on/off
in the Options dialog box, and this setting is being saved
in the configuration file.

## Antipatterns

Sometimes exceptions to rules require multiple tokens with complex
interrelations. In such a case, one may use an antipattern:

```xml
    <rule>
      <antipattern>
          <token>word1</token>
          <token>word2</token>
          <example>text word1 word2 more text</example>
      </antipattern>
      <pattern>
          <token>word1</token>
      </pattern>
      ...
    </rule>
```

This rule would match "This is a word1" but not "This is a word1 word2".
You can use all subelements of `pattern` in `antipattern` but `phrase`
and `or`. The text matched by the antipattern needs to overlap the
text matched by the pattern for the antipattern to become active.

The optional `example`s are used to check that the rule doesn't match for these 
example sentences. Please note it is *not* checked whether it is this specific
antipattern that prevents a match.

Antipatterns may be added to a group of rules (and then they are
valid for all rules in the group) and for particular rules in a group.

Be aware that if there is `or` in the pattern this can also lead
to problems with antipattern matching (as of LT 5.1).


## Min/Max

To match a token optionally, use the `min` attribute with a value
of `0`. For example, to match *a person* or *a nice person*:

```xml
    <token>a</token>
    <token min="0">nice</token>
    <token>person</token>
```

You can combine this with max to specify the maximum number of
occurrences possible. For example, to match *a person*, *a
nice person*, or *a nice nice person*:

```xml
    <token>a</token>
    <token min="0" max="2">nice</token>
    <token>person</token>
```

Use `max="-1"` to match an unlimited number of occurrences.

## `<or>`, `<and>`

`or` can be used to match a token if one or both of two conditions are
matched. This is sometimes a more compact alternative to writing more
than one rule. For example, this would match *t walk* (as in *don't
walk*) as well as *do not walk*:

```xml
    <or>
      <token>t</token>
      <token>not</token>
    </or>
    <token>walk</token>
```

`and` can be used to check that a token matches more than one condition.
For example, this would only match if a token has both *TAG_A* and *TAG_B*:

```xml
    <and>
      <token postag="TAG_A"/>
      <token postag="TAG_B"/>
    </and>
```

## Skip

The `skip` attribute of the token element is used in two situations:

**Simulate a simple chunker** for languages with flexible word order,
e.g., for matching errors of rection; we could for example skip possible
adverbs in some rule. `skip="1"` works exactly as two rules, i.e.

```xml
    <token skip="1">A</token>
    <token>B</token>
```

is equivalent to the pair of rules:

```xml
    <token>A</token>
    <token/>  <!-- this will match any word -->
    <token>B</token>
```

```xml
    <token>A</token>
    <token>B</token>
```

Using a negative value, we can match until the *B* is found, no
matter how many tokens are skipped. This cannot easily be encoded
using empty tokens as above because the sentence could be of any length.

**Match coordinated words**, for example to match *both ... as well as* we could write:

```xml
    <token skip="-1">both<exception scope="next">and</exception></token>
    <token>as</token>
    <token>well</token>
    <token>as</token>
```

Here the exception is applied only to the skipped tokens.

The scope attribute of the exception is used to make the exception valid only
for the token the exception is specified (`scope="current"`) or for skipped
tokens (`scope="next"`). Default behavior is `scope="current"`. Using scopes
is useful where several different exceptions should be applied to avoid false
alarms. In some cases, it's useful to use `scope="previous"` in rules that
already have `skip="-1"`. This way, you can set an exception against a single
token that immediately precedes the matched token. For example, we want to match
*tak* after *jak* which is not preceded by a comma:

```xml
    <token>tak</token>
    <token skip="-1">jak</token>
    <token>tak<exception scope="previous">,</exception></token>
```

In this case, the rule excludes all sentences, where there is a comma before *tak*.
Note that it's very hard to make such an exclusion otherwise.

## Variables

You can refer to previously matched tokens in the pattern. For example:

```xml
    <pattern>
      <token regexp="yes" skip="-1">ani|ni|i|lub|albo|czy|oraz<exception scope="next">,</exception></token>
      <token><match no="0"/></token>
    </pattern>
```

This rule matches sequences like *ani... ani*, *ni... ni*, and *i... i* (with
no comma in between) but you don't have to write all these cases explicitly.
The first match (matches are numbered from zero, so it's `<match no="0"/>`)
is automatically inserted into the second token. Note that this rule will
match sentences like:

> Nie kupiłem **ani** gruszek **ani** jabłek. Kupię to **lub** to **lub** tamto.

A similar mechanism can be used in suggestions, however there are more
features, and tokens are numbered from 1 (for compatibility with the
older notation `\1` for the first matched token). For example:

```xml
    <suggestion><match no="1"/></suggestion>
```

A more complicated example:

```xml
    <pattern>
      <token regexp="yes">^(\p{Lu}{2}+[i]*\p{Lu}+[\p{L}&amp;&amp;[^\p{Lu}]]{1,4}+)</token>
    </pattern>
    <message>Prawdopodobny błąd zapisu odmiany; skrótowce odmieniamy z dywizem: <suggestion><match
        no="1" regexp_match="^(\p{Lu}{2}+[i]*\p{Lu}+)([\p{L}&amp;&amp;[^\p{Lu}]]{1,4}+)"
        regexp_replace="$1-$2"/></suggestion>
    </message>
```

This rule matches Polish inflected acronyms such as *SMSem* that should be
written with a hyphen: *SMS-em*. So the acronym is matched with a complicated
regular expression, and the match replaces the match using Java regular
expression notation. Basically, the regular expression only shows two
parts and inserts a hyphen between them.

For some languages (currently Polish, English, Catalan, Spanish, Galician,
Dutch, Romanian, Slovak, Russian, Greek, and Ukrainian), element `match`
can be used to insert an inflected matched token (or another word with a
specified part of speech tag). For example:

```xml
    <pattern>
     <token regexp="yes">has|have</token>
     <marker>
       <token postag="VBD|VBP|VB" postag_regexp="yes">
         <exception postag="VBN|NN:U.*|JJ.*|RB" postag_regexp="yes"/>
       </token>
     </marker>
     <token><exception postag="VBG"/></token>
    </pattern>
    <message>Possible agreement error - use past participle here: <suggestion><match
        no="2" postag="VBN"/></suggestion>.
    </message>
```

The above rule takes the second verb with a POS tag `VBD`, `VBP` or `VB`
and displays its form with a POS tag `VBN` in the suggestion. You can
also specify POS tags using regular expressions (`postag_regexp="yes"`)
and replace POS tags – just like in the above example with acronyms.
This is useful for large and complicated tagsets (for many examples,
see the Polish rule file:
[rules/pl/grammar.xml](https://github.com/languagetool-org/languagetool/blob/master/languagetool-language-modules/pl/src/main/resources/org/languagetool/rules/pl/grammar.xml)).

Sometimes the rule should change the case of the matched word. For this
purpose, you can use `case_conversion` attribute values: `startlower`,
`startupper`, `allupper`, `alllower` and `firstupper`.

`startlower` converts only the first character to lowercase and leaves the rest unaltered, e.g. _EXAMPLE_ ⇾ _eXAMPLE_.  
`startupper` converts only the first character to uppercase and leaves the rest unaltered, e.g. _example_ ⇾ _Example_.  
`allupper` converts all characters to uppercase, e.g. _eXaMpLe_ ⇾ _EXAMPLE_.  
`alllower` converts all characters to lowercase, e.g. _eXaMpLe_ ⇾ _example_.  
`firstupper` converts the first character to uppercase and the remaining ones to lowercase, e.g. _eXaMpLe_ ⇾ _Example_.

Another useful thing is that `match` can refer to a token, but apply its
POS to another word. This is useful for suggesting another word with the
same part of speech. There is a special abbreviated syntax used for this purpose:

```xml
    <match no="1" postag="verb:.*perf">kierować</match>
```

This syntax means: take the POS tag of the first matched token that
matches the regular expression specified in the `postag` attribute,
and then apply this POS tag to the verb *kierować*. This way the
verb will be inflected just the way the matched verb was originally
inflected. The reason why you need to specify the POS tag is that
the matched token can have several POS tags (several readings).

Note that by default `match` element inside the `token` element
inserts only a string – so it matches a string, and not part of
speech tags. So even if it refers to a token with a POS tag,
it copies the matched token, and not its POS token. However,
you can use all above attributes to change the form of the token.

You can however use the `match` element to copy POS tags alone
but to do so, you must use the attribute `setpos="yes"`. All other
attributes can be applied so that the POS could be converted
appropriately. This can be useful for creating rules specifying
grammatical agreement.

You can use `postag_replace` to require the suggestion to have
only some of the same POS tags as the matching word. As always
with regular expressions, you put the relevant parts in parenthesis
and then refer to them using `$1`, `$2` etc:

```xml
    <match no="1"
           postag="(adj|ppas|pact):sg:inst.*(:pos)"
           postag_regexp="yes"
           postag_replace="$1:sg:.*nom.*:n1\.n2.*$2"></match>
```
It should be noted that the token matching the pattern has to match the pattern in the suggestion. If the expression is not the same a runtime exception error can occur (see more information on runtime exception errors [here](https://languagetooler.atlassian.net/wiki/spaces/DEV/pages/1959297044/XML+Rules+Possible+Error+Messages+How+to+solve).

## RuleFilter

LanguageTool supports `RuleFilter` as a way to make rules more powerful
by combining XML syntax and Java code. A `RuleFilter` takes a rule match
from an XML pattern rule and filters it, i.e. it either keeps it as it
is, modifies it, or discards it. In XML, the filter is used like this:

```xml
    <pattern>
      <token regexp="yes">\d</token>
      <token regexp="yes">\d\d</token>
    </pattern>
    <filter class="org.languagetool.rules.en.MyFilter" args="myToken:\1 otherArg:\2"/>
```

In Java code, `org.languagetool.rules.en.MyFilter` implements the `RuleFilter`
interface with its `acceptRuleMatch(...)` method. It gets the rule match and
the arguments from the `args` attribute (with `\1` etc being resolved to the
matching token at that position) as a Map. If it returns `null`, the rule
match will be discarded. If it returns a new rule match, that will be shown
to the user. It can also simply return the rule match it gets as a parameter
to accept the match.

## Repetition rules

Sometimes we want a rule to match only when a pattern has occurred several times 
in a text. To do this, we would need, in general, a Java text-level rule. But we have 
added some settings to XML pattern rules in order to do it using only pattern rules. 

These settings can be added to a rule or to a rulegroup (but not simultaneuously to a rulegroup
and to rules inside this rulegroup). If used in a rulegroup, a match of any of the rules inside 
the rulegroup will be considered a repetition. 

```xml
    <rule min_prev_matches="2" distance_tokens="80">
      <pattern>
        <token inflected="yes">test</token>
      </pattern>
      <message>Repeated word.</message>
      <example correction="">test</example>
    </rule>
```

This rule will match only when there are at least 2 previous matches within a total distance of 80 
tokens (80 tokens from first match to last match). If `distance_tokens` is not specified, the default 
value is 60 tokens for `min_prev_matches="1"`, 120 tokens for `min_prev_matches="2"`, 
180 tokens for `min_prev_matches="3"`, and so on.

# Adding new Java rules

Rules that cannot be expressed with a pattern in `grammar.xml` can be written
with Java code. As a developer, extend LanguageTool's `Rule` class and
implement the [match(AnalyzedSentence)](http://languagetool.org/development/api/org/languagetool/rules/Rule.html#match-org.languagetool.AnalyzedSentence-)
method. If your rule doesn't work on the sentence level, implement
[TextLevelRule](http://languagetool.org/development/api/org/languagetool/rules/TextLevelRule.html)
instead.

See [DemoRule.java](https://github.com/languagetool-org/languagetool/blob/master/languagetool-core/src/main/java/org/languagetool/rules/DemoRule.java)
for a simple example which you can use to develop your own rules. You will
also need to add your rule class to the
[getRelevantRules()](https://languagetool.org/development/api/org/languagetool/Language.html#getRelevantRules-java.util.ResourceBundle-)
method in `<YourLanguage>.java` to activate it. If you're using the LanguageTool
API, you can call [JLanguageTool.addRule()](https://languagetool.org/development/api/org/languagetool/JLanguageTool.html#addRule-org.languagetool.rules.Rule-)
instead.

# Translating the user interface

We use [WebTranslateIt](https://webtranslateit.com) to translate our
property files. Updated translations are only copied to the LanguageTool source before a
release, so if you need an early preview, say so on the LanguageTool forum and we'll
update the files accordingly.
