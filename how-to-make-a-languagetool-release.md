# How to make a LanguageTool Release

This is our internal documentation for how to make a new release. This 
is only relevant to release managers. Also see [Roadmap](/roadmap).

# How to enter Feature Freeze

* in `languagetool`: `wti push` and `wti pull`
* send an email to the forum about the code freeze:
  * planned release date
  * ask for [webtranslateit.com](https://webtranslateit.com) updates if needed
  * ask for fixing bugs at <https://github.com/languagetool-org/languagetool/issues?state=open>  
  * ask for updating the `CHANGES.md`
  * no new i18n strings allowed
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
* update the i18n property files by running `wti pull`
  * Daniel has the API key for webtranslateit.com
* Update the version number in:
  * top-level pom.xml: only set property `revision`
  * `mvn versions:set` (set the version number of today's release when prompted)
  * update `<version>${revision}</version>` to the new version number in all `pom.xml` files (using `${revision}`
    as a variable would cause issues when publishing the artifact via Sonatype)
  * commit changes
* `mvn clean test`
* `./build.sh languagetool-standalone package -DskipTests`
  * test the result in `languagetool-standalone/target/`
  * also test `testrules.sh` and `testrules.bat`
  * check how much bigger the ZIP has become compared to the previous release
* run `org.languagetool.dev.RuleOverview` and paste the result to `languages.html` (when running from IntelliJ IDEA, set "Working directory" to `$MODULE_WORKING_DIR$`)
* update `CHANGES.md` file
  * sort language changes alphabetically
  * make sure list of updated languages matches `languages.html`
* update `README.md` file
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
  * `./stage-artifacts.sh` (this will sign and upload the artifacts to the staging area, it will take > 30 minutes. NOTE: this requires a proper set-up of `~/.m2/settings.xml`)
  * if this doesn't work, go to <https://oss.sonatype.org/#profile;User%20Token> and create a token,
    copying the XML to `~/.m2/settings.xml` and using `sonatype-nexus-staging` as the `<id>`
* log in at <https://oss.sonatype.org>  
  * go to "Staging Repositories" page.
  * select the staging repository: `orglanguagetool-xyz` (usually at the bottom of the list)
  * click "Close"
  * test the artifacts in project `languagetool-client-example`:
    * adapt the pom.xml (set the new "orglanguagetool-xyz" as a repo and update the dependencies)
    * clean local m2 repo: `rm -r  ~/.m2/repository/org/languagetool/`
    * adapt version in `pom.xml` and `build-opensource.sh` and run it (unzips the uberjar and replaces `META-INF/org/langetool/language-module.properties`
      with the `language-module.properties` from languagetool-standalone - this is needed because with the original
      file, all languages except one get lost)
  * if okay, click "Release" (requires a refresh) - note that once released, the artifacts cannot be deleted or modified! It may take a few hours before the artifacts actually become available.
* set a tag in git: `git tag -a vX.Y -m 'version X.Y'`
* push the tag: `git push origin vX.Y`

## Releasing the ZIP and OXT for end-users

* check out the new tag from git and run `mvn clean package`
* copy the stand-alone LT to a path with a space and test some sentences
* upload to the server (see personal notes at LanguageTooler -> Release for how to access the drive):
  * `LanguageTool-6.x.zip` also as `LanguageTool-stable.zip`
  * `CHANGES.md`
  * `README.md`

## After the Release

* `git checkout vx.y-release`
* Set the new version (x.y-SNAPSHOT) in these files:
  * set `<version>x.y</version>` to `<version>${revision}</version>` in all `pom.xml` files
  * top-level pom.xml: set property `properties` -> `revision` to the new x.y-SNAPSHOT version
  * `mvn versions:set` (use `${revision}` when prompted for new version)
  * commit
* merge the branch back to trunk: `git checkout master; git merge vX.Y-release`
* update: `git pull` (not pull -r)
* push your changes: `git push`

## Update website

* update [roadmap](/roadmap)  
* enter the next feature freeze date in your personal calendar so you don't forget it
* set new version number in [Java API](/java-api)  

## Write announcements

* <http://forum.languagetool.org> - include checksums (Unix command: `sha256sum <file>`).
* Update this release documentation if there have been any changes in the process

## Update the web app at [community.languagetool.org](http://community.languagetool.org)

Just update the LT dependencies in `BuildConfig.groovy`. Deployment happens automatically.
  
## Backups and Misc

Not really related to a release, but should be done once in a while and the release is a good opportunity:

* Download forum backup at <http://forum.languagetool.org/admin/backups>  
