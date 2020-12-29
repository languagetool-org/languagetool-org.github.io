# Embedding LanguageTool in Java applications

LanguageTool requires Java 8 or later.

Get LanguageTool by adding a dependency like this to your
[Maven](http://maven.apache.org/run-maven/) `pom.xml`:

```xml
    <dependency>
      <groupId>org.languagetool</groupId>
      <artifactId>language-en</artifactId>
      <version>5.2</version>
    </dependency>
```

This will get the dependencies needed to check English. Use `language-de` as an
`artifactId` for German etc. ([show all artifacts](http://search.maven.org/#search|ga|1|languagetool)).
If you want to use all languages that LanguageTool supports, use `language-all`.

If you don't use Maven (or a similar system), download the stand-alone ZIP instead.
You will need the JAR files in the `libs` directory, the `org` directory, and the
`META-INF` directory in your classpath. We **strongly** recommend using Maven or Gradle instead.

To use LanguageTool, you just need to create a `JLanguageTool` object and use
that to check your text. Also see [the API documentation (Javadoc)](http://languagetool.org/development/api/).
For example:

```java
    JLanguageTool langTool = new JLanguageTool(new BritishEnglish());
    // comment in to use statistical ngram data:
    //langTool.activateLanguageModelRules(new File("/data/google-ngram-data"));
    List<RuleMatch> matches = langTool.check("A sentence with a error in the Hitchhiker's Guide tot he Galaxy");
    for (RuleMatch match : matches) {
      System.out.println("Potential error at characters " +
          match.getFromPos() + "-" + match.getToPos() + ": " +
          match.getMessage());
      System.out.println("Suggested correction(s): " +
          match.getSuggestedReplacements());
    }
```

Use `activateLanguageModelRules()` to make use of [Finding errors using Big Data](/finding-errors-using-n-gram-data).

## Checking Text with Markup

LanguageTool usually works on plain text. If you need to check text with markup (HTML,
XML, LaTeX, ...) you usually cannot just remove the markup, as that would mess up
the position information that LanguageTool returns. In this case, use
[AnnotatedTextBuilder](https://languagetool.org/development/api/org/languagetool/markup/AnnotatedTextBuilder.html) to create an [AnnotatedText](https://languagetool.org/development/api/org/languagetool/markup/AnnotatedText.html) object that tells LanguageTool which parts of your text are markup and which are text. There's a [check method in JLanguageTool](https://languagetool.org/development/api/org/languagetool/JLanguageTool.html#check-org.languagetool.markup.AnnotatedText-)
that accepts the `AnnotatedText` object.

## Multi-Threading

The `JLanguageTool` class is not thread safe. Create one instance of `JLanguageTool`
per thread, but create the language only once (e.g. `new BritishEnglish()`) and
use that for all instances of `JLanguageTool`. The same is true for
`MultiThreadedJLanguageTool` - its name refers to the fact that it uses
threads internally, but it's not thread safe itself.

## Spell Checking

If you want spell checking and the language you're working with has variants,
you will need to specify that variant in the `JLanguageTool` constructor, e.g.
`new AmericanEnglish()` instead of just `new English()`.

To ignore words, i.e. exclude them from spell checking, call the `addIgnoreTokens(...)`
method of the spell checking rule you're using. You first have to find the rule by
iterating over all active rules. Example:

```java
    JLanguageTool lt = new JLanguageTool(new AmericanEnglish());
    for (Rule rule : lt.getAllActiveRules()) {
      if (rule instanceof SpellingCheckRule) {
        List<String> wordsToIgnore = Arrays.asList("specialword", "myotherword");
        ((SpellingCheckRule)rule).addIgnoreTokens(wordsToIgnore);
      }
    }
```

You can also ignore phrases with the `acceptPhrases()` method:

```java
    for (Rule rule : lt.getAllActiveRules()) {
      if (rule instanceof SpellingCheckRule) {
        ((SpellingCheckRule)rule).acceptPhrases(Arrays.asList("foo bar", "producct namez"));
      }
    }
```

## Supported Languages

LanguageTool determines which languages it supports at runtime by reading
them from a file `META-INF/org/languagetool/language-module.properties`
in the classpath. The file may look like this:

    languageClasses=org.languagetool.language.Italian
    languageClasses=org.languagetool.language.Polish
    languageClasses=org.languagetool.language.English,org.languagetool.language.AmericanEnglish

You either build that file yourself, adapted to the languages you support, or you
take it from the LanguageTool stand-alone distribution. Of course, the classes
referenced in that file actually need to be in your classpath.

## Updating to a new Release

LanguageTool releases a new version every three months. While our API is generally
quite stable, we sometimes make small changes that might affect your usage of the
API. We recommend upgrading without skipping a version. So if you are using
LanguageTool 3.2 and want to upgrade to 3.5, we recommend updating to 3.3 first,
then to 3.4, then to 3.5. With each update, you should carefully look at the "API"
section in our [change log](https://github.com/languagetool-org/languagetool/blob/master/languagetool-standalone/CHANGES.md)
and re-compile the code that uses the LanguageTool API to see if there are
deprecation warnings. If there are warnings, the javadoc we provide usually
has a suggestion which method to use instead of the deprecated one.

## Using a remote LanguageTool server

If you prefer to use a [LanguageTool server](/http-server) to
running LanguageTool in-process, use `org.languagetool.remote.RemoteLanguageTool`
from the `languagetool-http-client` module. It takes care of sending the
HTTP(S) request and parsing the JSON result. For testing, feel free to
use [our public HTTPS API server](/public-http-api). The advantage
of using a remote server is lower CPU, memory, and disk usage for your
local process, especially if you'd like to use
[ngram data](/finding-errors-using-n-gram-data) for detecting commonly
confused words.
