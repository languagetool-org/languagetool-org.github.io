# Adding ngram Data Rules

This page describes how rule developers can make use of the n-gram data to detect errors.
As an introduction and for set-up, please see [[[Finding errors using n-gram data]]] first.

To add word pairs, simply add them to the `resource/<languageCode>/confusion_sets.txt`
file of your language. If such a file doesn't exist for your language, [ask the developers](https://languagetool.org/forum)
to add one. However, you need massive amounts of ngram data which is easily available only for English,
German, French, Spanish, Italian, Russian, Chinese, and Hebrew. Here's an example line from that file:

```
word1; word2; 10     # comment
```

In this example, {{word1}} and {{word2}} are two similar words that can easily be confused. The order of these words doesn't matter to LT, but we recommend sorting them alphabetically. {{10}} is a factor that determines how much the word used in the text is preferred. If you use {{1}} here, the other word will be suggested as a correction even when it's only a little bit more probable than the word from the text. In many cases, this would lead to false alarms. To avoid those, use a larger factor, often something from {{10}} to {{10000000}}. To decide what's a good compromise between finding many errors and not creating too many false alarms, use {{ConfusionRuleEvaluator.java}}:

### Finding a factor

Run {{ConfusionRuleEvaluator.java}} from your IDE. It will print the options you need to provide: the token pair, the language code, the ngram directory, and an evaluation file with example sentences that contain the words in their correct context. It will show output like this:

```
Factor:   10 - 5 false positives, 11 false negatives
Summary:  p=0.997, r=0.994, 2000, 3grams, 2015-09-16

Factor:   100 - 2 false positives, 22 false negatives
Summary:  p=0.999, r=0.989, 2000, 3grams, 2015-09-16

Factor:   1000 - 2 false positives, 42 false negatives
Summary:  p=0.999, r=0.979, 2000, 3grams, 2015-09-16
```

`p=` is the precision value, i.e. the probability that a correct use of a word is detected
as correct. This value needs to be close to `1`, as the difference between `1` and
the value indicates the amount of false alarms the rule will throw. `r=` is the
recall, i.e. the probability that an incorrect use of a word is detected as incorrect.
The closer this is to `1`, the better, but a high precision should be preferred.
You can now choose the factor value that has a good compromise between precision and
recall and add it as the third value for this pair of words in `confusion_sets.txt`.

There's also the chance that even a very high factor doesn't cause a good precision.
As a rule of thumb, the precision should be at least `0.995` for very common words
and `0.99` for other words. If that's not possible with any factor, or if the recall
is very low (< `0.5`), this pair of words might simply not be a good fit for ngram-based
error detection.

Of course the result of evaluation depends on what you use as input: the input itself
may contain errors. That's why `ConfusionRuleEvaluator.java` will also print the cases
that are probably false alarms. If they aren't, you should probably clean up the input.
For English, we use a combination of Wikipedia and Tatoeba as input. The fewer of the
affected words appear in Wikipedia and Tatoeba, the less meaningful the evaluation
output will become. For English, we try to use 1000 example sentences for each word.

### Known Limitations

* Currently, only errors can be detected where both words are one token. For example,
  a confusion between `your` and `you're` cannot be detected, as `you're` is
  internally split into more than one token.
* This works only for pairs - but if you have a set of three similar words, just arrange
  them into pairs: word1/word2, word1/word3, word2/word3
