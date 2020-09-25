# How to make a LanguageTool Release

This is our internal documentation for how to make a new release. This 
is only relevant to release managers. Also see [Roadmap](/roadmap).

# How to enter Feature Freeze

* languagetool-community-website: `./i18n_update.sh`
* in `languagetool`: `wti pull`
* send an email to the forum about the code freeze:
  * planned release date
  * ask for [webtranslateit.com](https://webtranslateit.com) updates if needed
  * ask for fixing bugs at <https://github.com/languagetool-org/languagetool/issues?state=open>  
  * ask for updating the `CHANGES.md`
  * no new i18n strings allowed
* ask people to test the snapshots on twitter and in the forum
* run more tests locally to catch exceptions (locally for dnaber: `/media/Data/languagetool/regression-test/*.sh`)
* build the ZIPs and see how much bigger they have become compared to the latest release
* optionally, run `checkurl.bash` to fix old URLs

# How to make a LanguageTool release

We build artifacts with Maven and upload them to oss.sonatype.org with 
a script, where they can then be released on Maven Central. Note that 
this is independent of the *.zip and *.oxt files we release. If there's 
a problem with the Sonatype-release for Maven Central (which is only 
relevant for Java developers), we can always make the release of the 
user artifacts (`*.zip` and `*.oxt`) and care about the other problems 
later.

## Preparation

* make sure you're on Java 1.8 (we support Java 1.8 and being on Java 1.8 is the easiest way to ensure that): `java -version`
* update local source: `git pull -r`
* work in a local branch: `git checkout -b vX.Y-release`
* make sure licenses in languagetool-office-extension, languagetool-wikipedia, and languagetool-standalone are in sync (src/main/resources/third-party-licenses), except `icons.txt`:
  * `diff -r languagetool-office-extension/src/main/resources/third-party-licenses languagetool-wikipedia/src/main/resources/third-party-licenses`
  * `diff -r languagetool-office-extension/src/main/resources/third-party-licenses languagetool-standalone/src/main/resources/third-party-licenses`
* update the i18n property files by running `wti pull`
  * Daniel has the API key for webtranslateit.com
* Update the version number in:
  * `JLanguageTool.VERSION` in `JLanguageTool.java`
  * `manifest.xml`
  * `description.xml`
  * top-level pom.xml: only set property `languagetool.version`
  * all pom.xml files: `mvn versions:set`
  * commit changes
* `mvn clean test`
* `./build.sh languagetool-standalone package -DskipTests`
  * test the result in `languagetool-standalone/target/`
  * also test `testrules.sh` and `testrules.bat`
  * check how much bigger the ZIP has become compared to the previous release
* `./build.sh languagetool-wikipedia package -DskipTests`
  * test the result in languagetool-wikipedia/target
* `./build.sh languagetool-office-extension package -DskipTests`
  * test the result in languagetool-office-extension/target, rename the *zip to *oxt and install it in LibreOffice/OpenOffice, test with <https://github.com/languagetool-org/languagetool/blob/master/languagetool-office-extension/src/test/resources/manual-testing.odt?raw=true>  
* run `org.languagetool.dev.RuleOverview` and paste the result to `languages.blade.php` (when running from IntelliJ IDEA, set "Working directory" to `$MODULE_WORKING_DIR$`)
* update CHANGES.md file
  * sort language changes alphabetically
  * make sure list of updated languages matches `languages.blade.php` (but that covers 6 months, so check manually)
* update README.md file
* make sure there are no useless files (these would become part of the download file):
  * run `git status` and check the output under "untracked files"
  * make sure there are no `*.bak` files in resources

Now we're ready to create and upload the Maven artifacts. Details are 
at <http://central.sonatype.org/pages/ossrh-guide.html>:

## Releasing the artifacts to Maven Central

* `mvn clean install`
* `mvn javadoc:jar`
* `mvn source:jar`
* `cd languagetool-standalone/scripts`
* open the `stage-artifacts.sh` script
  * set the version number
  * make sure the list of Maven projects to be deployed is up-to-date
  * `./stage-artifacts.sh` (this will sign and upload the artifacts to the staging area, this will take > 30 minutes)
* log in at <https://oss.sonatype.org>  
  * go to "Staging Repositories" page.
  * select the staging repository: `orglanguagetool-xyz` (usually at the bottom of the list)
  * click "Close"
  * test the artifacts in project languagetool-client-example:
    * adapt the pom.xml (set the new "orglanguagetool-xyz" as a repo and update the dependencies)
    * clean local m2 repo
    * run `mvn clean package`
    * unzip the uberjar and replace language-module.properties in META-INF with the language-module.properties from languagetool-standalone (this is needed because with the original .properties file, all languages except one get lost)
    * re-zip the directory and run the JAR, it should check a tiny English text fragment with all languages
  * if okay, click "Release" (requires a refresh) - note that once released, the artifacts cannot be deleted or modified! It may take a few hours before the artifacts actually become available.
* set a tag in git: `git tag -a v**x.y** -m 'version **x.y**'`
* push the tag: `git push origin v**x.y**`

## Releasing the ZIP and OXT for end-users

* check out the new tag from git and run `mvn clean package`
* copy the stand-alone LT to a path with a space and test some sentences
* upload to the server:
  * scp LanguageTool-**4.x**.zip LanguageTool-**4.x**.oxt LanguageTool/CHANGES.md LanguageTool/README.md languagetool@languagetool.org:repo/public/download/

## After the Release

* `git checkout v**x.y**-release`
* Set the new version (x.y-SNAPSHOT) in these files:
  * JLanguageTool.VERSION in JLanguageTool.java
  * manifest.xml
  * description.xml
  * property `languagetool.version` in top-level pom.xml (not `version`, the next command will take care of that)
  * all pom.xml files: `mvn versions:set`
  * commit
* merge the branch back to trunk: `git checkout master; git merge v**x.y**-release`
* update: `git pull` (not pull -r)
* push your changes: `git push`

## Update website

* update `welcome.blade.php`
* link the release on the server:
  * `cd /home/languagetool/repo/public/download/`
  * `rm LanguageTool-stable.oxt && ln -s LanguageTool-**4.X**.oxt LanguageTool-stable.oxt && rm LanguageTool-stable.zip && ln -s LanguageTool-**4.X**.zip LanguageTool-stable.zip`
*  update [roadmap](/roadmap)  
* enter the next feature freeze date in your personal calendar so you don't forget it
* Javadoc
  * `git checkout v**x.y**`
  * run `mvn javadoc:aggregate`, then upload `target/site/apidocs/` to server at `/home/languagetool/repo/public/development/api` - note: this requires a local `mvn install -DskipTests`
  * `git checkout master`
* set new version number in [Java API](/java-api)  
* set new version in languagetool.update.xml (this is linked in resources/description.xml and allows updating LT from within the LO/OO extension manager)
  * install the old version of LT in LibreOffice and see if the automatic update from extension manager works

## Write announcements

* <http://forum.languagetool.org> - include checksums (Unix command: `sha256sum <file>`).
* LanguageTool Announcement list at <https://ui.newsletter2go.com>  
* Social networks via [Zoho Social](https://www.zoho.eu/social/login.html#home) (twitter, Facebook)
* <http://extensions.services.openoffice.org/project/languagetool>  
* <https://extensions.libreoffice.org/extensions/languagetool>   via <https://extensions.libreoffice.org/admin>   (Note: set the "State" to "final release")
* optional: the personal Xing account of the maintainer who makes the release
* Update this release documentation if there have been any changes in the process

## API Server Update

No action is needed, the API gets re-deployed every day with the latest 
snapshot by 
[create-snapshot.sh](https://github.com/languagetool-org/languagetool-website/blob/master/create-snapshot.sh) 
(unless there are test failures).

## Update the web app at [community.languagetool.org](http://community.languagetool.org)

Just update the LT dependencies in `BuildConfig.groovy`. Deployment 
happens automatically, the 
[create-snapshot.sh](https://github.com/languagetool-org/languagetool-website-2018/blob/master/create-snapshot.sh) 
script does this automatically every day.
  
## Backups and Misc

Not really related to a release, but should be done once in a while and the release is a good opportunity:

* Download forum backup at <http://forum.languagetool.org/admin/backups>  
* Send the spell checker words we collected from users to the maintainers of the spell dictionaries
