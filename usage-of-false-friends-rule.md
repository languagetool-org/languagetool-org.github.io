# Usage of the false friends rule

A false friend is a pair of words in different languages that look or sound
similar, but differ significantly in meaning.

**This section is outdated:** The `false-friends.xml` is not used anymore to
decide whether a word is a false friend, but only to generate the error message.
See e.g. [confusion_sets_l2_de.txt](https://github.com/languagetool-org/languagetool/blob/master/languagetool-language-modules/en/src/main/resources/org/languagetool/resource/en/confusion_sets_l2_de.txt) instead.

Take an example:

```xml
    <rulegroup id="ABNEGATION">
        <rule>
          <pattern lang="en">
            <token>abnegation</token>
          </pattern>
          <translation lang="pl">poświęcenie</translation>
        </rule>
        <rule>
          <pattern lang="pl">
            <token inflected="yes">abnegacja</token>
          </pattern>
          <translation lang="en">slovenliness</translation>
          <translation lang="en">untidiness</translation>
        </rule>
      </rulegroup>
```

This rule specifies that whenever you have "abnegation" in an English 
text and "mother tongue" option set to Polish, a warning will appear 
that "abnegation" means "poświęcenie" in Polish.

If you, however, are dealing with a Polish text checked with the option 
"mother tongue" set to English, you will see a warning for every 
instance of Polish word "abnegacja"; it will tell you that actually in 
Polish, it means the same as English "slovenliness" or "untidiness".
