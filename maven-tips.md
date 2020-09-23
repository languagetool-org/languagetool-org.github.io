# Maven Tips

LanguageTool uses Maven 3.x. Here you will find some tips for building LanguageTool with Maven.

## The Basics

You can build everything with this command, called in the top-level 
directory of where you checked out LanguageTool:

    mvn clean package

The results (so-called artifacts in Maven lingo) can be found here:

* `languagetool-standalone/target/LanguageTool-x.y-SNAPSHOT.zip`: this 
  is the standalone version with the GUI and command line tool
* `languagetool-office-extension/target/LanguageTool-x.y-SNAPSHOT.zip`: 
  despite being called *.zip, this is the *.oxt for 
  LibreOffice/OpenOffice. Rename it to *.oxt and install it with the 
  LO/OO extension manager.

If you're not familiar with Maven, we suggest you always call Maven 
from the top-level directory. You *can* call it from most of the 
sub-directories, but then only that module will be built and other 
parts might be outdated.

## Performance

Maven comes with some overhead. If it's too slow for you, these tips might help:

* **If you want to run only the tests for language "xy"** and still be 
  sure that everything this language module depends on is re-built, use 
  `build.sh` (e.g. `./build.sh xy test`) or call this command: `mvn 
  --projects languagetool-language-modules/xy --also-make clean test`
* Don't use Maven that much: seriously, Maven is a build tool and most 
  development can be done without building the software. 
  * As a rule developer, use `testrules.sh` or `testrules.bat` to run 
    our automatic tests of the grammar.xml rule files. Call 
    `./testrules.sh en` to run only the English tests etc., which is much 
    faster than running all tests.
  * As a Java developer, call `mvn clean test` to run all tests (this 
    also isn't fast, but that's not so much a Maven problem). Do all 
    other work directly in your IDE, so you won't need Maven for that. 
    This assumes that you have properly imported all LanguageTool 
    projects once. All modern IDEs should be capable to do that or they 
    should have some plugin for it.
* Build but skip all unit tests: `mvn clean package -DskipTests`
* If you build often you can comment out dependencies to languages not 
  interesting to you in the top-level pom.xml and in 
  languagetool-language-modules/all/pom.xml
* Use `mvn -T 1C test` to run the tests with 1 thread per CPU core, or 
  `mvn -T 2` to use 2 threads.

## Debugging

If something doesn't work, make sure you did `mvn clean` in the 
top-level directory of LT. This way remainders of old Maven runs will 
be deleted. If there are problems with dependencies, try this command:

    mvn dependency:tree

It will show you the dependencies of the current project (i.e. the 
project whose directory you are currently in).
