# Usage of the false friends rule

False friends can be used for checking texts in two different situations:

* in texts written in a language that is not user's mother tongue, 
  which is why the user tends to use false cognates; 
* in texts translated from another language: unexperienced, busy or 
  distracted translators can easily use false friends in translation.

## False friends as a tool for checking translations

In that case, set the "mother tongue" option to the language of the 
source text.

## Understanding false friends rules

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

## Editing

Please edit the file using xxe. There is a 
[http://languagetool.cvs.sourceforge.net/viewvc/*checkout*/languagetool/JLanguageTool/src/rules/false-friends.css 
css file] that makes your life easier.
