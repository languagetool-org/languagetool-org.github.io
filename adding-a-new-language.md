# Adding a new Language

Adding support for a new language to LanguageTool requires setting up a 
new Maven project. This page will guide you through all the necessary 
steps. It was written for software developers with Java and Maven 
experience. If you aren't a software developer, but are still willing 
to write XML rules for a new language, please ask on our 
[forum](https://forum.languagetool.org/). We will help you set up the 
new language so no programming will be required by you. If you are a 
developer, fork [LanguageTool on 
github](https://github.com/languagetool-org/languagetool) and make the 
following changes in your fork. Once you've added enough error 
detection rules and we've checked your changes, you can send a pull 
request. Please note that we're only going to add a language to the 
official version of LanguageTool if we can assume that you will 
maintain that language support in the future. This means you should 
have maintained your fork for a few months before creating the pull 
request.

Note that the changes listed are just the technical changes needed so 
your language can be selected in LanguageTool. How useful support for 
the new language will be depends solely on the rules you're going to 
write. These rules decide how many errors can actually be found and how 
many false alarms the system shows. Well-supported languages have more 
than 1000 rules in LanguageTool.

1. Fork the code [at github](https://github.com/languagetool-org/languagetool)
2. Clone your forked repository
3. Switch to the `languagetool-language-modules` directory.
4. Create a new project using Maven: `mvn archetype:generate -DgroupId=org.languagetool -DartifactId=**xy** -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false` (replace xy with the [ISO 639-1 Code](http://www.loc.gov/standards/iso639-2/php/code_list.php) of your language)
5. Switch to the `**xy**` directory that has just been created.
6. Copy the [pom.xml for English](https://github.com/languagetool-org/languagetool/blob/master/languagetool-language-modules/en/pom.xml) over your pom.xml and adapt the `artifactId` and `name` elements.
7. Now you will need to create some files - just copy them over from language-en and adapt them:
 - `src/main/java/org/languagetool/language/**Mylanguage**.java` (copy from `src/main/java/org/languagetool/language/English.java`, use your language's name instead of `Mylanguage`)
 - `src/main/resources/META-INF/org/languagetool/language-module.properties` (needs to point to your **Mylanguage** class, it will be loaded by LanguageTool at runtime to detect the supported languages)
 - `src/main/resources/org/languagetool/rules/**xy**/grammar.xml` (the main rule file, see [development documentation](http://www.languagetool.org/development/#checkout))
 - This is a minimal setup so far. It's enough to write rules that refer to words, but not to part-of-speech tags. Add other Java classes like Tokenizers and Taggers, depending on what you need. For example, a trivial tagger that only assigns null tags to words is [DemoTagger](https://github.com/languagetool-org/languagetool/blob/master/languagetool-core/src/main/java/org/languagetool/tagging/xx/DemoTagger.java).
 - Add an entry `xy = Mylanguage` to `languagetool-core/src/main/resources/org/languagetool/MessagesBundle.properties`, with `xy` being your language code (e.g. `fr`) and Mylanguage being the language name (e.g. French)
8. Add your language project as a dependency in `languagetool-language-modules/all/pom.xml`.
9. Add `<module>languagetool-language-modules/**xy**</module>` to the top-level `pom.xml`
10. For your language to be accessible in LibreOffice/OpenOffice, add it to `Linguistic.xcu` in project `languagetool-office-extension`
11. To translate the LibreOffice/OpenOffice menus, adapt `languagetool-office-extension/src/main/resources/description.xml` and `languagetool-office-extension/src/main/resources/Addons.xcu`
12. Finally, run `mvn clean package` in the LanguageTool directory. The result in `languagetool-standalone/target` should now support your new language.
13. If your language has a part-of-speech tagger, add a file `resource/xy/tagset.txt` that describes the tags, like [this one for English](https://github.com/languagetool-org/languagetool/blob/master/languagetool-language-modules/en/src/main/resources/org/languagetool/resource/en/tagset.txt) does
14. To translate the user interface for your language, ask us to add your language at [Transifex](https://www.transifex.com/) (we usually only add a language at Transifex once it has some rules).

The process as described here assumes that your language will become 
part of LanguageTool. Technically you can also develop your language as 
an independent project, but we don't recommend that. For example, the 
LanguageTool API might change and not being part of LanguageTool might 
make it difficult to keep up with our changes.
