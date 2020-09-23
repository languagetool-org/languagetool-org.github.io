# Developing Robust Rules

In order to obtain robust, heavily tested rules there are some tips you can follow. Please feel free to improve these if you think you have a good idea. If you don't know how to develop rules yet, please see [our development documentation](http://www.languagetool.org/development/) first.

# The Easy Way

When developing rules, you should test them. The more you test them, 
the better - but the following two ways to test rules are easy and 
effective: using the rule editor on our homepage and testing a local 
Wikipedia dump.

## The Rule Editor

We offer the [rule editor on our 
homepage](http://www.languagetool.org/ruleeditor/). You can use it to 
create a simple rule, or you can switch it to "Advanced mode" to test 
an existing rule. It will run your rule against a part of Wikipedia. It 
might find real errors in Wikipedia, but its main use is to find false 
alarms that your rule would trigger. If your rule matches text which is 
actually correct, please try to make the rule more strict to avoid such 
cases as far as possible.

## Local Plain Text Checks

If you have a developer setup (Java and Maven), you can use 
`languagetool-standalone/scripts/regression-test.sh`. Change to the 
directory the script is in and call it. It will print its usage. Call 
it with the needed parameters, make changes to the LanguageTool rules 
and/or source code, and run the script again with the same parameters. 
It will print a diff of the old versus the new results. This way you 
can easily spot any newly introduced false alarms. All this requires is 
a large plain text file that you can LanguageTool to run on (for 
example from <http://tatoeba.org>  ).

## Local Wikipedia Checks


To check your rule against a larger set of Wikipedia documents, 
download the [LanguageTool-wikipedia-...-snapshot from our nightly 
builds](http://www.languagetool.org/download/snapshots/?C=M;O=D). Unzip 
it and run


    java -jar languagetool-wikipedia.jar


This will display the usage. For now, `check-data` is the interesting 
option for us. You can get its usage by calling


    java -jar languagetool-wikipedia.jar check-data


Now download a dump from <http://dumps.wikimedia.org/dewiki/latest/>   
- this is the URL for German dumps, simply replace "de" in the URL with 
the code of the language you are interested in. The file we suggest to 
use is "*de*wiki-latest-pages-articles.xml.bz2" (again, with your 
language code instead of "de"). Unpack the dump.

Now, to check a rule with ID `MY_RULE`, you can use a command similar to this:

    java -jar languagetool-wikipedia.jar check-data -l de -f dewiki-20141006-pages-articles1.xml -r MY_RULE --max-errors 100


As always, replace `de` with the language code you are working with. 
This command will check the articles from the dump against rule 
`MY_RULE` and stop after 100 matches.

Notes:
* Some Wikipedias are huge. For those, checking your rule against the whole dump might not be feasible, so feel free to set a limit for the maximum number of articles to be checked (see the command's usage).
* Text extraction from Wikipedia is not very robust yet. Thus using this approach for rules that check correct use of whitespace or special characters is probably not advisable.
* You can specify the `-f` option more than once. Typically, to complement the encyclopedic style of Wikipedia, you would specify the downloadable file from Tatoeba: <http://downloads.tatoeba.org/exports/sentences.tar.bz2.>   You need to unpack it and filter out the lines that contain the language you're interested in, e.g. using `grep -P "\teng\t" sentences.csv` to get the English sentences.

# The Long Way

While the tests described above are easy and very useful, they only 
cover Wikipedia text. It's better to test your rule against a more 
diverse set of texts. We now describe how to do that.

## Get a meaningful corpus collection

Try to obtain several texts regarding diverse subjects like Philosophy, 
Laws, Maths, Chemistry, Engineering, and so on. Find novels of a 
variety of genres, like mystery, love, science fiction, humour... It's 
advisable to gather also articles and other short text elements, in 
order to perform quick level 1 tests.

There are several sites with free books, like [*<http://librodot.com>   
LibroDot.com]. You can find sample texts in plain text files, odf or 
doc format files and in pdf format files. Working with plain text files 
is easier, over all for test automation. Therefore I recommend 
converting files to plain text.

From within LibreOffice/OpenOffice, you can perform the doc/odf/rtf 
conversion easily. For pdf files you can use 
[pdftotext](http://en.wikipedia.org/wiki/Pdftotext), a free utility 
included in many Gnu/Linux distros and also available for Windows.

## Use a separate rule file


In LanguageTool you can find your language rule file in 
`org/languagetool/rules/xx/grammar.xml`, whereas `xx` is a language 
code like *en* or *de*. This is the default rule file and it is the 
file LanguageTool will load when you run it. But it could come in handy 
if you create a separate xml file containing rules under development. 
This practice brings to you some advantages:

* You always work with a small set of rules, regardless the size of the language `grammar.xml` file.
* You can compare performances of different approaches to the same problem.
* The grammar checker performs faster.

> By now this is only supported by the stand-alone GUI 
(`languagetool.jar`). When needed, load the work file 
`rules-xx-Actual.xml` (see below) and test against your typing or short 
files.

## Create example files

Since you can feed LanguageTool text files from command, it is a good 
idea to set up some files containing example sentences. For each rule 
or rule group, create a "should warn" file and a "should not warn" 
file. Each file containing only one sentence per line. This way, you 
would obtain results like


    ...
    11.) Line 11, column 9, Rule ID: TU_VERBO_P2[1]
    Message: El pronombre personal 'tú' lleva tilde.
    Suggestion: tú
    .... Si encuntras algo, tu me lo dices. Ellos y tu tenéis mucho de que hablar. Tu primo es igua...
                                                    ^^                                             
    
    12.) Line 12, column 23, Rule ID: TU_FINAL[4]
    Message: El pronombre personal 'tú' lleva tilde.
    Suggestion: tú
    ...s mucho de que hablar. Tu primo es igual que tu. Yo lo se todo sobre ti. Tuve que aprender a...
                                                    ^^                                             
    ...


and it would be very easy to see if it worked as expected. Of course 
using the "should not warn" file the output expected would be something 
like


    Expected text language: Spanish
    Working on texts/dequeismos-bien.txt...
    Time: 1153ms for 25 sentences (21.7 sentences/sec)


Sometimes, for some simple rules it isn't worth to make two files, one 
file should be enough. Be sure to put the correct examples at the tail 
of the file to preserve the line->error number correlation.

You can use these files as an input for LanguageTool command line.

## Development model

### 1) Choose the rule you want to implement or improve

* Search the rule you are thinking on. It may be already implemented, 
  or perhaps it is a particular case of another rule that have to be 
  extended.
* Be sure the rule you choose is computable. There are some rules 
  relying on meanings, like ambiguous constructions.
* Try to avoid rules with extremely low incidence. They waste computer 
  resources and they may help only 1 out of 100.000 users.

### 2) Design the rule using a rule or rule group

Tend to make it general. If the rule is complex enough, create a separate XML file containing the new XML rules.
To do this, use the actual `grammar.xml` file as a template:


    <?xml version="1.0" encoding="UTF-8"?>
    <?xml-stylesheet type="text/xsl" href="../print.xsl" title="Pretty print" ?>
    <?xml-stylesheet type="text/css" href="../rules.css" title="Easy editing stylesheet" ?>
    <rules lang="es" xsi:noNamespaceSchemaLocation="../rules.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xs="http://www.w3.org/2001/XMLSchema">
      <!-- Here goes the stuff -->
    </rules>


and update language code. Let's use xx as a language code. I also like to move the file, so update the stylesheet paths if applicable. For example:


    <?xml version="1.0" encoding="UTF-8"?>
    <?xml-stylesheet type="text/xsl" href="rules/print.xsl" title="Pretty print" ?>
    <?xml-stylesheet type="text/css" href="rules/rules.css" title="Easy editing stylesheet" ?>
    <rules lang="xx" xsi:noNamespaceSchemaLocation="../rules.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xs="http://www.w3.org/2001/XMLSchema">
      <!-- here goes the stuff -->
    </rules>


Now save the file. In order to use it with `languagetool.jar`, use a 
name like `rules-xx-Actual.xml`. Load this rule file from the "File" 
menu. Now you can work with your small rule set.

#### Search for use cases and write them down to files

* Search for different cases matching the rule. Write them to "should warn" file
* Search for cases which may fool the rule. Write them to "should not 
  warn" file or at the end of the unique file, if it was your choice.

#### Greedy - Lazy balancing

Making a greedy rule set, testing it against the corpus collection and 
saving the results is always a great idea. Later you can compare if the 
fine tuned rule matched all the cases in such corpus.

> For example, if you want to implement the *"All to gather"* rule 
(*"All together"*) in a first stage you can make a rule with the tokens 
*All* and *to*. Perhaps, and surprisingly you can improve the rule 
because you found *"All to getter"*.

### 3) Test the rule using `languagetool.jar`.

`languagetool.jar` is a very quick way to test if your last 
modification went well. Don't forget to choose your development file! 
Step back until satisfied.

### 4) Test the rule against your short "should warn" file and a "should not warn" files.

Keep the output for future reference. You may fix some cases but break 
some others when changing rules! Return to step 2 if unsatisfied.

> You can automate this step, but keep in mind 
`languagetool-commandline.jar` does not accept the trick of the 
separate file. In order to avoid putting under risk your old 
`grammar.xml` file, you can use the sample xx language.

### 5) Integrate/replace your new rules into the regular `grammar.xml` file and run `testrules.sh` (Linux) `testrules.bat` (Windows).

This step is very important because you can find many problems with the 
examples included in your rules. You can also detect errors like 
unmarked regular expressions.

Once `testrules.sh` or `testrules.bat` is run, you're ready to test 
your rules against your corpus collection. Call the script with your 
language code as a parameter to run only the tests relevant to your 
language, e.g. `./testrules.sh en` for English tests. This is much 
faster than running all tests.

### 6) Test the rules against your level 1 corpus collection.

This is the same procedure as step 4 but using your short articles. It 
will take a little more time than the above step but not as much as the 
heavy corpus. It will raise a couple of false alarms but you'll have to 
wait less when correcting them.

You can automate this with a very simple shell script. This sample is 
based upon you have all the texts in a directory called `texts` and 
they have the same prefix `lt-` (for *long text*):


    #!/bin/bash
    # testes-tl.sh
    # Long test for Spanish grammar
    # Copyright (C) 2010 Juan Martorell
    
    _help()
    {
      echo "$0 {level}"
      echo
      echo '  where {level} is one of'
      echo '       1: Test level 1 corpus: files prefixed with lt-1-'
      echo '       2: Test level 2 corpus: files prefixed with lt-2-'
      echo '       a: Test all levels'
      exit
    }
    
    function process {
    for file in $FILELIST
    	{
    		echo "Processing $file"
    		java -jar languagetool-commandline.jar --language es \
    		--disable WHITESPACE_RULE,UNPAIRED_BRACKETS,COMMA_PARENTHESIS_WHITESPACE,DOUBLE_PUNCTUATION \
    		 $file >$file.log	
    	}
    }
    
    if [ -z "$1" ]; then
      _help
    elif [ "$1" == "1" ]; then
      FILELIST=`ls -rS texts/tl-1-*.txt`
      process
    elif [ "$1" == "2" ]; then
      FILELIST=`ls -rS texts/tl-2-*.txt`
      process
    elif [ "$1" == "a" ]; then
      FILELIST=`ls -rS texts/tl-?-*.txt`
      process
    elif [ "$1" == "--help" ]; then
      _help
    else
      echo "** $1 is not a valid option."
      echo
      _help
    fi
    exit


Notice there are some rules disabled. They are typical of PDF->TXT 
conversion and usually they don't supply value -- in fact they are 
annoying if they are not under test.

### 7) Test the rules against your level 2 corpus collection.

Once you are satisfied with the level 1 test result, it's time to test 
it against at least 1.000.000 words. It will take time, but after this 
test, you'll be reasonably sure you have false alarms under control. 

### 8) Test the regular rule file against your level 2 corpus collection.

This is the final test. Like you did in step 5, copy the rules into the 
regular `grammar.xml` file and run the tests against the level 2 corpus 
(and also level 1 if you want to be more rigorous) You can use a script 
like this:


    #!/bin/bash
    # testes-tl-all.sh
    # Complete test for Spanish grammar
    # Copyright (C) 2010 Juan Martorell
    
    function scancorpus {
    for file in `ls -rS texts/tl-*.txt`
    	{
    		echo "Processing $file"
    		java -jar languagetool-commandline.jar --language es --mothertongue en $file >$file.log		
    	}
    }
    scancorpus


Here you can do some benchmarking if you saved the results prior to the 
new rules integration. For example I have a corpus of roughly 1.7 
million words and it takes for me about 14 minutes, with an average 
speed of 100 sentences per second.

This is also a good moment to ensure there are not overlapping rules.

Anyway, the most important thing is that this way you make sure the 
whole thing works as expected.

### 9) Release the regular file

Update git or send your file to your language maintainer when you are 
sure the quality is OK.

## Conclusion

* Try to automate the developing process. It will take some time, but 
  it is time you'll save in the long way.
* This development method is optional and of course you can use it at 
  your will, any step can be adapted to your personal needs and to your 
  language particularities.

