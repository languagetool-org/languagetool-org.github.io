# LanguageTool as a Java-based spell checker

## Why?

It's easy to use LanguageTool as a spell checker in your Java projects. While you can also use
existing projects like [HunspellJNA](https://github.com/dren-dk/HunspellJNA) or
[HunspellBridJ](https://github.com/thomas-joiner/HunspellBridJ) ([at Maven Central](http://search.maven.org/#search|ga|1|a%3A%22hunspell-bridj%22)),
LanguageTool has some advantages:

1. It provides spell checking, but adding more powerful checks, including several grammar checks,
   is very easy.
2. Our goal is to provide 100% pure Java code, whereas the [Hunspell](http://hunspell.sourceforge.net/)-based
   alternatives linked above require native code. We're not there yet for all languages - if in doubt,
   run `mvn dependency:tree` on your LanguageTool-based Maven project. If the dependencies contain
   `hunspell-native-libs`, LanguageTool also uses Hunspell internally for at least one of your languages
   and thus also requires native libraries.
3. Hunspell can be slow when it comes to generating suggestions for misspelled words. For languages where
   LanguageTool does not internally use Hunspell (see item 2), it's faster in generating suggestions.
4. LanguageTool is [available at Maven Central](https://search.maven.org/search?q=languagetool) and
   comes with dictionaries included. For most languages, those dictionaries are the Hunspell
   dictionaries also used for LibreOffice/OpenOffice.

## How?

See [Java API](/java-api) for the Maven dependency you need to specify. Then, use code
like this to find *only* spelling errors in a string:

```java
    JLanguageTool langTool = new JLanguageTool(new BritishEnglish());
    for (Rule rule : langTool.getAllRules()) {
      if (!rule.isDictionaryBasedSpellingRule()) {
        langTool.disableRule(rule.getId());
      }
    }
    List<RuleMatch> matches = langTool.check("A speling error");
    for (RuleMatch match : matches) {
      System.out.println("Potential typo at characters " +
          match.getFromPos() + "-" + match.getToPos() + ": " +
          match.getMessage());
      System.out.println("Suggested correction(s): " +
          match.getSuggestedReplacements());
    }
```

If you want more than just spell checking, just remove the first `for` loop to activate
all rules. See [our development documentation](/development-overview) for how
those rules work. Need help? Just ask in [our forum](https://forum.languagetool.org).

## Extending the dictionary

[See this page](/hunspell-support)
