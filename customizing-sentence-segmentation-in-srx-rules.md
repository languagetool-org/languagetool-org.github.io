# Customizing sentence segmentation in SRX rules

LanguageTool supports specifying the sentence segmentation rules in 
[SRX format](https://www.gala-global.org/srx-10), thanks to 
[segment](https://github.com/loomchild/segment) Java library. The rules 
for all languages are contained in `/resource/segment.srx` file, which 
can be downloaded also directly from git 
[here](https://github.com/languagetool-org/languagetool/blob/master/languagetool-core/src/main/resources/org/languagetool/resource/segment.srx). 
The rules are cascading, i.e., there are a few universal rules; 
alternating rules for paragraph breaking (you don't need to edit them); 
and rules for specific languages.

The file can be edited using one of the available SRX editors: 
[Ratel](http://www.opentag.com/okapi/wiki/index.php?title=Ratel) (open 
source, actively developed and used in the project), and 
[SRXEditor](http://www.maxprograms.com/products/srxeditor.html) 
(proprietary but free; contains a very helpful example file, which is 
copyrighted so it can only be consulted for inspiration). You can also 
use [Pangolin](https://github.com/davidmason/Pangolin), which is a 
web-based editor using the same code as Ratel.

Please use Ratel to maintain the same file formatting for easy version 
control. Basically, there are two kinds of rules: * specifying the 
sentence break * disallowing the sentence break (specifying the 
exceptions to breaking rules).

No-break rules should precede the break rules in the file. All rules 
have to parts:

* before the break
* and after the break

Both parts have to be specified using regular expressions. The library 
we're using, `segment`, uses standard Java regular expressions which 
are slightly more expressive than what is described in SRX 
specification. To maintain portability, one shouldn't use very advanced 
features such as lookahead - they are missing in the spec.

**Note:** in SRX specification and most available rules, there is an 
obvious mistake: sentences that are not the first in the paragraph 
start with leading whitespaces, which is obviously wrong and 
unacceptable for our project. Take time to see that the `afterbreak` 
section of the rule doesn't contain any space (`\s`). In most cases, 
`afterbreak` should stay empty.

The rules are stored in a single file to make maintanance easier. 
Please note that the file covers many languages, and that rules support 
inheritance, and if you want to change Java code to support another SRX 
file, you will need to use `one` and `two` rule sets to support proper 
paragraph-breaking.
