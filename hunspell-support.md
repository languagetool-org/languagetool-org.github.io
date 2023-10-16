# Improving Spell Checking

You can improve the [LanguageTool](https://languagetool.org) spell checker without touching the dictionary. Add your
words to one of these files:

* `spelling.txt`: words that the spell checker will ignore and use to generate corrections if someone types a similar word
* `ignore.txt`: words that the spell checker will ignore but not use to generate corrections
* `prohibited.txt`: words that should be considered incorrect even though the spell checker would accept them
* `spelling_global.txt`: words like proper names, brands or common acronyms (e.g. Google, Schwarz Group, BMW); works like spelling.txt (see above)Works like spelling.txt, but for all languages. Therefore, please add only words that are valid for any language.

After making changes to any of the files listed above, you need to 
restart LanguageTool for the change to become active. The rest of this 
page explains how to modify the internal spell checking dictionary.

## Hunspell

LanguageTool supports spell checking using hunspell via BridJ (thanks to 
[hunspell-java](https://gitlab.com/dumonts/hunspell-java)). 
Unfortunately, the Hunspell performance of creating suggestions is very low.

## Morfologik

Another speller, based on finite-state automata (FSA), was included 
because of the speed problems with hunspell. The dictionary for the 
speller is built in the way similar to [Developing a tagger 
dictionary](/developing-a-tagger-dictionary). All it requires is a list of valid 
words in a language. Example: for Polish, the 3.5 million word list 
becomes less than 1MB file in the FSA format.

## For Users

LanguageTool's stand-alone version comes with a tool to build a binary 
dictionary. As input, it needs a plain text list of words (one word per 
line) and the `.info` file that's already part of LanguageTool. You can 
call the tool like this to write the output to `/tmp/output.dict`:


    java -cp languagetool.jar org.languagetool.tools.SpellDictionaryBuilder de-DE /path/to/dictionary.txt org/languagetool/resource/en/hunspell/en_US.info - -o /tmp/output.dict


To export an existing dictionary as plain text you can use this command:


    java -cp languagetool.jar org.languagetool.tools.DictionaryExporter -i org/languagetool/resource/en/hunspell/en_GB.dict -info org/languagetool/resource/en/hunspell/en_GB.info -o /tmp/out.txt


For some dictionaries this prints a plain list of words, for some the 
result might look like this:


    Abe+I
    Abel+J
    Abelard+F
    Abelson+E
    Aberconwy+E


The character after the `+` indicates a frequency class. `A` marks the 
least frequent words, `Z` the most frequent ones (`Z` might not be 
used, so maybe the most frequent words are marked with `X` or so).


## For Developers

To create a morfologik dictionary under Linux, you can use 
`create_dict.sh`. It assumes you have a Hunspell dictionary (`.dic` and 
`.aff`) in ISO-8859-1 encoding. If your encoding is different, or your 
target encoding (as specified in the `.info` file) is not UTF-8, you 
need to adapt the script. As an example, call it like this for American 
English from the top-level LanguageTool directory:

    languagetool-language-modules/de/src/main/resources/org/languagetool/resource/de/hunspell/create_dict.sh en US

It assumes the `.dic` and `.aff` files of the Hunspell dictionary are 
placed in 
`languagetool-language-modules/<LANG>/src/main/resources/org/languagetool/resource/<LANG>/hunspell/`. 
It will write its resulting `.dict` into your system's temp directory.

This script will expand the Hunspell word list to a list of inflected 
forms and tries to do all the work automatically.

## Configuring the dictionary

The dictionary can be further configured using the `.info` file. Currently, the following properties are supported:

* `fsa.dict.speller.ignore-numbers` - if `true`, words containing numbers are not checked;
* `fsa.dict.speller.ignore-all-uppercase` - if set, all UPPERCASE words will be ignored during the spell-check;
* `fsa.dict.speller.ignore-camel-case` - if set, the CamelCase words will be ignored during the spell-check;
* `fsa.dict.speller.ignore-punctuation` - if set, punctuation will be ignored during the spell-check;
* `fsa.dict.speller.runon-words` - if `true`, the speller tries to chop run-on words (i.e., written `likethis`);
* `fsa.dict.speller.locale` - the name of the Locale associated with the dictionary, used for case conversions;
* `fsa.dict.speller.convert-case` - if `true`, the speller will treat upper and lower case as equivalent;
* `fsa.dict.speller.ignore-diacritics` - if `true`, the speller will treat characters with diacritics as equivalent to the ones without them (hence, "ą" will be equivalent to "a" when generating spelling suggestions; the words without diacritics will *not* be accepted as correct);
* `fsa.dict.speller.replacement-pairs` - pairs of sequences of characters that are treated as equivalent (useful for typical phonetic errors), which can be written in UTF-8, see also the example below. Note: the more replacement pairs you add, the slower finding suggestions will become;
* `fsa.dict.speller.equivalent-chars` - pairs of individual characters to be treated as equivalent, see also the example below;
* `fsa.dict.frequency-included` if set to `true`, the frequency information is included in the dictionary (see below);
* `fsa.dict.input-conversion` - conversion pairs used to replace non-standard characters before search in a speller dictionary. For example, common ligatures can be replaced here.
* `fsa.dict.output-conversion` - output conversion pairs to replace non-standard characters before search in a speller dictionary.For example, standard characters can be replaced here into ligatures.
* `fsa.dict.encoder` - use `NONE` (this replaces the obsolete `fsa.dict.uses-prefixes=false` and `fsa.dict.uses-infixes=false`)
* `fsa.dict.encoding` - encoding of the dictionary (e.g. `utf-8`);
* `fsa.dict.separator` - field separator for the dictionary (useful if you use the tagger dictionary to do the spell-check; this is however discouraged as tagging should cover more words than the speller);
* `fsa.dict.license` - license;
* `fsa.dict.author` - dictionary author;
* `fsa.dict.created` - creation date.

An example of replacement pairs (just like `REP` in hunspell):

    fsa.dict.speller.replacement-pairs=rz ż, ż rz, ch h, h ch, ę en, en ę

The speller will then suggest "brzuch" if you mistype "bżuch", or even 
"bżuh".

An example of equivalent chars (this is the same as `MAP` in hunspell):

    fsa.dict.speller.equivalent-chars=x ź, l ł, u ó, ó u

If you write "wex", the speller will be able to suggest "weź".

## Including frequency data

Sorting the suggestions by frequency of their usage is very useful to 
make our speller work in the expected way for the user.

To include the frequency data, you can run our dictionary builder like this:


    java -cp languagetool.jar org.languagetool.tools.SpellDictionaryBuilder -i dictionary.dump -info org/languagetool/resource/en/hunspell/en_US.info -freq en_us_wordlist.xml -o /tmp/output.dict


The frequency data files can be found 
[here](https://github.com/mozilla-b2g/gaia/tree/master/apps/keyboard/js/imes/latin/dictionaries). 
In addition, one needs to include the flag 
`fsa.dict.frequency-included`. The process of producing the dictionary 
dump from the current version of the binary dictionary is described on 
the page [Developing a tagger dictionary](/developing-a-tagger-dictionary).

Note that this approach is of limited value for languages with 
compounds (like German): only words listed in the dictionary will get 
their occurrence count, but other words are accepted as compounds and 
will thus not have an occurrence count.

## Current limitations of MorfologikSpeller

* It does not support compounding etc. - it requires a *finite* wordlist.
* It doesn't support regex in replacement pairs (see
  [this issue](https://github.com/morfologik/morfologik-stemming/issues/38))

There is a 
[script to convert hunspell dictionaries to finite wordlists](http://sourceforge.net/tracker/index.php?func=detail&aid=3529456&group_id=143754&atid=756397)
but it is painfully slow. Alternatively, one could convert hunspell dictionaries 
to hfst format, but we cannot (at least now) convert them to our fsa 
format.
