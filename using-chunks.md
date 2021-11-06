# Using Chunks

### Example

[LanguageTool](https://languagetool.org) has a so-called chunker for English. It 
detects chunks like noun chunks or verb chunks (also known as noun 
phrases and verb phrases). Consider, for example, this sentence:

`She put the big knives on the table.`

It contains three noun phrases, printed in bold:

**She** put **the big knives** on **the table**.

Its analysis will look like this:

> She/**B-NP-singular|E-NP-singular**    
put/B-VP  
the/**B-NP-plural**  
big/**I-NP-plural**  
knives/**E-NP-plural**  
on/B-PP  
the/**B-NP-singular**  
table/**E-NP-singular**  
./O

### Chunk Tags

A chunk tag like `B-NP-singular` is made up of two or three parts:

First part:

* `B` - marks the beginning of a chunk
* `I` - marks the continuation of a chunk
* `E` - marks the end of a chunk

As a chunk may be only one word long (like "She" in the example above), 
it can be both beginning and end of a chunk at the same time.

Second part:

* `NP` - noun chunk
* `VP` - verb chunk

Third part, only for noun chunks (this is an extension by LanguageTool 
over the original chunks):

* `singular`
* `plural`

To see how a text is chunked use the `-v` parameter of the command line 
tool.

## Chunks in Pattern Rules

Chunks can be used to match text in order to find mistakes using the 
`chunk` attribute in the `grammar.xml` file. This will match any word 
that has the `B-NP-singular` chunk tag:

`<token chunk="B-NP-singular"/>`

The matching of chunks can be combined with other ways to match text. 
The next example will match the end of a noun chunk, but only if it is 
not the word *will*:

`<token chunk="E-NP-singular"><exception>will</exception></token>`

To specify a chunk as a regular expression, use:

`<token chunk_re="[IB]-VP">`

Note that there is currently no way to negate chunks.

## Implementation

We use the chunker from [OpenNLP](http://opennlp.apache.org). As a 
chunker needs disambiguated input (e.g. it must be clear whether "walk" 
is a noun or a verb in the given context), we're also using the OpenNLP 
part-of-speech tagger (POS tagger) for chunking. Using our own POS 
tagger isn't feasible, as its results are ambiguous unless 
disambiguated by our `disambuation.xml`. The OpenNLP POS tagger has 
been trained on text tokenized with the OpenNLP tokenizer, so we also 
have to use that instead of our own (but only for chunking).

Here's an overview of the steps that we run to get chunked text:

1. Tokenizer (in `EnglishChunker.java`)
2. POS Tagger (in `EnglishChunker.java`)
3. Chunker (in `EnglishChunker.java`)
4. our own filter (in `EnglishChunkFilter.java`) - this adds singular/plural information

OpenNLP has been trained on tagged text and uses statistics to get the 
most probable result. **Thus OpenNLP will not be 100% correct all the 
time.**

