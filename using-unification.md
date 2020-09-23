# Using Unification

Unification is used to find agreement between words. 

* This may be useful if you want, for example, to **match incorrect 
  technical terms** as translations in an inflected language that does 
  not have any other way to identify noun groups but grammatical 
  agreement (Slavonic languages have this feature). This is used in the 
  [Polish grammar rule 
  set](https://raw.githubusercontent.com/languagetool-org/languagetool/master/languagetool-language-modules/pl/src/main/resources/org/languagetool/rules/pl/grammar.xml).
* Also, it may be used to **disambiguate words with multiple 
  grammatical tags** -- by choosing the consistent interpretation in a 
  group of words, one may discard a huge number of interpretations and 
  reduce the number of false alarms.
* Another use is to **detect the failures of agreement.** This is safe 
  only with languages that have an independent mechanism of marking the 
  start of the noun groups (basically, articles). This is how unification 
  is used in French and Catalan rule sets.

## More formally...

Unification is used to match sequences of tokens that match the same 
criteria, or share some features. In the context of formal grammar, 
unification grammar stipulate that the linguistic tokens share certain 
features which are defined formally.

In LanguageTool, unification might be used to match several tokens that 
share a certain set of features, while the exact values of the features 
are unknown. This way certain rules of agreement can be defined. For 
example, if the feature to be matched is the same letter case -- all 
uppercase; all lowercase; starting from uppercase and continuing in 
lowercase etc. -- then you simply specify that all tokens must share 
this feature and only such tokens will be matched.

Unification is not limited to matching tokens in XML rules (the support 
in LanguageTool is universal enough to be used in Java rules; simply 
look at JUnit tests for some inspiration).

To make it work, you need to first define the feature. You simply need 
to give a name to it:

```xml
    <unification feature="case_sensitivity">
    ...  
    </unification>
```

Now, you need to add some possible values, or types of these features. 
To do that, you specify certain criteria of equivalence between tokens 
the same way as in rules, that is via the `token` element:

```xml
    <unification feature="case_sensitivity">
        <equivalence type="startupper">
          <token regexp="yes">\p{Lu}\p{Ll}+</token>
        </equivalence>
        <equivalence type="lowercase">
          <token regexp="yes">\p{Ll}+</token>
        </equivalence>
     </unification>
```

Here you can see two possible types of instances of the feature 
`case_sensitivity`: `startupper` and `lowercase`, both defined with a 
regular-expression token. The `unification` block must appear in the 
XML file before any rules and phrases, immediately after the root 
element `rules`.

To match tokens that share some feature, you simply write inside the 
`pattern` element:

```xml
    <unify>
    	<feature id="case_sensitivity">
    		<type id="startupper"/>
    	</feature>
        <token/>
        <token>York</token>
    </unify>
```

The pattern will match any uppercase-starting word before the word 
"York" (New York, Old York, Pork York...).

A slightly less trivial would be an example of unification over three 
features with many values. Take features such as grammatical number and 
gender: they have different values in different languages (like 
singular / plural / dual; feminine / masculine / neutral...). Inflected 
languages usually have tagsets that specify such features in POS tags. 
You can match those features using `token` element, and stipulate that 
following tokens will have the same features as the starting one:

```xml
    <unify>
    	<feature id="gender">
    		<type id="masc"/>
    	</feature>
    	<feature id="number">
    		<type id="singular"/>
    	</feature>
       <token/>
       <token>foo</token>
    </unify>
```

This pattern will match only two tokens which have the same gender and 
number (masculine and singular). You can also skip specifying the types 
- in this case, LanguageTool will try to match all possible values 
defined as equivalences for the features. Note: you cannot skip 
features.


```xml
    <unify>
       <feature id="gender"/>
       <feature id="number"/>
       <token/>
       <token>foo</token>
    </unify>
```

You can also match all tokens but the ones that share a certain set of 
features. Simply use `negate="yes"` on the `unify` element:

```xml
    <unify negate="yes">
       <feature id="gender"/>
       <feature id="number"/>
       <token/>
       <token>foo</token>
    </unify>
```

Unification is also used for disambiguation. It is integrated with the 
XML-based disambiguator. Simply specify the `action` attribute of the 
`disambig` element as `"unify"`, and only agreeing tokens will be 
selected.

## Ignoring neutral elements

Sometimes it is useful not to include some tokens in the matched 
(agreeing) sequence. Think of punctuation, adverbs that do not have any 
gender or number, weird idiomatic expressions, or connectives. To 
silently add these to the unified sequence, simply use:

```xml
    <unify>
       <feature id="gender"/>
       <feature id="number"/>
       <token>foo</token>
       <unify-ignore>
          <token>,</token>
       </unify-ignore>
       <token>foo</token>
    </unify>
```

The comma will be then added without checking whether it agrees with 
other tokens (frankly, it cannot, as commas are not inflected).
