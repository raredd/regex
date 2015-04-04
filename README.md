<!-- Grab toc from .html since not copied over with keep_md: yes -->


<div class="container-fluid main-container">

<div id="header">
<h1 class="title">regex in R cheatsheet</h1>
</div>

<div id="TOC">
<ul>
<li><a href="#other-resources">Other resources</a></li>
<li><a href="#references">References</a></li>
<li><a href="#basic-regex">Basic regex</a><ul>
<li><a href="#specific-characters">Specific characters</a></li>
<li><a href="#quantifiers">Quantifiers</a></li>
<li><a href="#metacharacters">Metacharacters</a></li>
<li><a href="#character-class-shortcuts">Character class shortcuts</a></li>
</ul></li>
<li><a href="#anchors">Anchors</a><ul>
<li><a href="#look-ahead-and-look-behind">Look-ahead and look-behind</a></li>
</ul></li>
<li><a href="#additional-regex-things">Additional regex things</a><ul>
<li><a href="#metaquote-and-case-translations">Metaquote and case translations</a></li>
<li><a href="#special-variables">Special variables</a></li>
<li><a href="#modifiers">Modifiers</a></li>
<li><a href="#binding-operators">Binding operators</a></li>
</ul></li>
<li><a href="#function-for-captures">Function for captures</a></li>
<li><a href="#examples">Examples</a><ul>
<li><a href="#trimming-whitespace">Trimming whitespace</a></li>
<li><a href="#find-the-last-occurrence-of-a-string">Find the last occurrence of a string</a></li>
<li><a href="#capitalize-the-first-letter-of-each-word">Capitalize the first letter of each word</a></li>
<li><a href="#replacing-all-or-parts-of-a-string">Replacing all or parts of a string</a></li>
<li><a href="#inserting-a-character-between-two-matches">Inserting a character between two matches</a></li>
<li><a href="#matching-a-pattern-that-doesnt-include-another-pattern">Matching a pattern that doesn’t include another pattern</a></li>
<li><a href="#greed">Greed</a></li>
<li><a href="#extracting-from-between-parentheses-or-other-characters">Extracting from between parentheses (or other characters)</a></li>
<li><a href="#nesting-look-arounds-inside-of-other-look-arounds">Nesting (look-arounds inside of other look-arounds)</a></li>
<li><a href="#capitalizing-every-first-word-of-every-sentence">Capitalizing every first word of every sentence</a></li>
<li><a href="#capturing-a-word-before-or-after-a-keyword">Capturing a word before (or after) a keyword</a></li>
<li><a href="#extracting-time-formats-from-strings">Extracting time formats from strings</a></li>
<li><a href="#using-k-and-inserting-spaces-between-words">Using <code>&#92;K</code> and inserting spaces between words</a></li>
<li><a href="#removing-words-with-repeating-letters">Removing words with repeating letters</a></li>
</ul></li>
</ul>
</div>
</div>

##  Other resources 

\* __[Regex 101](https://www.regex101.com)__    
\* [Regex tester](http://regexpal.com)    
\* __[ReGeX cRoSsWoRdS!](http://regexcrossword.com)__   
\* __[Text explainer](http://rick.measham.id.au/paste/explain.pl?regex=%28%3F<%3Dfoo%29%5Cs%2Afoo%5BA-Z%5D%5Cd%7B2%2C3%7D)__   
\* [Explainer 1 (with flavors)](https://www.debuggex.com)   
\* [Explainer 2 (javascript)](http://www.regexper.com)    

## References
  
[general perl regex](www.erudil.com/preqr.pdf)     
[look-arounds](http://www.perlmonks.org/?node_id=518444)   

<div align=center><a href="http://xkcd.com/1171/"><img src="http://imgs.xkcd.com/comics/perl_problems.png" style="display: block; margin: auto;"/></a></div>

<div align=center><a href="http://xkcd.com/208/"><img src="http://imgs.xkcd.com/comics/regular_expressions.png" style="display: block; margin: auto;"/></a></div>

<div align=center><a href="http://xkcd.com/1313/"><img src="http://imgs.xkcd.com/comics/regex_golf.png" style="display: block; margin: auto;"/></a></div>

## Basic regex

### Specific characters

 character | meaning
--------|--------
`\t`    |tab
`\n`    |newline (OS neutral)
`\r`    |carriage return
`\f`    |form feed
`\cx`   |control character **C-_x_**
`\NNN`  |octal code for ASCII character _**NNN**_
`\cN`   |control character with the hex code _**N**_
`\xYY`  |character with the hex code _**NN**_
`\uYYYY`|character with the hex code _**NNNN**_

### Quantifiers

These quantifiers apply to the preceding *atom*. By default, they are "greedy" meaning they attempt to match as many characters as possible. In order to match as *few* characters as possible, follow them with a `?`.

character | meaning
--------|--------
`*`     |match **0** or more times, i.e., any number of times
`+`     |match **1** or more times, i.e., at least once
`?` (`??`)     |match (or lazy match) 0 or 1 times, i.e., at most once
`{n}`   |match *exactly* **n** times
`{n,}`  |match *at least* **n** times
`{n,m}` |match *at least* **n** times but *no more* than **m** times

### Metacharacters

The following characters need to be escaped because they have special meanings:

`\ | ( ) [ { ^ $ * + ? .`

 character | meaning
--------|--------
`.`     |match any single character (except `\n`; use `/s` to match `\n`, too)
`|`     |matches either the subexpression preceding or following it
`( )`   |groups subexpressions for capturing to $1, $2... (\\1, \\2, ... in R)
`(?: )` |groups subexpressions without capturing (cluster)
`{ }`   |specifies a range of occurrences for the element preceding it
`[ ]`   |define character class, matches any one of the characters contained within the brackets
`\`     |escapes next character, i.e., modify the meaning of the next character
`\N`    |matches the text from the N<sup>th</sup> group
`\K`    |resets the beginning of the match to the current position (this only affects what is reported as the full match)
`^`     |if the first character of a class, negates that class
`-`     |unless first, last, or escaped (i.e., `[ad-]`: match a, d, or -), used for a range (`[a-b]`: match any character in the range from a to b)

### Character class shortcuts

These can be used on their own or within a character class.

character | long version | meaning
--------|---------------|--------
`\d`    |`[0-9]`        |digit
`\D`    |`[^0-9]`       |non-digit
`\s`    |`[ \t\n\r\f]`  |whitespace
`\S`    |`[~ \t\n\r\f]` |non-whitespace
`\w`    |`[a-zA-Z0-9_]` |"word" character
`\W`    |`[^a-zA-Z0-9_]`|non-"word" character

## Anchors

Also known as *zero-width (length) assertions*, these do not match any characters but "look around" to see what comes before and/or after the current position.

character | meaning
--------|--------
`^`     |match at the *beginning* of a string (or line if `/m` is used)
`$`     |match at the *end* of a string (or line if `/m` is used)
`\b`    |match at a "word" (`\w`) boundary
`\B`    |match at *not* a "word" boundary

### Look-ahead and look-behind

Zero-length assertions can also be used to match extended patterns and require that a pattern match succeed (*positive assertion*) or fail (*negative assertion*).

character | meaning
--------|--------
`(?= )`   |positive look-ahead assertion
`(?! )`   |negative look-ahead assertion
`(?<= )`  |positive look-behind assertion
`(?!= )`  |negative look-behind assertion

Every extended pattern is written as a parenthetical group with a question mark as the first character. Notice that the `=` or `!` is always *last*. The directional indicator (`<`) is only present in the look-behind and comes *before* the positive or negative indicator.

Look-behind expressions cannot be of variable length. That means you cannot use quantifiers (?, *, +, or {1,5}) or alternation of different-length items inside them.

## Additional regex things

### Metaquote and case translations

character | meaning
--------|--------
`\Q`    |quote (de-meta) characters until `\E`
`\U`    |uppercase characters until `\E`
`\L`    |lowercase characters until `\E`
`\l`    |lowercase next character
`\u`    |titlecase<sup>&dagger;</sup> next character
`\E`    |end modification

<font size =1><sup>&dagger;</sup>Unicode concept which most often is equal to uppercase, but for certain characters (like the German "sharp s") there is a difference.</font>

### Special variables

character | meaning
--------|--------
``$` `` |characters to the left of the match
`$&`    |characters that matched
`$'`    |characters to the right of the match
`\N`    |characters captured by the N<sup>th</sup> set of parentheses (if on the match side)
`$N`    |characters captured by the N<sup>th</sup> set of parentheses (if *not* on the match side)

### Modifiers

These modifiers apply to the entire pattern (all except `/e` apply to both `m//` and `s///`)

character | meaning
--------|--------
`/i`    |ignore case
`/g`    |match globally (all)
`/m`    |let `^` and `$` match next to embedded `\n`
`/s`    |let `.` match `\n`
`/x`    |ignore most whitespace and allow comments
`/e`    |evaluate right-hand side of `s///` as an expression

### Binding operators

character | meaning
--------|--------
`=~`    |TRUE if the regex matches
`!~`    |TRUE if the regex does *not* match

## Function for captures

Common r functions like `regexpr`, `gregexpr`, or `regexec` are more focused on matching regular expressions than returning matches. Using a [helper function](https://github.com/raredd/rawr/blob/master/R/utils.R#L1352:L1420), we can extract the captures in a useful format.


```r
library('rawr')
x <- c('larry:35,M', 'alison:22,F', 'dave', 'lily:55,F')
m <- regexpr('(.*):(\\d+),([MF])', x, perl = TRUE)
regcaptures(x, m)

x <- 'ACCACCACCAC'
m <- gregexpr('(?=([AC]C))', x, perl = TRUE)
regcaptures(x, m)[[1]]
```

```
## [[1]]
##      [,1]    [,2] [,3]
## [1,] "larry" "35" "M" 
## 
## [[2]]
##      [,1]     [,2] [,3]
## [1,] "alison" "22" "F" 
## 
## [[3]]
## character(0)
## 
## [[4]]
##      [,1]   [,2] [,3]
## [1,] "lily" "55" "F" 
## 
##      [,1] [,2] [,3] [,4] [,5] [,6] [,7]
## [1,] "AC" "CC" "AC" "CC" "AC" "CC" "AC"
```

## Examples

### Trimming whitespace


```r
p1 <- '  this text string   has much white space 
and even some new 

lines    '
## leading
gsub('^\\s+', '', p1)
## trailing
gsub('\\s+$', '', p1)
## both
gsub('^\\s+|\\s+$', '', p1)
## all whitespace (including line breaks, tabs, returns)
gsub('\\s+', '', p1)
```

```
## [1] "this text string   has much white space \nand even some new \n\nlines    "
## [1] "  this text string   has much white space \nand even some new \n\nlines"
## [1] "this text string   has much white space \nand even some new \n\nlines"
## [1] "thistextstringhasmuchwhitespaceandevensomenewlines"
```

### Find the last occurrence of a string

There are actually a number of ways to get the last occurrence that don't involve look-arounds, but if you think of "the last foo" as "foo that isn't followed by a string containing foo," you can express that notion like this:


```r
p1 <- 'the last word not followed by word'
p2 <- 'the last word not followed by werd'
gsub('word(?!.*word)', 'XXX', p1, perl = TRUE)
gsub('word(?!.*word)', 'XXX', p2, perl = TRUE)
```

```
## [1] "the last word not followed by XXX"
## [1] "the last XXX not followed by werd"
```

### Capitalize the first letter of each word

We can use the combination of `regmatches` and `gregexpr` in R so see the letters this regex is matching. Then do a global sub (replace all the matches, not just the first) with the `\\U`ppercase version of the `\\1`<sup>st</sup> group.


```r
## positive look-behind assertion for a word boundary
## then match letters and replace with the uppercase version
p1 <- 'Look for A Lower-case Letter proceded by a Word Boundary'
regmatches(p1, gregexpr('(?<=\\b)([a-z])', p1, perl = TRUE))[[1]]
gsub('(?<=\\b)([a-z])', '\\U\\1', p1, perl = TRUE)
```

```
## [1] "f" "c" "p" "b" "a"
## [1] "Look For A Lower-Case Letter Proceded By A Word Boundary"
```

### Replacing all or parts of a string

Many substitutions match a chunk of text and then replace part or all of it. You can often avoid that by using look-arounds:


```r
p1 <- 'this is a wordy word and ends on a word'
gsub('(?<=word)', ',', p1, perl = TRUE)     ## find every pattern match
gsub('(?<=word\\b)', ',', p1, perl = TRUE)  ## only patterns on a word boundary
gsub('(?<=word$)', ',', p1, perl = TRUE)    ## only patters on a string boundary
```

```
## [1] "this is a word,y word, and ends on a word,"
## [1] "this is a wordy word, and ends on a word,"
## [1] "this is a wordy word and ends on a word,"
```

### Inserting a character between two matches

Sometimes we want to insert after a specific pattern but only if it is followed by another specific pattern.


```r
p1 <- 'this lookahead needs a hyphen, but this look ahead does not'
gsub('(?<=look)(?=ahead)', '-', p1, perl = TRUE)
```

```
## [1] "this look-ahead needs a hyphen, but this look ahead does not"
```

The next defines two look-around groups. The first uses a look-behind (`(?<= )`) and defines a character class of non-capital letter characters followed by `(`; the second group defines (capital) letter characters followed by `(` but in a look-*ahead* (`(?= )`).


```r
p1 <- 'AbcDef(123)'
gsub('(?<=[^A-Z(])(?=[A-Z(])', ' ', p1, perl = TRUE)
```

```
## [1] "Abc Def (123)"
```

### Matching a pattern that doesn't include another pattern

You might want to capture everything between foo and bar that doesn't include baz. The technique is to have the regex engine look-ahead at every character to ensure that it isn't the beginning of the undesired pattern:

```
foo  # Match starting at foo
 (         # Capture
 (?:       # Complex expression:
   (?!baz) #   make sure we're not at the beginning of baz 
   .       #   accept any character
 )*        # any number of times
 )         # End capture
 bar  # and ending at bar
```
 

```r
## this example is greedy
p1 <- 'match when you love me but not when you do not love me :('
regmatches(p1, gregexpr('you((?:(?!not).)*)me', p1, perl = TRUE))[[1]]
```

```
## [1] "you love me"
```

The next regular expression uses a negative look-ahead (`(?! )`) to see if there is no character except a line break and no substring orange (`.*orange`), and if so then the dot will match any character except a line break as it is wrapped in a group (`).)`) and repeated (0 or more times, `*`). Next we look for apple and any character except a line break (`apple.*`) 0 or more times. Finally, the start and end of line anchors  (`^ $`) are in place to make sure the input is consumed.


```r
p1 <- c('I like apples', 'I really like apples', 
        'I like apples and oranges', 'I like oranges and apples',
        'I really like oranges and apples but oranges more')

p1[grepl('^((?!.*orange).)*apple.*$', p1, perl = TRUE)]
```

```
## [1] "I like apples"        "I really like apples"
```

broken down:

```
^               # the beginning of the string
(               # group and capture to \1 (0 or more times)
  (?!             # look ahead to see if there is not:
   .*             # any character except \n (0 or more times)
   orange         # 'orange'
  )               # end of look-ahead
 .               # any character except \n
)*              # end of \1 
 apple          # 'apple'
.*              # any character except \n (0 or more times)
$               # before an optional \n, and the end of the string
```

### Greed

Placing `?` after quantifiers determine if we want to match until the last occurrence of the look-ahead or until the first occurrence.


```r
p1 <- 'match from a to z and after z if there is a z that follows but at not the last z because I said so'
## match a ... z but not a ... z which contains a not
regmatches(p1, gregexpr(' a\\s?((?:(?! not ).)*) z ', p1, perl = TRUE))[[1]]
regmatches(p1, gregexpr(' a(.*)z ', p1, perl = TRUE))[[1]]
## insert the ? to get each match
regmatches(p1, gregexpr(' a\\s?((?:(?! not ).)*?) z ', p1, perl = TRUE))[[1]]
regmatches(p1, gregexpr(' a(.*?)z ', p1, perl = TRUE))[[1]]
```

```
## [1] " a to z and after z if there is a z "
## [1] " a to z and after z if there is a z that follows but at not the last z "
## [1] " a to z "  " after z " " a z "    
## [1] " a to z "            " after z "           " a z "              
## [4] " at not the last z "
```

### Extracting from between parentheses (or other characters)


```r
p1 <- 'extract (everything) between parentheses (if any)'
## greedily
regmatches(p1, gregexpr('(?=\\().*(?<=\\))', p1, perl = TRUE))[[1]]
## not
regmatches(p1, gregexpr('(?<=\\().*?(?=\\))', p1, perl = TRUE))[[1]]
regmatches(p1, gregexpr('(?=\\().*?(?<=\\))', p1, perl = TRUE))[[1]]
```

```
## [1] "(everything) between parentheses (if any)"
## [1] "everything" "if any"    
## [1] "(everything)" "(if any)"
```

### Nesting (look-arounds inside of other look-arounds)

A look-around sub-expression inherits a starting position from the enclosing expression and can walk all around relative to that position without affecting the position of the enclosing expression. They all have independent (though initially inherited) bookkeeping for where they are in the string.

The concept is pretty simple, but the notation becomes hairy very quickly. Let's look at the real example to add space after punctuation sign. We want a space after any comma (or any punctuation by using `[,.: etc]`) that is not nestled between two digits (since this rule should not apply to numbers, presumably):

```
 (?<=,         # after a comma,
    (?!        # but not matching
      (?<=\d,) #   digit-comma before, AND
      (?=\d)   #   digit afterward
    )
  )

using De Morgan's law, !(A & B) <=> !A | !B, this is equivalent to

  (?<=,        # after a comma
    (?:        #  but either
      (?<!\d,) #   not matching digit-comma before
      |        #   OR
      (?!\d)   #   not matching digit afterward
    )
  )
```


```r
p1 <- 'this is an unformatted,ugly sentence.no spaces one,two,many 1,000.0 commas'
gsub('(?<=,(?!(?<=\\d,)(?=\\d)))', ' ', p1, perl = TRUE)
gsub('(?<=[,.](?!(?<=\\d[,.])(?=\\d)))', ' ', p1, perl = TRUE)
```

```
## [1] "this is an unformatted, ugly sentence.no spaces one, two, many 1,000.0 commas"
## [1] "this is an unformatted, ugly sentence. no spaces one, two, many 1,000.0 commas"
```

### Capitalizing every first word of every sentence


```r
txt <- "this is just a test! i'm not sure if this is o.k. or if it will work? who knows? regex is sorta new to me... There are certain cases that I may not figure out. sad!"

gsub("([^.!?\\s])([^.!?]*(?:[.!?](?!['\"]?\\s|$)[^.!?]*)*[.!?]?['\"]?)(?=\\s|$)", "\\U\\1\\E\\2", txt, perl = TRUE)
```

```
## [1] "This is just a test! I'm not sure if this is o.k. Or if it will work? Who knows? Regex is sorta new to me... There are certain cases that I may not figure out. Sad!"
```

Explanation: 

```
[^.!?\\s]         # match sentence ending in punctuation (. or !) or end of string
[^.!?]*           # first char is non-punct, non-whitespace
(?:               # greedily consume up to punctuation
  [.!?]           # group for unrolling the loop
  (?!['\"]?\\s|$) # (special) inner punctuation ok if
  [^.!?]*         # not followed by ws or EOS
)*                # greedily consume up to punctuation
[.!?]?            # zero or more (special normal*)
['\"]?            # optional ending punctuation
(?=\\s|$)"        # optional closing quote
```
### Capturing a word before (or after) a keyword

This is an example of creating and referencing groups. Each `( )` defines a group and can be referenced with `\\N` for the N<sup>th</sup> group.


```r
p1 <- 'I want this here-keyword there'
gsub('(.+\\s)(\\w+)(\\s|-)(keyword)(\\s|-)(\\w+).*', '\\2', p1, perl = TRUE)
gsub('(.+\\s)(\\w+)(\\s|-)(keyword)(\\s|-)(\\w+).*', '\\6', p1, perl = TRUE)
```

```
## [1] "here"
## [1] "there"
```

### Extracting time formats from strings


```r
p1 <- c('R uses 1:5 for 1, 2, 3, 4, 5.', 
        'At 3:00 we will meet up and leave by 4:30:20.',
        'We will meet at 6:33.', 'He ran it in 0:22.34829985234 seconds.')
regmatches(p1, gregexpr('\\d{0,2}:\\d{2}(?:[:.]\\d+)?', p1))
```

```
## [[1]]
## character(0)
## 
## [[2]]
## [1] "3:00"    "4:30:20"
## 
## [[3]]
## [1] "6:33"
## 
## [[4]]
## [1] "0:22.34829985234"
```

### Using `\K` and inserting spaces between words

The `\K` escape sequence resets the starting point of the reported match and any previously consumed characters are no longer included, basically throwing away everything matched up to that point.

Another example of using capturing groups to capture to matched context and referring back to each matched group in the replacement.


```r
p1 <- c('CamelCaseIsFun','ThisIsRegexxxxx')
gsub('[a-z]\\K(?=[A-Z])', ' ', p1, perl = TRUE)
```

```
## [1] "Camel Case Is Fun" "This Is Regexxxxx"
```

```
[a-z]       # any character of a to z
\K          # \K (resets the starting point of the reported match)
(?=         # look ahead to see if there is:
  [A-Z]     #   any character of A to Z
)           # end of look-ahead
```

### Removing words with repeating letters

It may be useful to filter out words with repeating characters which are not really words at all.

```{r, results='hold'}
p1 <- "aaaahahahahaha that was a good banana joke ahahah haha aha harhar"
gsub('\\b(\\S+?)\\1\\S*\\b', '', p1, perl = TRUE)
```

```
## [1] " that was a good banana joke   aha "
```

```
\b        # the boundary between a word char (\w) and
          # something that is not a word char
(         # group and capture to \1:
  \S+?     #  non-whitespace (all but \n, \r, \t, \f, and " ") (1 or
           #  more times (matching the least amount possible))
)         # end of \1
\1        # what was matched by capture \1
\S*       # non-whitespace (all but \n, \r, \t, \f, and " ") (0 or
          # more times (matching the most amount possible))
\b        # the boundary between a word char (\w) and
          # something that is not a word char
```
