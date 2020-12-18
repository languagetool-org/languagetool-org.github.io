# Using the AdvancedSynthesizerFilter (ASF)

The ASF is used to transfer the postags from one token to the lemma of another token. This allows for "nested matches" (putting matches inside matches within suggestions).

* This can be used, for example, in combination with **[unification](/using-unification)** to check the agreement in parallel structures.

## An easy example case

Say you want to write a rule that finds and corrects both these errors:

– He has many houses and **boat**. → boats<br>
– He has many children and **grandchild**. → grandchildren

```xml
<rule id="MANY_HOUSES_AND_BOAT" name="He has many houses and boat(s)">
  <pattern>
        <token>many</token>
        <token postag="NNP?S" postag_regexp="yes"/>
        <token>and</token>
      <marker>
        <token postag="NNP?" postag_regexp="yes"/>
      </marker>
  </pattern>
  <filter class="org.languagetool.rules.en.AdvancedSynthesizerFilter" args="lemma_from:4 lemma_select:NN.* postag_from:2 postag_select:NN.*"/>
  <message>The plural noun '\2' and the singular noun '\4' do not seem to match.</message>
  <example correction="boats">He has many houses and <marker>boat</marker>.</example>
  <example correction="grandchildren">He has many children and <marker>grandchild</marker>.</example>
</rule>
```

## Some pointers for using the ASF

As you can see the in the above example, you invoke the ASF by referencing `org.languagetool.rules.en.AdvancedSynthesizerFilter`. You can replace `en` with `de`, `fr`, `ca` or `es` if you want to use it in German, French, Catalan or Spanish.<br><br>
The arguments (`args`) of the ASF are pretty straightforward: choose the `lemma_from` token number X, take the `postag_from` token number Y (here, the first token is numbered `1`). The elements `lemma_select` and `postag_select` ensure that the token is correctly interpreted. If a token were `houses`, `lemma_select:N.*` makes sure this word is interpreted as a noun, not as the verb "to house something". The same goes for `postag_select`.<br><br>
**Keep in mind the following things:**

* The ASF replaces the `<suggestion>`
* Put the ASF **above** the `<message>` (other than you would do with the suggestion. Otherwise, it won't work)
* The ASF doesn't like `<token min="0">` — if the optional token isn't there, the `lemma_from` number will no longer be correct.

## Combining the ASF with unification

If you combine the ASF with the unification function, you can cover even more general patterns and propose a correction.

For example, you want a rule that finds and corrects both these errors:

– I sang and **dance** every day. → danced<br>
– He reads and **wrote** every day. → writes

```xml
<rule id="SANG_AND_DANCE" name="He sang and dance(d) every day">
  <pattern>
      <token postag="SENT_START"/>
      <token postag="PRP|NNP" postag_regexp="yes"/>
    <unify negate="yes">
      <feature id="person"/>
      <feature id="tense"/>
      <token postag="VB[PZD]" postag_regexp="yes"/>
    <unify-ignore>
      <token>and</token>
    </unify-ignore>
    <marker>
      <token postag="VB[PZD]" postag_regexp="yes"/>
    </marker>
    </unify>
  </pattern>
  <filter class="org.languagetool.rules.en.AdvancedSynthesizerFilter" args="lemma_from:5 lemma_select:V.* postag_from:3 postag_select:V.*"/>
  <message>The verb '\5' doesn't seem to match '\3'.</message>
  <example correction="danced">I sang and <marker>dance</marker> every day.</example>
  <example correction="writes">He reads and <marker>wrote</marker> every day.</example>
</rule>
```
**Note:**<br>
* The unification features in this example are made-up.
* You can place the `<marker>` quite freely inside and outside the `<unification>`.
