# Java Code Style

These are our basic style rules for new Java code in [LanguageTool](https://languagetool.org). For IntelliJ IDEA, 
you might want to use [these inspection 
settings](https://raw.githubusercontent.com/languagetool-org/languagetool/master/languagetool-dev/code-analysis/IDEA-IntelliJ-Inspections.xml).

* Indent by 2 spaces.
* Use curly braces even if there's only one statement in the `if` block.
* Do not use `final` for parameters or local variables, but write your 
  code as if there was a `final`, i.e. do not re-assign local variables. 
  Configure your IDE to help you with that.
  * For IntelliJ IDEA, the warnings (called "inspections") to activate 
    are "Reuse of local variable" and "Assignment to method parameter". 
* Use `final` for member variables if possible.
* Try to make classes immutable. That means that an object, once 
  created, cannot be changed anymore. This helps making code more robust. 
  Obviously, not everything in a complex object-oriented software can be 
  immutable.
* Do not remove public methods, but mark them deprecated and keep them 
  working. Use the `@deprecated` javadoc tag to document the method/class 
  to be used instead.
* Add a `@since x.y` javadoc annotation to all new non-private methods, 
  with `x.y` being the version number of the next release.
* Add `@Nullable` to methods that might return `null`, no matter if 
  these methods are public or not.

Examples for proper use of whitespace:

```java
    if (var == 2) {
      statements;
    }
    
    if (var != 3) {
      statements;
    } else {
      statements;
    }
    
    if (var == 1 && foo == 5) {
      statements;
    } else if (condition) {
      statements;
    } else if (condition) {
      statements;
    }
    
    try {
      statement;
    } catch (Exception e) {
      statement;
    } finally {
      statement;
    }
```
