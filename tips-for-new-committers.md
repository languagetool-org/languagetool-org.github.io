# Tips for new Committers

Here are some tips for new [LanguageTool](https://languagetool.org) committers:

* [Subscribe to the 
  forum](https://forum.languagetool.org/t/how-to-use-this-forum-like-a-mailing-list/1067) 
  to stay up-to-date, or follow the [forum 
  feed](http://languagetool.discoursehosting.net/posts.rss).
* In your very first commit, add [this text](/developer-s-certificate-of-origin)
  to the commit message.
* Be aware that every change to the code and the rules that don't break 
  the unit tests will automatically go live on 
  [languagetool.org](https://languagetool.org) each day at roughly 22:30 
  CET. As we have more than one server, changes are deployed only to a 
  fraction of the server for about 1-2 hours. During this time, you might 
  get inconsistent results.
* You might want to subscribe to the [commit message mailing 
  list](https://lists.sourceforge.net/lists/listinfo/languagetool-commits). 
  This list receives the daily regression test emails which show the 
  effects of rule modifications.
* You might want to *watch* the LanguageTool projects at github (some 
  issues are discussed in the bug tracker and not on our forum)
* Follow the guidelines for messages: 
  <https://forum.languagetool.org/t/en-guidelines-rules-for-messages/5350>.  
* Your commits should not break anything:
  * Get the latest updates from master via `git pull -r` (rebase). This 
    will prevent the ugly merge commits you get when you just do `git 
    pull`.
  * If you have issues with git, ask for help on our forum. Never use 
    git's `--force` option.
  * For Java changes, consider the [style guide](/code-style).
  * Before committing, always run the JUnit tests (`mvn clean test` or 
    `testrules.sh xx` / `testrules.bat xx` if you've only modified the XML 
    rules, with `xx` being the language code). See [Maven tips](/maven-tips) for 
    hints about how to speed this up.
  * Before you push your changes, you will want to check them. `gitk` 
    works fine for that.
* Committing large files (e.g. binary files) will grow the git history, 
  and they can basically never be removed. So if you think you need to 
  commit large files, discuss this on the forum first.
* If you write a new piece of Java code, add a JUnit test.
* If you make non-trivial changes, document them in 
  `languagetool-standalone/CHANGES.md`.
* User interface translations are done using [webtranslateit](/translating-messages),
  so please don't modify the translated MessageBundle files directly in git.
* If your commit only affects one language, start your commit message 
  with `[xx]`, where `xx` is the language code (like `en` for English)
* If your code involves a major change to some core functionality 
  (changing XML markup of the rules, adding a new feature, changing 
  important APIs), ask on the forum before you work on it.
* Be aware of our [Roadmap](/roadmap) and the feature freezes.
