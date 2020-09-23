## Translating LanguageTool

You can translate the LanguageTool user interface [online at 
webtranslateit.com](https://webtranslateit.com). The English strings 
are downloaded automatically from github 3 times a day. If a new string 
appears, the completion status of all languages will be less than 100% 
so you can see that something needs to be translated. Your changes at 
webtranslateit will not appear immediately in LanguageTool, though. You 
need a developer setup with 
[wti](https://github.com/webtranslateit/webtranslateit) to get 
translations downloaded to your code immediately (using `wti pull`). If 
you don't have one, the translations will be merged from webtranslateit 
before a release or earlier, if you ask on [our 
forum](https://forum.languagetool.org/).

As a developer, please also use webtranslateit.com and never commit to 
the property files directly (except the English one).

Note: some messages in LibreOffice/OpenOffice are supplied by 
translating two other files, 
[Addons.xcu](https://github.com/languagetool-org/languagetool/blob/master/languagetool-office-extension/src/main/resources/Addons.xcu) 
and 
[description.xml](https://github.com/languagetool-org/languagetool/blob/master/languagetool-office-extension/src/main/resources/description.xml). 
Translations for these files need to be checked in directly to git.

Whenever a new language is added, it needs to be added as a language at 
webtranslateit once (`wti addlocale xx` with xx being the language 
code).

## community.languagetool.org

[community.languagetool.org](http://community.languagetool.org) is still translated via Transifex.
s
