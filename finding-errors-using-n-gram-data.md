LanguageTool can make use of large n-gram data sets to detect errors with words that
are often confused, like *their* and *there*. The n-gram data set is huge and thus
not part of the LT download. To make use of it, you have two choices:

* Use the form on <https://languagetool.org>, which always has the latest and best ngram data.
* Set up your own server with the n-gram data or use it locally in the LanguageTool
  stand-alone version.

To use the data locally:

1. Make sure you have a fast disk, i.e. an SSD. Without an SSD, using this data can make
   LanguageTool *much* slower.
2. Download the data (8GB!) from <http://languagetool.org/download/ngram-data/> - note: data
   is currently only available for English, German, French, and Spanish (plus some data
   for untested languages).
3. Unzip it and put it in its own directory named `en`, `de`, `fr`, or `es`, depending on the
   language. The path you need to set in the next step is the directory that the `en` etc.
   directory is in, not that directory itself.
4. Then, depending on how you use LanguageTool:
   * Stand-alone user interface and LibreOffice/OpenOffice add-on: open the Options dialog
     and set the n-gram directory. For the stand-alone version you now need to restart LanguageTool.
   * Command line: start with the `@@--languagemodel@@` option pointing to the ngram-index directory.
   * Server mode: Start with the `@@--languageModel@@` option. Alternatively, you can start with
     the `@@--config file@@` option. This properties file needs to have a `languageModel=...` entry
     pointing to the ngram-index directory. Using the properties files will give you some advanced
     configurations. Call `java -jar languagetool-server.jar` to get a list of all options.
5. Test with these sentences. These are examples of errors that can only be detected using the
   n-gram rule, as of September 2020:
   * English:
     * Don't forget to put on the **breaks**.
   * German:
     * In den christlichen Traditionen gibt es unterschiedliche Anleitungen
       zur **Mediation** und Kontemplation.
 
## Background

An n-gram is a contiguous sequence of *n* items from a text, like `a girl` (2-gram) or
`a tall girl` (3-gram). Once you have a large amount of these n-grams with their number
of occurrences, you can use this to detect errors in texts. For example, in
`This is there last chance to escape.`, LanguageTool will look at the context of
*there*, considering up to three words:

`This is there`, `is there last`, `there last chance`

The probabilities of these n-grams are then compared to the probabilities of:

`This is their`, `is their last`, `their last chance`

If the probability of the n-grams with *their* is higher than of those with *there*,
LanguageTool assumes there's an error in the input sentence. This approach is language
independent, but we currently only supply the n-gram data for English and German.

We use [this data set from Google](http://storage.googleapis.com/books/ngrams/books/datasetsv2.html),
which is very similar to what Google uses for its [n-gram viewer](https://books.google.com/ngrams/).

Here you can see the confusion pairs we support so far:

* [English](https://github.com/languagetool-org/languagetool/blob/master/languagetool-language-modules/en/src/main/resources/org/languagetool/resource/en/confusion_sets.txt)
* [German](https://github.com/languagetool-org/languagetool/blob/master/languagetool-language-modules/de/src/main/resources/org/languagetool/resource/de/confusion_sets.txt)
* [French](https://github.com/languagetool-org/languagetool/blob/master/languagetool-language-modules/fr/src/main/resources/org/languagetool/resource/fr/confusion_sets.txt)
* [Spanish](https://github.com/languagetool-org/languagetool/blob/master/languagetool-language-modules/es/src/main/resources/org/languagetool/resource/es/confusion_sets.txt)

## Rule Developers

How to add words pairs is documented at [Adding n-gram data rules](/adding-n-gram-data-rules).

## Developers

Technical background information can be found on [Finding errors using Big Data](/finding-errors-using-big-data).
To use ngrams via the Java API, use `JLanguageTool.activateLanguageModelRules(File)`.
