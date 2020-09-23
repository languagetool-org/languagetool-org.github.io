# Developing a Tagger Dictionary

## A Shortcut

If you just want to add a few missing words and their part-of-speech 
information to LanguageTool and don't care about the technical details, 
please see "Adding words to the POS tagger" on [Tips and Tricks](/tips-and-tricks).

## Introduction

A tagger, or POS (part-of-speech) tagger is used to tag, or annotate, 
words with their respective part-of-speech information (see Wikipedia 
for [more background 
information](http://en.wikipedia.org/wiki/Part-of-speech_tagging)). 
Actually, POS tags usually convey more information, such as 
morphological information (plural / singular etc.).

Most taggers in LanguageTool are dictionary-based because statistical 
or context-oriented taggers are trained to ignore occasional grammar 
errors. For this reason, their output will be correct even if the input 
was in fact incorrect. While this is a desired behavior for most 
natural language processing applications, in grammar checking it is 
simply wrong.

We haven't however tried to train any statistical taggers on incorrect 
input to correct their output. This remains to be tested by someone who 
has enough time. However, we did test lexicon-based 
taggers/lemmatisers. For most languages, we use finite-state automata 
encoding for them. This means that the plain text files are prepared 
with a tool in the [morfologik-stemming 
library](http://sourceforge.net/projects/morfologik/). The resulting 
binary files are then used at runtime from Java code by 
morfologik-stemming library which is bundled with LanguageTool.

Preparing files helps a lot: the Polish input text file of the 
dictionary is about 190MB but as a binary file it gets squeezed into 
less than 3MB, plus the speed of the automaton tagger is really high.

## Exporting the data


Run `DictionaryExporter` with the `*.dict` file as a parameter like this:

    java -cp languagetool.jar org.languagetool.tools.DictionaryExporter -i org/languagetool/resource/en/english.dict -info org/languagetool/resource/en/english.info -o dictionary.dump

It will write the contents of the dictionary to `dictionary.dump`. The 
format is three columns separated by tabs: the inflected form of the 
word, the base form, and its POS tag. It might look like this:

    boyar	boyar	NN
    boyard	boyard	NN
    boyardism	boyardism	NN:UN
    boyards	boyard	NNS
    boyarism	boyarism	NN:UN
    boyarisms	boyarism	NNS
    boyars	boyar	NNS

(for the meaning of the English tags, see [the documentation in our git 
repository](https://github.com/languagetool-org/languagetool/blob/master/languagetool-language-modules/en/src/main/resources/org/languagetool/resource/en/tagset.txt))

## Building the binary POS dictionary

Run `POSDictionaryBuilder` with two parameters:

* A plain text dictionary with three tab-separated columns. Use the 
  same format that the `DictionaryExporter` writes: inflected form, base 
  form, POS tag. Note that the process needs an input file with UNIX line 
  endings, so if your lexicon file comes from Windows, run dos2unix on it 
  before you proceed.
* An `.info` file as described below.

For example, to re-generate a binary dictionary for English with the data that the example above has exported, call this:

    java -cp languagetool.jar org.languagetool.tools.POSDictionaryBuilder -i dictionary.dump -info org/languagetool/resource/en/english.info -o output.dict

Copy the result over the existing `.dict` file in LanguageTool to actually use it.

## Building the binary synthesizer dictionary

A synthesizer dictionary is the counterpart of the POS dictionary: 
while the POS dictionary takes an inflected form of a word and looks up 
its base form and POS tag, a synthesizer dictionary takes a base form 
and a POS tag and returns all inflected forms. Creating a synthesizer 
dictionary works similarly to building the binary POS dictionary:

    java -cp languagetool.jar org.languagetool.tools.SynthDictionaryBuilder -i dictionary.dump -info org/languagetool/resource/en/english_synth.info -o result.dict

Note that this command expects the same plain text input as the 
`POSDictionaryBuilder` - a three column format. Exporting a 
*synthesizer* dictionary, modifying it and creating a new binary 
dictionary will not work, as the format is a slightly different one.

## Building a spell checker dictionary

See [spell checking](/hunspell-support)

## The .info properties file

To make the file working in LanguageTool, you need also an `.info` file 
which needs to be placed in the same directory as the binary 
dictionary. These files already exist for all languages that 
LanguageTool supports, so you only have to create one if you build a 
binary dictionary that doesn't exist yet. The file looks like this:

    # Dictionary properties
    fsa.dict.separator=+
    fsa.dict.encoding=iso-8859-1
    fsa.dict.encoder=prefix

Please note that you can use UTF-8 as the encoding of the dictionary in 
the .info file but remember that it must match the encoding of the 
input.txt file. The possible values of the property `fsa.dict.encoder` 
are: `suffix`, `prefix`, and `infix`. The first one is the default 
value, and usually gets reasonably good results (i.e., small files) of 
the dictionary automaton.

If your dictionary contains lemmas or inflected forms with `+`, you 
need to change the separator character. FSA by default uses `+` to 
separate the inflected form from the lemma, and the lemma from the 
tags. Usually, `_` is a safe bet, as this character is rarely a part of 
real dictionary words. You need to change the line containing 
`fsa.dict.separator` like this:

    fsa.dict.separator=_

If only the tag field contains a separator character (like `+` for 
Polish), you don't have to worry. We stop processing after the second 
separator, so you might have as many separator characters as you want. 
Joining the tags using `+` can make the dictionary more compact in a 
binary form, and the tagger class `PolishTagger.java` is able to split 
the tag in a correct way.
