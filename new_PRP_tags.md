# Advanced tags for personal pronouns in English

Before 2021-04-20, LT only knew `PRP` (like 'I', 'me', 'myself') and `PRP$` (like 'my' and 'mine'). Now, you can distinguish more precisely and synthesize one form from another (e.g. 'I' from 'myself').

## Immediate consequences

This change has one immediate impact: If you want to negate the postag `PRP`, you'll now have to negate `PRP(_.*)?`. If you want to negate the postag `PRP\$`, you'll now have to negate `PRP\$(_.*)?`.
Example:
<br><br>**Before:**
```xml
<token postag="PRP"><exception postag="PRP" negate_pos="yes"/></token>
```
**Now:**
```xml
<token postag="PRP"><exception postag="PRP(_.*)?" postag_regexp="yes" negate_pos="yes"/>
```
<br>**Also:** If you want to write an `exception` for `PRP` (or `PRP\$`), you might have to use `PRP(_.*)?` (or `PRP\$(_.*)?`) for the exception to work.

## Konwn bug and workaround

If you use the new postags to convert, e.g. an object pronoun to a possessive pronoun, there is a conflict with the interpretation of the `$` no matter if you escape it or not.
<br><br>**These two ways of writing it will make the test fail:**
```xml
<match no="3" postag="PRP_O(.*)" postag_replace="PRP$_P$1" postag_regexp="yes"/>
```
```xml
<match no="3" postag="PRP_O(.*)" postag_replace="PRP\$_P$1" postag_regexp="yes"/>
```
**This is how you make it work:**
```xml
<match no="3" postag="PRP_O(.*)" postag_replace="PRP._P$1" postag_regexp="yes"/>
```
→ use a `.` instead of the contentious `$` sign. It's unambiguous in that position anyway.

<br>**Rule of thumb:** Inexplicable build fails can go away if you replace `\$` or `$` with `.`.

## The new PRP tags

The last 2 or 3 characters in these tags refer to person [123], number [SP], and, if applicable, gender [MFN].
E.g., 'her' is an object pronoun (`PRP_O.*`) and the last 3 characters are `3SF`, so the complete tag is `PRP_O3SF`.

### Subject pronouns (PRP_S.*)
```
I       I       PRP_S1S
you     you     PRP_S2S
you	you	PRP_S2P
he	he	PRP_S3SM
she	she	PRP_S3SF
it	it	PRP_S3SN
we	we	PRP_S1P
they	they	PRP_S3P
they	they	PRP_S3S
```

### Object pronouns (PRP_O.*)
```
me	I	PRP_O1S
you	you	PRP_O2S
him	he	PRP_O3SM
her	she	PRP_O3SF
it	it	PRP_O3SN
them	they	PRP_O3S
us	we	PRP_O1P
you	you	PRP_O2P
them	they	PRP_O3P
```

### Reflexive pronouns (PRP_R.*)
```
myself	        I	PRP_R1S
yourself        you     PRP_R2S
himself	        he	PRP_R3SM
herself	        she	PRP_R3SF
itself	        it	PRP_R3SN
themself	they	PRP_R3S
ourselves	we	PRP_R1P
yourselves	you	PRP_R2P
themselves	they	PRP_R3P
```

## The new PRP$ tags

There is a new level of distinction for `PRP$` as well. We distinguish "adjective-like" possessives (e.g. 'my') and proper "possessive pronouns" (e.g. 'mine').

### Adjective-like possessives (PRP$_A.*)
```
my	I	PRP$_A1S
your	you	PRP$_A2S
his	he	PRP$_A3SM
her	she	PRP$_A3SF
its	it	PRP$_A3SN
their	they	PRP$_A3S
our	we	PRP$_A1P
your	you	PRP$_A2P
their	they	PRP$_A3P
```

### Possessive pronouns (PRP$_P.*)

```
mine	I	PRP$_P1S
yours	you	PRP$_P2S
his	he	PRP$_P3SM
hers	she	PRP$_P3SF
theirs	they	PRP$_P3S
ours	we	PRP$_P1P
yours	you	PRP$_P2P
theirs	they	PRP$_P3P
```
