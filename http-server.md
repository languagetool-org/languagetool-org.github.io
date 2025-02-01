# LanguageTool embedded HTTP Server

[LanguageTool](https://languagetool.org) comes with its own embedded HTTP server, so you can send a text
to a local instance of LanguageTool. This page describes how to set this up.

**WARNING:** This approach is only supposed to be used by advanced users who
are familiar with using the command line. If you're not, we recommend using
the default settings, which use our Cloud servers.

### Getting the server

Download a recent [LanguageTool snapshot](https://internal1.languagetool.org/snapshots/), 
named `LanguageTool-yyyymmdd-snapshot.zip` and unzip it.

**Please note:**
  * This will give you a basic LanguageTool server without AI-based rules. The
    AI-based rules are only available in the Cloud.
  * The [Windows](https://languagetool.org/windows) desktop app will not work with this approach.

On MacOS you can install the server using [brew](https://github.com/Homebrew/brew) (please
note that Homebrew support is not provided by the LanguageTool team):

    brew install languagetool

### Starting from Command Line

The next three steps are optional but strongly recommended on Linux and Mac for 
good language detection - on Windows, fastText is not available and LanguageTool's
language detection will not work that well:

1. [Build fastText](https://fasttext.cc/docs/en/support.html)
2. Download the [fastText language identification model](https://fasttext.cc/docs/en/language-identification.html)
3. Create a file `server.properties` with this content (change `/path/to/fasttext`
   to your fastText installation):
   ```
   fasttextModel=/path/to/fasttext/lid.176.bin
   fasttextBinary=/path/to/fasttext/fasttext
   ```

If you skip these steps, create an empty `server.properties` file instead.

On the command line, go to the unzipped LanguageTool directory and start the
LanguageTool HTTP server using these commands:

    java -cp languagetool-server.jar org.languagetool.server.HTTPServer --config server.properties --port 8081 --allow-origin

Or this command that is useful for system package management installs like
for Arch Linux:

    languagetool --http --config server.properties --port 8081 --allow-origin "*"

On MacOS you can start the server as a service in the background using [brew](https://github.com/Homebrew/brew):

    brew services start languagetool

* If this fails with an error saying that `java` cannot be found,
  [install Java 8 or later](https://java.com/en/download/help/download_options.xml) first.
* You can remove `--allow-origin` if you do not want to use the server from the browser 
  add-on.
* You now need to set this server in the browser add-on: visit the add-on's options
  by clicking the cog icon, then open "Experimental settings" and select "Local server".
  Note that Safari seems to require https, so you'd need to set up a reverse proxy and
  configure `https://localhost:8082/v2` as "Other server" ([source](https://forum.languagetool.org/t/languagetool-for-safari/5554/24?u=dnaber)).
  Note: The LanguageTool server doesn't support synonyms, so the synonym feature in the add-on
  will not work.

### Testing the server

You can test the server by calling this URL in your browser:

<http://localhost:8081/v2/check?language=en-US&text=my+text>

If you're not just testing, you should use HTTP POST to transfer your data. You can
test it like this, using [curl](http://curl.haxx.se/):

    curl -d "language=en-US" -d "text=a simple test" http://localhost:8081/v2/check

You can specify a file with advanced configuration options for the LT server 
with `--config`. Use `--help` to get information about the supported settings in that file.

For security reasons, the server will not be accessible from other hosts. If 
you want to run a server for remote users, use the `--public` option. 

## HTTP Parameters and Result

**See [the JSON API](https://languagetool.org/http-api/swagger-ui/#/default).**

Note that for a server started from a GUI, a user may configure it in the configuration
dialog box to disable some unwanted rules. This may be beneficial if the calling 
program does not allow configuration of the call to the LanguageTool server, and
the user wants to enable or disable some checks. However, if the program does
disable or enable any rules, then the configuration set by the user will be silently ignored.

## Using SSL/TLS

We recommend using the HTTP server of LanguageTool and running it behind an Apache or
nginx reverse proxy with SSL/TLS support.
