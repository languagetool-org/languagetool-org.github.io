# LanguageTool embedded HTTP Server

LanguageTool comes with its own embedded HTTP server, so you can send a text
to LanguageTool via HTTP and get the detected errors back as JSON. This embedded
server can be started like this.

**WARNING:** This approach is only supposed to be used by advanced users who
are familiar with using the command line. If you're not, we recommend using
the default settings, which use our Cloud servers.

### Getting the server

Download the [LanguageTool Desktop version for offline use](https://languagetool.org/download/LanguageTool-stable.zip)
(>170MB) and unzip it.

### Starting from Command Line

On the command line, go to the unzipped directory and start LanguageTool using this command:

    java -cp languagetool-server.jar org.languagetool.server.HTTPServer --port 8081 --allow-origin "*"

If this fails with an error saying that `java` cannot be found,
[install Java 8 or later](https://java.com/en/download/help/download_options.xml) first.

You can remove `--allow-origin "*"` if you do not want to use the server from the browser 
add-on. You need to set this server in the browser add-on: visit the add-on's options
by clicking the cog icon, then open "Experimental settings" and select "Local server".

**NOTE:** This will give you a basic server missing some statistics rules.
See [here](/finding-errors-using-n-gram-data) for how to set up those.

### Testing the server

You can test the server by calling this URL in your browser:

<http://localhost:8081/v2/check?language=en-US&text=my+text>

If you're not just testing, you should use HTTP POST to transfer your data. You can
test it like this, using [curl](http://curl.haxx.se/):

    curl --data "language=en-US&text=a simple test" http://localhost:8081/v2/check

You can specify a file with advanced configuration options for the LT server 
with `--config`. Use `--help` to get information about the supported settings in that file.

For security reasons, the server will not be accessible from other hosts. If 
you want to run a server for remote users, use the `--public` option. 

### Starting from stand-alone LT

Start the stand-alone application and configure it (*Text Checking -> Options... -> General*)
to listen on a port that is not used yet (the default port, 8081, should often be okay).
This way LanguageTool will also be available in server mode until you stop it. 

## HTTP Parameters and Result

**See [the JSON API](https://languagetool.org/http-api/swagger-ui/#/default).**

Note that for a server started from a GUI, a user may configure it in the configuration
dialog box to disable some unwanted rules. This may be beneficial if the calling 
program does not allow configuration of the call to the LanguageTool server, and
the user wants to enable or disable some checks. However, if the program does
disable or enable any rules, then the configuration set by the user will be silently ignored.

## Using SSL/TLS

We recommend using the HTTP server of LanguageTool and run it behind an Apache or
nginx reverse proxy with SSL/TLS support.
