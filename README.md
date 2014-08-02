regex
=====

### perl regex cheatsheet with examples in R

\* [Characters, metacharacters, and quantifiers](#specific-characters)    
\* [Anchors and look-arounds](#anchors)   
\* [Character shortcuts and special characters](#character-class-shortcuts)   
\* [Examples](#examples)    

\* references:   
[general perl regex](#www.erudil.com/preqr.pdf)     
[look arounds](#http://www.perlmonks.org/?node_id=518444)   

<div align=center><a href="http://xkcd.com/1171/"><img src="http://imgs.xkcd.com/comics/perl_problems.png" style="display: block; margin: auto;"/></a></div>

<div align=center><a href="http://xkcd.com/208/"><img src="http://imgs.xkcd.com/comics/regular_expressions.png" style="display: block; margin: auto;"/></a></div>

<div align=center><a href="http://xkcd.com/1313/"><img src="http://imgs.xkcd.com/comics/regex_golf.png" style="display: block; margin: auto;"/></a></div>

### Specific characters

 character | meaning
--------|--------
`\t`    |tab
`\n`    |newline (OS neutral)
`\r`    |carriage return
`\f`    |form feed
`\cx`   |control character **CTRL-_x_**
`\NNN`  |octal code for ASCII character _**NNN**_

**Quantifiers**

These quantifiers apply to the preceding *atom*. By default, they are "greedy" meaning they attempt to match as many characters as possible. In order to match as *few* characters as possible, follow them with a `?`.

character | meaning
--------|--------
`*`     |match **0** or more times, i.e., any number of times
`+`     |match **1** or more times, i.e., at least once
`?`     |match 0 or 1 times, i.e., at most once
`{n}`   |match *exactly* **n** times
`{n,}`  |match *at least* **n** times
`{n,m}` |match *at least* **n** times but *no more* than **m** times

**Metacharacters**

The following characters need to be escaped because they have special meanings: `\ | ( ) [ { ^ $ * + ? .`

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
`^`     |if the first character of a class, negates that class
`-`     |unless first, last, or escaped (i.e., `[ad-]`: match a, d, or -), used for a range (`[a-b]`: match any character in the range from a to b)

### Anchors

Also known as *zero-width (length) assertions*, these do not match any characters but "look around" to see what comes before and/or after the current position.

character | meaning
--------|--------
`^`     |match at the *beginning* of a string (or line if `/m` is used)
`$`     |match at the *end* of a string (or line if `/m` is used)
`\b`    |match at a "word" (`\w`) boundary
`\B`    |match at *not* a "word" boundary

**Look-ahead and look-behind**

Zero-length assertions can also be used to match extended patterns and require that a pattern match succeed (*positive assertion*) or fail (*negative assertion*).

character | meaning
--------|--------
`(?= )`   |positive look-ahead assertion
`(?! )`   |negative look-ahead assertion
`(?<= )`  |positive look-behind assertion
`(?!= )`  |negative look-behind assertion

Every extended pattern is written as a parenthetical group with a question mark as the first character. Notice that the `=` or `!` is always *last*. The directional indicator (`<`) is only present in the look-behind and comes *before* the positive or negative indicator.

Look-behind expressions cannot be of variable length. That means you cannot use quantifiers (?, *, +, or {1,5}) or alternation of different-length items inside them.

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

**Metaquote and case translations**

character | meaning
--------|--------
`\Q`    |quote (de-meta) characters until `\E`
`\U`    |uppercase characters until `\E`
`\L`    |lowercase characters until `\E`
`\l`    |lowercase next character
`\u`    |titlecase<sup>&dagger;</sup> next character
`\E`    |end modification

<font size =1><sup>&dagger;</sup>Unicode concept which most often is equal to uppercase, but for certain characters (like the German "sharp s") there is a difference.</font>

**Special variables**

character | meaning
--------|--------
``$` `` |characters to the left of the match
`$&`    |characters that matched
`$'`    |characters to the right of the match
`\N`    |characters captured by the N<sup>th</sup> set of parentheses (if on the match side)
`$N`    |characters captured by the N<sup>th</sup> set of parentheses (if *not* on the match side)

**Modifiers**

These modifiers apply to the entire pattern (all except `/e` apply to both `m//` and `s///`)

character | meaning
--------|--------
`/i`    |ignore case
`/g`    |match globally (all)
`/m`    |let `^` and `$` match next to embedded `\n`
`/s`    |let `.` match `\n`
`/x`    |ignore most whitespace and allow comments
`/e`    |evaluate right-hand side of `s///` as an expression

**Binding operators**

character | meaning
--------|--------
`=~`    |TRUE if the regex matches
`!~`    |TRUE if the regex does *not* match

### Examples

\* **Trimming whitespace**


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
```

```
## [1] "  this text string   has much white space \nand even some new \n\nlines"
```

```
## [1] "this text string   has much white space \nand even some new \n\nlines"
```

```
## [1] "thistextstringhasmuchwhitespaceandevensomenewlines"
```

\* **Find the last occurrence of a string**

There are actually a number of ways to get the last occurrence that don't involve look-arounds, but if you think of "the last foo" as "foo that isn't followed by a string containing foo," you can express that notion like this:


```r
p1 <- 'the last word not followed by word'
p2 <- 'the last word not followed by werd'
gsub('word(?!.*word)', 'XXX', p1, perl = TRUE)
gsub('word(?!.*word)', 'XXX', p2, perl = TRUE)
```

```
## [1] "the last word not followed by XXX"
```

```
## [1] "the last XXX not followed by werd"
```

\* **Capitalize the first letter of each word**

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
```

```
## [1] "Look For A Lower-Case Letter Proceded By A Word Boundary"
```

\* **Replacing all or parts of a string**

Many substitutions match a chunk of text and then replace part or all of it. You can often avoid that by using look-arounds:


```r
p1 <- 'this is a wordy word and ends on a word'
gsub('(?<=word)', ',', p1, perl = TRUE)     ## find every pattern match
gsub('(?<=word\\b)', ',', p1, perl = TRUE)  ## only patterns on a word boundary
gsub('(?<=word$)', ',', p1, perl = TRUE)    ## only patters on a string boundary
```

```
## [1] "this is a word,y word, and ends on a word,"
```

```
## [1] "this is a wordy word, and ends on a word,"
```

```
## [1] "this is a wordy word and ends on a word,"
```

\* **Inserting a character between two matches**

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

\* **Matching a pattern that doesn't include another pattern**

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

\* **Greed:** placing `?` after quantifiers determine if we want to match until the last occurrence of the look-ahead or until the first occurrence.


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
```

```
## [1] " a to z and after z if there is a z that follows but at not the last z "
```

```
## [1] " a to z "  " after z " " a z "
```

```
## [1] " a to z "            " after z "           " a z "              
## [4] " at not the last z "
```

\* **Extracting from between parentheses (or other characters)**


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
```

```
## [1] "everything" "if any"
```

```
## [1] "(everything)" "(if any)"
```

\* **Nesting (look-arounds inside of other look-arounds)**

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
```

```
## [1] "this is an unformatted, ugly sentence. no spaces one, two, many 1,000.0 commas"
```

\* **Capitalizing every first word of every sentence**


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
\* **Capturing a word before (or after) a keyword**

This is an example of creating and referencing groups. Each `( )` defines a group and can be referenced with `\\N` for the N<sup>th</sup> group.


```r
p1 <- 'I want this here-keyword there'
gsub('(.+\\s)(\\w+)(\\s|-)(keyword)(\\s|-)(\\w+).*', '\\2', p1, perl = TRUE)
gsub('(.+\\s)(\\w+)(\\s|-)(keyword)(\\s|-)(\\w+).*', '\\6', p1, perl = TRUE)
```

```
## [1] "here"
```

```
## [1] "there"
```
