# Tips and Tricks

There are many simple but non-obvious ways how you can build 
error-checking rules. Here are some tips. As a beginner, you should 
first read <http://www.languagetool.org/development/.>  


## Misc

### Stay updated on this LanguageTool Wiki

Track changes with RSS by adding this to your RSS feeds: <http://languagetool.wikidot.com/feed/site-changes.xml>   

### Finding words in Context

For English, an easy way to get frequency data is the [Google Web 1T 
5-Gram 
Database](http://corpora.linguistik.uni-erlangen.de/demos/cgi-bin/Web1T5/Web1T5_freq.perl) 
- just search for `xyz *` if you're interested in which words follow 
`xyz`, or `* xyz` if you're interested in which words precede it. You 
can also search for `xyz * *` to get the most common two following 
words etc.

Also consider using the [Corpus of Contemporary American English 
(COCA)](http://corpus.byu.edu/coca/) to see the context of the words in 
your rule. On the COCA website, set display to KWIC and then search for 
a word. It will be displayed with its neighbour words like this:

[[image coca.png]]

Note that the COCA website will ask you to register after some queries.

## Ways to Use LanguageTool


### Tagging a corpus using LanguageTool

LanguageTool has a POS tagger, sometimes it has a disambiguator or a 
chunker for a language, so you can use it to tag a big corpus. We added 
a special command-line switch `--taggeronly` or, for short, `-t` to 
disable rule checking and use only the tagger.

We didn't go beyond 3 GB of pure text but you should experience no 
problem even with huge corpora.


    java -jar languagetool-commandline.jar -l <language> -c <encoding> -t <corpus_file> > <tagged_corpus_file>


With some smart tooling, you can use such corpus to create 
grammar-checking rules semi-automatically (but this goes beyond a 
simple trick).

### Checking text from standard input

LanguageTool will accept input text from the standard input, so you 
would be able to pipe the output from other tools to LT. To use STDIN, 
you must supply `-` as the filename.

### Automatic application of suggestions

You can switch on automatic application of suggestions by using 
`--apply` (or `-a` for short) on the command-line. Note: only the 
first suggestion is implied, when the rule has more than one 
suggestion. Make sure that you only select the rules which are reliable 
enough to allow automatic correction (use the switch `-e` to select the 
rules you want to use, or `-d` to disable some of the rules). The 
suggestions are applied in the order of the starting position of errors 
in the text. Try to make some tests to see if you have clashing or 
conflicting suggestions. LanguageTool checks if the error found by the 
rule is still there in the text before applying the suggestion, but 
that's the only quality check. The text is checked only once, so if 
multiple suggestions create an error that another rule could correct, 
you can simply run LT on the output from the previous check.

The output from LanguageTool will be the corrected text (without any 
other messages). You can still get verbose output on STDERR (by 
specifying `-v`).

This mode of operation can be used to clean a corpus before running 
some information extraction on it, or to post-edit some automatically 
created text (for example, from machine translation).

### Getting tags and rule matches


If you want to get all the matches for a given file, just run


    java -jar languagetool-commandline.jar -l <language> -c <encoding> <file>


You can pipe this to a file (add `> file` at the end of the line). To 
get part-of-speech tags as assigned by LanguageTool, simply write:


    java -jar languagetool-commandline.jar -l <language> -c <encoding> <file> >results.txt 2>tags.txt


You will get two files: `results.txt` with rule matches (and you can 
sort it using the stats.awk script mentioned below) and `tags.txt` that 
will contain all tags. Note: some operating systems allow pointing both 
std-err (2>) and std-in (>) to the same file.


## Writing Error Detection Rules


### For newcomers

Check the [development overview](/development-overview) page to learn 
basics of adding new XML rules.

### Adding rules to an external file

You can add your own rules to some external file. For that, you need to 
add a reference to that file to the `grammar.xml`. You need to add 
something like the following to the top of the file after 
`<?xml-stylesheet...>`:


```xml
    <!DOCTYPE rules [
        <!ENTITY UserRules SYSTEM "file:///path/to/user-rules.xml">
    ]>
```

If your `grammar.xml` already has a `<!DOCTYPE rules [...]>` section, 
just add `<!ENTITY UserRules SYSTEM "file:/*path/to/user-rules.xml">` 
to it.

You will also need to add this entity to the `<rules>...</rules>` part 
of the file - make sure it is inside a `<category>...</category>`, but 
not in a category that's turned off by default (`<category ... 
default="off">`):

    &UserRules;

LanguageTool will then, additionally, use the rules from 
`/path/to/user-rules.xml`. The URL doesn't have to point to a local 
file, you can use `http:*` as well. Starting with LanguageTool 2.7 it's 
possible to access password-protected rule files like this: 
`<http://user:password@example.org/path/user-rules.xml>  `. Note that 
this feature won't work if your instance of LanguageTool is running 
with a Java security manager that forbids `Authenticator.setDefault()`.

In the stand-alone GUI, to get details about an error, right-click on 
an error and click 'More'. The message includes a link 'Rule details on 
community.languagetool.org'. The link is not applicable to external 
rules. To remove the link from external rules, add `external="yes"` to 
each category.

### Test a corpus and sort rule matches

It's useful to run LanguageTool rules on a larger text and see the 
number of matches generated by the rules. To do it, run it from the 
command line (or straight from Eclipse by adding the following 
parameters)


    java -jar languagetool-commandline.jar -l <language> -c <encoding> <filename.txt> > <output_file.txt>


However, in such a case it's even more convenient to have the rule 
matches sorted by their hit frequency to see which ones should be 
corrected first. There is a 
[http://languagetool.cvs.sourceforge.net/viewvc/*checkout*/languagetool/JLanguageTool/src/dev/tools/stats.awk 
simple AWK script] you can use:


    gawk -f stats.awk <output_file.txt> > <sorted_file.txt>


You will get sorted matches, like those:


    Rule ID: HE_VERB_AGR[7], matches: 8
    
    Message: The proper name in singular (Government) must be used with a third-person verb: 'inventories'.
    Suggestion: inventories
    ...o the Federal Government inventory on and after July 1, 196...
                                ^^^^^^^^^                         
    
    Message: The proper name in singular (Man) must be used with a third-person verb: 'hoaxes'.
    Suggestion: hoaxes
    ...ator of the Piltdown Man hoax of 1912, creating the co...
                                ^^^^                         
    
    Message: The proper name in singular (Pope) must be used with a third-person verb: 'is'.
    Suggestion: is
    ...s Taggart and Betty Pope are from wealthy families. C...
                                ^^^                         
    
    Message: The proper name in singular (Earth) must be used with a third-person verb: 'orbits'.
    Suggestion: orbits
    ...M in an elliptical Earth orbit with an apogee of 4600 m...
                                ^^^^^                         
    
    Message: The proper name in singular (River) must be used with a third-person verb: 'sections'.
    Suggestion: sections
    ...t where the Cunene River section of the border with Namib...
                                ^^^^^^^                         
    
    Message: The proper name in singular (Wright) must be used with a third-person verb: 'designs'.
    Suggestion: designs
    ... that Frank Lloyd Wright design the architectural models...
                                ^^^^^^                         
    
    Message: The proper name in singular (Korea) must be used with a third-person verb: 'continues'.
    Suggestion: continues
    ...e. Japan and South Korea continue to dominate in the area ...
                                ^^^^^^^^                         
    
    Message: The proper name in singular (Set) must be used with a third-person verb: 'fires'.
    Suggestion: fires
    ...iots in Los Angeles. Set fire to the camp, and kill th...



### Various forms of negation

There are three ways to negate the meaning of the token in XML rules:

1. by negating the token
2. by negating the part of speech
3. by using exceptions

Exceptions are the easiest way and you can add many of them but adding 
a single negation via exception might be wordy. Remember, therefore, 
that even if you negate a token that has a part-of-speech specified, 
you'll be matching tokens with this part of speech.

For example, this is correct:

```xml
    <token postag="SENT_START" negate_pos="yes"/>
```

This will match all tokens that appear in the middle or at the end of 
the sentence. It is equivalent to:

```xml
    <token><exception postag="SENT_START"/></token>
```

This, however, will match any end-of-sentence token (which is probably undesired):

```xml
    <token postag="SENT_END" negate="yes"/>
```

It is logically equivalent to:

```xml
    <token postag="SENT_END"><exception/></token>
```

### Removing words

One of the most important forms of correction is a suggestion to remove 
a word. In LanguageTool, this is done implicitly in two ways:

* Replace two words with a single one, i.e., simply match any token 
  that follows or precedes the token to be deleted.
* Replace the word with an empty string as a suggestion. Note that this 
  will work flawlessly **only** for tokens that were not preceded with 
  whitespace; otherwise you will get a duplicated whitespace. So this is 
  a good way to remove, for example, a closing quotation mark that is 
  exactly at the end of the sentence and can be preceded with any word 
  (but not with whitespace, at least in many languages).

In other words, you cannot correctly remove a single word preceded by a 
whitespace if you cannot specify a preceding or following token. This 
happens only if the word to be deleted appears at the end of the 
skipped block and at the end of the sentence at the same time. We 
haven't found any evidence that a special feature for deleting the last 
token in the sentence would be important yet (nobody complained) but if 
you need the feature, it will be implemented.

### Testing if the word is preceded with a whitespace


You can test whether a token is preceded with some whitespace 
characters (spaces, new lines etc.):

```xml
    <token spacebefore="yes" regexp="yes">"|'</token>
    <token spacebefore="no">a</token>
    <token spacebefore="no" regexp="yes">"|'</token>
```

The above tokens match only "A", 'A' or "a' (but not " A ").

### Changing the case of matched word


When you want to change the case of a match, use 

```xml
    <match no="1" case_conversion ="startupper"/>
```

or

```xml
    <match no="1" case_conversion ="startlower"/>
```

`allupper` and `alllower` are supported, too.

**Note:** LT automatically adjusts the case of suggestions if they are 
added as plain text. If you want to suppress the default behavior, you 
need to use the `match` element. If you're not just changing the case 
but also changing the word, then you can use the following workaround: 
make `match` point at any token (it doesn't matter as long it's in the 
pattern) and use regular expression .* and replace it with the word you 
want to actually use. And add case_conversion:

```xml
    <pattern case_sensitive="yes">
    	<token negate="yes">foo<exception postag="SENT_START"/></token>
    	<marker>
    		<token>Bar</token>
    	</marker>
    	<token/>
    </pattern>
    <message>Change to <suggestion><match no="1" regexp_match=".*" regexp_replace="bar" case_conversion="alllower"/></suggestion></message>
```

LT automatically adjusts the case for tokens that are uppercase because 
they were at the beginning of the sentence. But there are also cases 
when you need a fairly complex process. For example, the incorrect 
Breton expression --Da Brest--  should be changed to vBrest or Vrest. 
You can do this using the following code:

```xml
    <message>
    <suggestion><match no="2" case_conversion="preserve" regexp_match="^"
     regexp_replace="v"/></suggestion> pe <suggestion><match no="2"
     regexp_match="^." regexp_replace="V"/></suggestion>?
    </message>
```

Note that the regular expression in the first `match` element adds "v" 
to the beginning of the word, and to keep this character lowercase, we 
need to use `startlower` as the value of `case_conversion`. This is 
because we are **not** preserving the case of the original token but of 
our own suggestion.

### Changing the matched word

When dealing with (partial) barbarisms e.g., one can replace strings in 
words. An example from the Dutch set:

```xml
    <match no="1" regexp_match="multiplechoice(.*)" regexp_replace="meerkeuze$1"/>
```

### Including the skipped sequence

Sometimes it's useful to include the skipped sequence in the pattern, 
for example to change both its beginning and end.

```xml
    <match no="1" include_skipped="all"/>
```

If you need to remove the beginning of the pattern, use 
`include_skipped="following"`. The default setting is 
`include_skipped="none"`, which means no inclusion of the skipped 
sequence of tokens.

### Selective case sensitivity for a rule

Generally, a rule is either case-sensitive, or not. When needed, that 
can be changed on the token leven however. In the example below, case 
sensitivity has been switched of by making it regexp and adding `(?iu)` 
to instruct Java to ignore the case sensitivity.

```xml
    <pattern case_sensitive="yes">
      <token negate="yes" skip="1" regexp="yes">(?iu)ten</token>
      <token regexp="yes">one|two</token>
    </pattern>
```

### Testing suggestions

Run tests on your grammar rules. There are three ways:

* In your IDE, by using Junit tests targets
* On the command-line: simply run `testrules.bat` (in Windows) or 
  `testrules.sh` (Linux/Unix)
* Using maven (`mvn clean test`).

For beginners, we recommend testrules.sh/bat method. It tests rules 
against examples and checks if they're valid XML. During development of 
rules, you'll find how many times there are stupid mistakes you 
wouldn't see without tests. If you want to run the tests only for your 
language, simply give a two-letter code of your language as the 
command-line parameter, and tests for other languages will be skipped:


    $ ./testrules.sh ru


The above command runs only tests for Russian.

### Testing corrections

If your suggestions are not explicit strings, but special codes or 
synthesized words, it's recommended to test them. The easiest way is to 
use the *correction* attribute of the **incorrect** example (in correct 
examples, correction is silently ignored as it makes no sense in it).

For example:

```xml
    <example correction="back and forth" type="incorrect">How to move <marker>back and fourth</marker> from linux to xmb?</example>
```

If the rule doesn't supply "back and forth" as a suggestion for this 
sentence, you will get an error during the standard rule test. Note: 
you don't supply \1 or anything like that in the correction attribute: 
you supply the string that would be generated based on the suggestion 
element you supplied in the rule.

Multiple suggestions are joined with "|":

```xml
    <example correction="back and forth|to and fro" type="incorrect">How to move <marker>back and fourth</marker>?</example>
```

### Warn for misused word, except in correct context

Some (correct) words sound alike, but have a very different meaning. 
These are often confused. It is possible to warn for confusion at every 
occurrence of such a word, but it is better to suppress the warning 
when the 'context' suggests the word is used properly.

Here is an example in Dutch, showing the words 'aanvaart' (= collides 
while sailing) and 'aanvaardt' (=accepts). Aanvaart is the most common 
mistake; the warning is suppressed however when typical 'sailing' words 
like boot (=boat) and haven (=harbour) are in the sentence.


```xml
    <rule id="ID2880" name="Aanvaart">
    	<pattern>
    		<token postag="SENT_START" skip="-1"><exception scope="next" regexp="yes">boot|haven</exception></token>
    		<marker>
    			<token skip="-1">aanvaart<exception scope="next" regexp="yes">boot|haven</exception></token>
    		</marker>
    		<token postag="SENT_END"><exception scope="current" regexp="yes">boot|haven</exception></token>
    	</pattern>
    	<message>Wanneer het botsing met boten is, is juist: <suggestion>aanvaardt</suggestion> of <suggestion>aanvaard</suggestion></message>
    	<short>aanvaardt? (ID2880)</short>
    	<example type="incorrect">Hij <marker>aanvaart</marker> de overeenkomst.</example>
    	<example type="correct">Als hij de boot toch weer <marker>aanvaart</marker>, heeft hij schade.</example>
    	<example type="correct">Als hij toch weer <marker>aanvaart</marker> tegen de boot, heeft hij schade.</example>
    </rule>
```

### Using xxe for editing rules

It's possible to edit rules using [XML Mind XML 
Editor](http://www.xmlmind.com/xmleditor/) (xxe) in a WYSYWIM (what you 
see is what you mean) mode. The program is available in a free version 
for many platforms (as it's developed in Java) though it's not open 
source. It offers spell-checking and validation, and we have developed 
[http://languagetool.cvs.sourceforge.net/viewvc/*checkout*/languagetool/JLanguageTool/src/rules/rules.css 
special CSS files] to make editing files easier. Put these in the rules 
directory.

Simply open the file in xxe, and it should display in a form input 
mode. It is recommended that you uncheck **Options** -> **Preferences** 
-> **Save** -> **Add open lines**. Set a high value in **Max line 
length**.

### Token unification

You can use [unification](/using-unification) to match several tokens 
with a similar feature.  Combined with POS tagging it can be very 
useful for word agreement, e.g. if you need to match tokens which have 
the same gender.

### Usage of part-of-speech tags

#### Testing if the POS tag is the only one

For some purposes, it's useful to have rules that react on words that 
can only take one part-of-speech tag. The way you do it is the 
following:

```xml
    <token postag="postag1">
    	<exception negate_pos="yes" postag="postag1"/>
    </token>
```

Why this works? The answer is simple, it first matches all tokens that 
have a POS tag *postag1*, and then checks if it has any other tag other 
than *postag1*, using the exception tag. The same method works for more 
POS tags -- you can check if the token has only one of two POS tags 
(and not any other):


```xml
    <token postag="tag1|tag2" postag_regexp="yes">
    	<exception negate_pos="yes" postag="tag1|tag2" postag_regexp="yes"/>
    </token>
```

To check if the token has exactly two tags, and only those, you can use 
the following notation using `and`:


```xml
    <and>
      <token postag="tag1">
    	<exception negate_pos="yes" postag="tag1|tag2" postag_regexp="yes"/>
      </token>
      <token postag="tag2"/>
    </and>
```

It's enough to set the exception on any of the tokens but they must be 
linked with `and` if both are supposed to appear. This can be useful 
for writing disambiguation rules, for example.

#### Adding words to the POS tagger

The LanguageTool part-of-speech tagger (POS tagger) uses a binary 
dictionary that comes with LanguageTool. With that, it can look up 
words, e.g. to detect that "children" is a plural noun in English. How 
to create and update that dictionary is described at [Developing a 
tagger dictionary](/developing-a-tagger-dictionary). An easier approach
is to simply add words to 
`org/languagetool/resource/XX/added.txt` (`XX` being a language code). 
Add words to that file in the form `fullform lemma pos_tag`, separated 
by tabs, for example: `children child NNS` (note than tabs are not 
displayed here in the Wiki). The words you've added will be recognized 
by LanguageTool after a restart.

#### Special POS tags

For all languages, there are special part-of-speech tags defined beside 
the tag set for the language. These are:

1. SENT_START that matches the sentence start
2. SENT_END that matches the sentence end
3. UNKNOWN that matches a token that has no part-of-speech token assigned by the tagger

#### Suggesting the word with the same POS tag

For some languages (those that have a *_synth.dict file in their 
resources directory) it's possible to inflect the words being 
suggested. However, a nice ability is to inflect the word just the way 
the matched token is inflected. Here's how it's done:


```xml
    <pattern>
    	<token>word</token>
    </pattern>
    <message>Here's another word: 
    	<suggestion>
    		<match no="1" postag="POS.*" postag_regexp="yes" >Wort</match>
    	</suggestion>
    </message>
```

Note the *postag* attribute - it allows to inflect Wort the same way 
"word" is inflected; without it, it's impossible to choose the right 
inflection pattern for tokens with multiple readings. But even if there 
is only one reading, the default logic "replace the word with saving 
its grammatical form" works only when the *postag* is selected as above 
(not necessarily as a regular expression, but it makes things easier). 
Wort must be the lemma (the base form). Note that `POS.*` above is just 
a placeholder for a real POS tag.

However, if the POS tag specified in the *postag* attribute does 
**not** match the original POS tag, the same rule will generate all 
forms that match just this `POS.*`, disregarding the original POS tag. 
So the filtering action happens only when there is a match between the 
*postag* attribute value and the POS tag of the word matched. 

Remember to test if the proposed corrections are what you meant by 
using the *correction* attribute in the example marked as *incorrect*.

#### Skipping and regular expressions over tokens

In some corpus query languages (for example, used in 
[Poliqarp](http://poliqarp.sourceforge.net/)), you can have regular 
expression queries over tokens, like this:

    [word="the"] [pos="jj"]{1,2} [pos="nn"]

Such a query would result in matching one or two words with POS tag 
equal to "jj". There is an almost equivalent notation in LanguageTool:

```xml
    <token>the</token>
    <token postag="jj" skip="1"><exception scope="next" negate_pos="yes" postag="jj|nn|SENT_END" postag_regexp="yes"/></token>
    <token postag="nn"/>
```

Note that you add negation, as exception already implies negation. This 
way, you get a positive condition (double-negation elimination). 
Another note: the POS tag is specified as a disjunction in the above 
example. This is because any token with a POS tag "nn" would be matched 
by the exception, so the end token of the pattern would never be found. 
To make sure that the negated POS exception is actually working 
properly you need to use the regular expression disjunction and 
include:

- the SENT_END POS tag (only if the last token may be the last one in the sentence)
- the POS tag of the end token of the skipping pattern ("nn" in this example)
- the POS tag you actually want to allow in the skipped sequence ("jj" in this example).

This regular expression:

    [pos="jj"]+

is equivalent to

```xml
    <token postag="jj" skip="-1"><exception negate_pos="yes" scope="next" postag="jj"/></token>
```

The only missing operator is "*". You cannot have a token that is 
matched zero times. In such a case, you have to write two instances of 
the rule: one without the asterisked token, and another with the 
notation equivalent to the "+".
