


# Ch. 14: Strings

\BeginKnitrBlock{rmdimportant}<div class="rmdimportant">**Key questions:**  
  
* 14.2.5. #3, 6
* 14.3.2.1. #2
* 14.3.3.1 #1, #2</div>\EndKnitrBlock{rmdimportant}

\BeginKnitrBlock{rmdtip}<div class="rmdtip">**Functions and notes:**</div>\EndKnitrBlock{rmdtip}
* `writeLines`: see raw contents of a string (prints each string in a vector on a new line)
* `str_length`: number of characters in a string
* `str_c`: combine two or more strings
    * use `collapse` arg to make vector of strings to single string
* `str_replace_na`: print `NA` as "NA"
* `str_sub`: `start` and `end` args to specify position to remove (or replace), can use negative numbers as well to represent from back
* `str_to_lower`, `str_to_upper`, `str_to_upper`: for changing string case
    * `locale` arg (to handle slight differences in characters)
* `str_order`, `str_sort`: more robust version of `order` and `sort` which take allow a `locale` argument
* `str_view`, `str_view_all`: shows how character and regular expression match

* `\d`: matches any digit.
* `\s`: matches any whitespace (e.g. space, tab, newline).
* `[abc]`: matches a, b, or c.
* `[^abc]`: matches anything except a, b, or c.

* `{n}`: exactly n
* `{n,}`: n or more
* `{,m}`: at most m
* `{n,m}`: between n and m

* `str_detect`: returns logical vector of `TRUE`/`FALSE` values
* `str_subset`: subset of `TRUE` values from `str_detect`
* `str_count`: number of matches in a string
* `str_extract`: extract actual text of a match
* `str_extract_all`: returns list with all matches
    * `simplify = TRUE` returns a matrix
* `str_match`: similar to `str_extract` but gives each individual component of match in a matrix, rather than a character vector (also have a `str_match_all`)
* `tidyr::extract`: like `str_match` but name columns with matches which are moved into new columns
* `str_replace`, `str_replace_all`: replace matches with new strings
* `str_split` split a string into pieces -- default is individual words (returns list)
    * `simplify = TRUE` again will return a matrix
* `boundary` use to specify level of split, e.g. `str_view_all(x, boundary("word"))`
* `str_locate`, `str_locate_all`: gives starting an dending positions of each match
* `regex` use in match to specify more options, e.g. `str_view(bananas, regex("banana", ignore_case = TRUE))`
    * `multiline = TRUE` allows `^` and `$` to match start and end of each line (rather than of string)
    * `comments = TRUE` allows you to add comments on a complex regular expression
    * `dotall = TRUE` allows `.` to match more than just letters e.g. `\\n`
* `fixed`, `coll` related alternatives to `regex`
* `apropos` searches all objects available from global environment (e.g. say you can't remember function name)
* `dir`: lists all files in a directory
    * `pattern` arg takes a regex
* `stringi` more comprehensive package than `stringr` (~5x as many funs)

## 14.2: String basics

Use `wrteLines` to show what string 'This string has a \\n new line' looks like printed.


```r
string_exp <- 'This string has a \n new line'
print(string_exp)
```

```
## [1] "This string has a \n new line"
```

```r
writeLines(string_exp)
```

```
## This string has a 
##  new line
```

To see full list of specifal characters:


```r
?'"'
```

Objects of length 0 are silently dropped. This is particularly useful in conjunction with `if`:


```r
name <- "Bryan"
time_of_day <- "morning"
birthday <- FALSE

str_c(
  "Good ", time_of_day, " ", name,
  if (birthday) " and HAPPY BIRTHDAY",
  "."
)
```

```
## [1] "Good morning Bryan."
```

Collapse vectors into single string


```r
str_c(c("x", "y", "z"), c("a", "b", "c"), collapse = ", ")
```

```
## [1] "xa, yb, zc"
```

Can use assignment form of `str_sub()`


```r
x <- c("Apple", "Banana", "Pear")
str_sub(x, 1, 1) <- str_to_lower(str_sub(x, 1, 1))
x
```

```
## [1] "apple"  "banana" "pear"
```

`str_pad` looks interesting


```r
str_pad("the dogs come for you.", width = 40, pad = ",", side = "both") #must specify width =, side = default is left
```

```
## [1] ",,,,,,,,,the dogs come for you.,,,,,,,,,"
```

### 14.2.5

1.  *In code that doesn't use stringr, you'll often see `paste()` and `paste0()`. What's the difference between the two functions?*  

    * `paste0()` has no `sep` argument and just appends any value provided like another string vector. 
    * They differ from `str_c()` in that they  automatically convert `NA` values to character. 
    
    ```r
    paste("a", "b", "c", c("x", "y"), sep = "-")
    ```
    
    ```
    ## [1] "a-b-c-x" "a-b-c-y"
    ```
    
    ```r
    paste0("a", "b", "c", c("x", "y"), sep = "-")
    ```
    
    ```
    ## [1] "abcx-" "abcy-"
    ```
    
    *What `stringr` function are they equivalent to?*  
    
    `paste()` and `paste0()` are similar to `str_c()` though are different in how they handle NAs (see below). They also will return a warning when recycling vectors whose legth do not have a common factor.  
    
    ```r
    paste(c("a", "b", "x"), c("x", "y"), sep = "-")
    ```
    
    ```
    ## [1] "a-x" "b-y" "x-x"
    ```
    
    ```r
    str_c(c("a", "b", "x"), c("x", "y"), sep = "-")
    ```
    
    ```
    ## Warning in stri_c(..., sep = sep, collapse = collapse, ignore_null = TRUE):
    ## longer object length is not a multiple of shorter object length
    ```
    
    ```
    ## [1] "a-x" "b-y" "x-x"
    ```
    
     *How do the functions differ in their handling of `NA`?*
    
    ```r
    paste(c("a", "b"), c(NA, "y"), sep = "-")
    ```
    
    ```
    ## [1] "a-NA" "b-y"
    ```
    
    ```r
    str_c(c("a", "b"), c(NA, "y"), sep = "-")
    ```
    
    ```
    ## [1] NA    "b-y"
    ```
     
     
1.  *In your own words, describe the difference between the `sep` and `collapse` arguments to `str_c()`.*
    
    `sep` puts characters between items within a vector, collapse puts a character between vectors being collapsed

1.  *Use `str_length()` and `str_sub()` to extract the middle character from a string.* 
    
    ```r
    x <- "world"
    str_sub(x, start = ceiling(str_length(x) / 2), end = ceiling(str_length(x) / 2))
    ```
    
    ```
    ## [1] "r"
    ```

    *What will you do if the string has an even number of characters?*
    
    In this circumstance the above solution would take the anterior middle value, below is a solution that would return both middle values.
    
    ```r
    x <- "worlds"
    
    str_sub(x, ceiling(str_length(x) / 2 + 1), start = ceiling(str_length(x) / 2 + 1))
    ```
    
    ```
    ## [1] "l"
    ```
    
    ```r
    str_sub(x,
            start = ifelse(str_length(x) %% 2 == 0, floor(str_length(x) / 2), ceiling(str_length(x) / 2 )), 
            end = floor(str_length(x) / 2) + 1)
    ```
    
    ```
    ## [1] "rl"
    ```

1.  *What does `str_wrap()` do? When might you want to use it?*
    
    * Use `indent` for first line, `exdent` for others  
    * could use `str_wrap()` for editing of documents etc., setting `width = 1` will give each word its own line
    
    
    ```r
    str_wrap("Tonight, we dine in Hell.", width = 10, indent = 0, exdent = 3) %>% 
      writeLines()
    ```
    
    ```
    ## Tonight,
    ##    we dine in
    ##    Hell.
    ```
    
1.  *What does `str_trim()` do? What's the opposite of `str_trim()`?*
    Removes whitespace from beginning and end of character, `side` argument specifies which side 
    
    ```r
    str_trim("   so much white space   ", side = "right") # (default is 'both')
    ```
    
    ```
    ## [1] "   so much white space"
    ```

1.  *Write a function that turns (e.g.) a vector `c("a", "b", "c")` into the string `a, b, and c`. Think carefully about what it should do if given a vector of length 0, 1, or 2.*
    
    ```r
    vec_to_string <- function(x) {
      
      #If 1 or 0 length vector
      if (length(x) < 2)
      return(x)
      comma <- ifelse(length(x) > 2, ", ", " ")
      b <- str_c(x, collapse = comma)
      
      #replace ',' with 'and' in last
      str_sub(b,-(str_length(x)[length(x)] + 1), -(str_length(x)[length(x)] +
      1)) <- " and "
      return(b)
    }
    x <- c("a", "b", "c", "d")
    vec_to_string(x)
    ```
    
    ```
    ## [1] "a, b, c, and d"
    ```
    
  
## 14.3: Matching patterns w/ regex


```r
x <- c("apple", "banana", "pear")
str_view(x, "an")
```

<!--html_preserve--><div id="htmlwidget-01c5e0afea8ae15f593d" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-01c5e0afea8ae15f593d">{"x":{"html":"<ul>\n  <li>apple<\/li>\n  <li>b<span class='match'>an<\/span>ana<\/li>\n  <li>pear<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

To match a literal `\` need `\\\\` because both string and regex will escape it.

```r
x <- "a\\b"
writeLines(x)
```

```
## a\b
```

```r
str_view(x,"\\\\")
```

<!--html_preserve--><div id="htmlwidget-e54bc9d833239793979d" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-e54bc9d833239793979d">{"x":{"html":"<ul>\n  <li>a<span class='match'>\\<\/span>b<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

Using `\b` to set boundary between words (not used often)


```r
apropos("\\bsum\\b")
```

```
## [1] "contr.sum" "sum"
```

```r
apropos("^(sum)$")
```

```
## [1] "sum"
```

Other special characters:  

* `\d`: matches any digit.
* `\s`: matches any whitespace (e.g. space, tab, newline).
* `[abc]`: matches a, b, or c.
* `[^abc]`: matches anything except a, b, or c.


Controlling number of times:  
  
* `?`: 0 or 1
* `+`: 1 or more
* `*`: 0 or more
* `{n}`: exactly n
* `{n,}`: n or more
* `{,m}`: at most m
* `{n,m}`: between n and m

By default these matches are "greedy": they will match the longest string possible. You can make them "lazy", matching the shortest string possible by putting a `?` after them. This is an advanced feature of regular expressions, but it's useful to know that it exists:


```r
x <- "1888 is the longest year in Roman numerals: MDCCCLXXXVIII"
str_view(x, 'C{2,3}')
```

<!--html_preserve--><div id="htmlwidget-54ab3965c6614abf7df3" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-54ab3965c6614abf7df3">{"x":{"html":"<ul>\n  <li>1888 is the longest year in Roman numerals: MD<span class='match'>CCC<\/span>LXXXVIII<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

```r
str_view(x, 'C{2,3}?')
```

<!--html_preserve--><div id="htmlwidget-2fff63fa4c6dc5628c01" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-2fff63fa4c6dc5628c01">{"x":{"html":"<ul>\n  <li>1888 is the longest year in Roman numerals: MD<span class='match'>CC<\/span>CLXXXVIII<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

### 14.3.1.1

1.  *Explain why each of these strings don't match a `\`: `"\"`, `"\\"`, `"\\\"`.*

    `"\"` -> leaves open quote string because escapes quote
    `"\\"`,  -> escapes second `\` so left with blank
    `"\\\"` -> third `\` escapes quote so left with open quote as well
    
1.  *How would you match the sequence `"'\`?*
    
    ```r
    x <- "alfred\"'\\goes"
    writeLines(x)
    ```
    
    ```
    ## alfred"'\goes
    ```
    
    ```r
    str_view(x, "\\\"'\\\\")
    ```
    
    <!--html_preserve--><div id="htmlwidget-840ef7d4492b483e7b8c" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-840ef7d4492b483e7b8c">{"x":{"html":"<ul>\n  <li>alfred<span class='match'>\"'\\<\/span>goes<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

1.  *What patterns will the regular expression `\..\..\..` match?*

    Would match 6 character string of following form "(dot)(anychar)(dot)(anychar)(dot)(anychar)"
    
    ```r
    x <- c("alf.r.e.dd.ss..lsdf.d.kj")
    str_view(x, pattern = "\\..\\..\\..")
    ```
    
    <!--html_preserve--><div id="htmlwidget-731cdb890c1a2c0ff57c" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-731cdb890c1a2c0ff57c">{"x":{"html":"<ul>\n  <li>alf<span class='match'>.r.e.d<\/span>d.ss..lsdf.d.kj<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

    *How would you represent it as a string?*
    
    ```r
    x_pattern <- "\\..\\..\\.."
    writeLines(x_pattern)
    ```
    
    ```
    ## \..\..\..
    ```

### 14.3.2.1

1.  *How would you match the literal string `"$^$"`?*

    
    ```r
    x <- "so it goes $^$ here"
    str_view(x, "\\$\\^\\$")
    ```
    
    <!--html_preserve--><div id="htmlwidget-68c2cfd4511bb312fb5d" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-68c2cfd4511bb312fb5d">{"x":{"html":"<ul>\n  <li>so it goes <span class='match'>$^$<\/span> here<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->


1.  *Given the corpus of common words in `stringr::words`, create regular expressions that find all words that:*
    
    1. *Start with "y".*
    
    ```r
    str_view(stringr::words, "^y", match = TRUE)
    ```
    
    <!--html_preserve--><div id="htmlwidget-3d1c6f287b92289a4f09" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-3d1c6f287b92289a4f09">{"x":{"html":"<ul>\n  <li><span class='match'>y<\/span>ear<\/li>\n  <li><span class='match'>y<\/span>es<\/li>\n  <li><span class='match'>y<\/span>esterday<\/li>\n  <li><span class='match'>y<\/span>et<\/li>\n  <li><span class='match'>y<\/span>ou<\/li>\n  <li><span class='match'>y<\/span>oung<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
    
    2. *End with "x"*
    
    ```r
    str_view(stringr::words, "x$", match = TRUE)
    ```
    
    <!--html_preserve--><div id="htmlwidget-f4e561a181b5cfa4ff4c" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-f4e561a181b5cfa4ff4c">{"x":{"html":"<ul>\n  <li>bo<span class='match'>x<\/span><\/li>\n  <li>se<span class='match'>x<\/span><\/li>\n  <li>si<span class='match'>x<\/span><\/li>\n  <li>ta<span class='match'>x<\/span><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
    
    3. *Are exactly three letters long. (Don't cheat by using `str_length()`!)*
    
    ```r
    str_view(stringr::words, "^...$", match = TRUE)
    ```
    
    4. *Have seven letters or more.*
    
    ```r
    str_view(stringr::words, ".......", match = TRUE)
    ```

    Since this list is long, you might want to use the `match` argument to `str_view()` to show only the matching or non-matching words.

### 14.3.3.1

1.  *Create regular expressions to find all words that:*

    1. *Start with a vowel.*
    
    
    ```r
    str_view(stringr::words, "^[aeiou]", match = TRUE)
    ```

    2. *That only contain consonants. (Hint: thinking about matching "not"-vowels.)*
    
    
    ```r
    str_view(stringr::words, "^[^aeiou]*[^aeiouy]$", match = TRUE)
    ```
    
    <!--html_preserve--><div id="htmlwidget-71c351401cf77305bc72" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-71c351401cf77305bc72">{"x":{"html":"<ul>\n  <li><span class='match'>mrs<\/span><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

    3. *End with `ed`, but not with `eed`.*
    
    ```r
    str_view(stringr::words, "[^e]ed$", match = TRUE)
    ```
    
    <!--html_preserve--><div id="htmlwidget-718da376eb423f0c83cb" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-718da376eb423f0c83cb">{"x":{"html":"<ul>\n  <li><span class='match'>bed<\/span><\/li>\n  <li>hund<span class='match'>red<\/span><\/li>\n  <li><span class='match'>red<\/span><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
    
    4. *End with `ing` or `ise`.*
    
    ```r
    str_view(stringr::words, "(ing|ise)$", match = TRUE)
    ```
    
    <!--html_preserve--><div id="htmlwidget-7ca2c564312bcc893882" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-7ca2c564312bcc893882">{"x":{"html":"<ul>\n  <li>advert<span class='match'>ise<\/span><\/li>\n  <li>br<span class='match'>ing<\/span><\/li>\n  <li>dur<span class='match'>ing<\/span><\/li>\n  <li>even<span class='match'>ing<\/span><\/li>\n  <li>exerc<span class='match'>ise<\/span><\/li>\n  <li>k<span class='match'>ing<\/span><\/li>\n  <li>mean<span class='match'>ing<\/span><\/li>\n  <li>morn<span class='match'>ing<\/span><\/li>\n  <li>otherw<span class='match'>ise<\/span><\/li>\n  <li>pract<span class='match'>ise<\/span><\/li>\n  <li>ra<span class='match'>ise<\/span><\/li>\n  <li>real<span class='match'>ise<\/span><\/li>\n  <li>r<span class='match'>ing<\/span><\/li>\n  <li>r<span class='match'>ise<\/span><\/li>\n  <li>s<span class='match'>ing<\/span><\/li>\n  <li>surpr<span class='match'>ise<\/span><\/li>\n  <li>th<span class='match'>ing<\/span><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
    
1.  *Empirically verify the rule "i before e except after c".*

    
    ```r
    str_view(stringr::words, "(^(ei))|cie|[^c]ei", match = TRUE)
    ```
    
    <!--html_preserve--><div id="htmlwidget-d56f1d1377afb821213b" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-d56f1d1377afb821213b">{"x":{"html":"<ul>\n  <li><span class='match'>ei<\/span>ght<\/li>\n  <li><span class='match'>ei<\/span>ther<\/li>\n  <li>s<span class='match'>cie<\/span>nce<\/li>\n  <li>so<span class='match'>cie<\/span>ty<\/li>\n  <li><span class='match'>wei<\/span>gh<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
    

1.  *Is "q" always followed by a "u"?*
    
    ```r
    str_view(stringr::words, "q[^u]", match = TRUE)
    ```
    
    <!--html_preserve--><div id="htmlwidget-f3ca49dca8cf47dde7e0" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-f3ca49dca8cf47dde7e0">{"x":{"html":"<ul>\n  <li><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
     
     of the words in list, yes.

1.  *Write a regular expression that matches a word if it's probably written in British English, not American English.*
    
    ```r
    str_view(stringr::words, "(l|b)our|parat", match = TRUE)
    ```
    
    <!--html_preserve--><div id="htmlwidget-d32b7bb75c054cd178bc" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-d32b7bb75c054cd178bc">{"x":{"html":"<ul>\n  <li>co<span class='match'>lour<\/span><\/li>\n  <li>la<span class='match'>bour<\/span><\/li>\n  <li>se<span class='match'>parat<\/span>e<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

1.  *Create a regular expression that will match telephone numbers as commonly written in your country.*
    
    ```r
    x <- c("dkl kls. klk. _", "(425) 591-6020", "her number is (581) 434-3242", "442", "  dsi")
    str_view(x, "\\(\\d\\d\\d\\)\\s\\d\\d\\d-\\d\\d\\d\\d")
    ```
    
    <!--html_preserve--><div id="htmlwidget-b177b6c95c429eb9c9a8" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-b177b6c95c429eb9c9a8">{"x":{"html":"<ul>\n  <li>dkl kls. klk. _<\/li>\n  <li><span class='match'>(425) 591-6020<\/span><\/li>\n  <li>her number is <span class='match'>(581) 434-3242<\/span><\/li>\n  <li>442<\/li>\n  <li>  dsi<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
    
    Aboves not a good way to solve this, will see better methods in next section.


### 14.3.4.1

1.  *Describe the equivalents of `?`, `+`, `*` in `{m,n}` form.*
    `?` : `{0,1}`
    `+` : `{1, }`
    `*` : `{0, }`

1.  *Describe in words what these regular expressions match: (read carefully to see if I'm using a regular expression or a string that defines a regular expression.)*

    1. `^.*$`   : starts with anything, and ends with anything--matches whole thing
    
    
    ```r
    str_view(x, "^.*$")
    ```
    
    <!--html_preserve--><div id="htmlwidget-6a9f4cf77c703ae8a211" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-6a9f4cf77c703ae8a211">{"x":{"html":"<ul>\n  <li><span class='match'>dkl kls. klk. _<\/span><\/li>\n  <li><span class='match'>(425) 591-6020<\/span><\/li>\n  <li><span class='match'>her number is (581) 434-3242<\/span><\/li>\n  <li><span class='match'>442<\/span><\/li>\n  <li><span class='match'>  dsi<\/span><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
    
    2. `"\\{.+\\}"` : match text in brackets greater than nothing
    
    
    ```r
    x <- c("test", "some in {brackets}", "just {} no match")
    str_view(x, "\\{.+\\}")
    ```
    
    <!--html_preserve--><div id="htmlwidget-8329b3d43f5def4ca080" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-8329b3d43f5def4ca080">{"x":{"html":"<ul>\n  <li>test<\/li>\n  <li>some in <span class='match'>{brackets}<\/span><\/li>\n  <li>just {} no match<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
    
    3. `\d{4}-\d{2}-\d{2}`: 4 numbers - 2 numbers - 2 numbers
    
    
    ```r
    x <- c("4444-22-22", "test", "333-4444-22")
    str_view(x, "\\d{4}-\\d{2}-\\d{2}")
    ```
    
    <!--html_preserve--><div id="htmlwidget-9fa755d77492b7cfb4d5" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-9fa755d77492b7cfb4d5">{"x":{"html":"<ul>\n  <li><span class='match'>4444-22-22<\/span><\/li>\n  <li>test<\/li>\n  <li>333-4444-22<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
    
    4. `"\\\\{4}"`:  4 brackets
    
    
    ```r
    x <- c("\\\\\\\\", "\\\\\\", "\\\\", "\\")
    writeLines(x)
    ```
    
    ```
    ## \\\\
    ## \\\
    ## \\
    ## \
    ```
    
    ```r
    str_view(x, "\\\\{4}")
    ```
    
    <!--html_preserve--><div id="htmlwidget-42d318bafe6ac37008d3" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-42d318bafe6ac37008d3">{"x":{"html":"<ul>\n  <li><span class='match'>\\\\\\\\<\/span><\/li>\n  <li>\\\\\\<\/li>\n  <li>\\\\<\/li>\n  <li>\\<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
    
    ```r
    x <- c("\\\\\\\\", "\\\\\\", "\\\\", "\\")
    str_view(x, "\\\\\\\\")
    ```
    
    <!--html_preserve--><div id="htmlwidget-cb5f127a62a7a0ed12c8" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-cb5f127a62a7a0ed12c8">{"x":{"html":"<ul>\n  <li><span class='match'>\\\\<\/span>\\\\<\/li>\n  <li><span class='match'>\\\\<\/span>\\<\/li>\n  <li><span class='match'>\\\\<\/span><\/li>\n  <li>\\<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

1.  *Create regular expressions to find all words that:*

    1. find all words that start with three consonants
    
    
    ```r
    str_view(stringr::words, "^[^aeoiouy]{3}", match = TRUE)
    ```
    
    <!--html_preserve--><div id="htmlwidget-685244c4b3faf4cf80c3" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-685244c4b3faf4cf80c3">{"x":{"html":"<ul>\n  <li><span class='match'>Chr<\/span>ist<\/li>\n  <li><span class='match'>Chr<\/span>istmas<\/li>\n  <li><span class='match'>mrs<\/span><\/li>\n  <li><span class='match'>sch<\/span>eme<\/li>\n  <li><span class='match'>sch<\/span>ool<\/li>\n  <li><span class='match'>str<\/span>aight<\/li>\n  <li><span class='match'>str<\/span>ategy<\/li>\n  <li><span class='match'>str<\/span>eet<\/li>\n  <li><span class='match'>str<\/span>ike<\/li>\n  <li><span class='match'>str<\/span>ong<\/li>\n  <li><span class='match'>str<\/span>ucture<\/li>\n  <li><span class='match'>thr<\/span>ee<\/li>\n  <li><span class='match'>thr<\/span>ough<\/li>\n  <li><span class='match'>thr<\/span>ow<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
    
    * Include `y` because when it shows up otherwise, is in vowel form.
    
    2. have three or more vowels in a row
    
    
    ```r
    str_view(stringr::words, "[aeiou]{3}", match = TRUE)
    ```
    
    <!--html_preserve--><div id="htmlwidget-7e3131aa2dbf1537d6ff" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-7e3131aa2dbf1537d6ff">{"x":{"html":"<ul>\n  <li>b<span class='match'>eau<\/span>ty<\/li>\n  <li>obv<span class='match'>iou<\/span>s<\/li>\n  <li>prev<span class='match'>iou<\/span>s<\/li>\n  <li>q<span class='match'>uie<\/span>t<\/li>\n  <li>ser<span class='match'>iou<\/span>s<\/li>\n  <li>var<span class='match'>iou<\/span>s<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
    
    In this case, do not include the `y`.
    
    3. have 2 or more vowel-consonant pairs in a row
    
    
    ```r
    str_view(stringr::words, "([aeiou][^aeiou]){2,}", match = TRUE)
    ```

1.  *Solve the beginner regexp crosswords at*
    *<https://regexcrossword.com/challenges/beginner>.*

### 14.3.5.1

1.  *Describe, in words, what these expressions will match:*

    * I change questions 1 and 3 to what I think they were meant to be written as `(.)\\1\\1` and `(.)\\1` respectively.

    1. `(.)\\1\\1` : repeat the char in the first group, and then repeat that char again
    1. `"(.)(.)\\2\\1"` : 1st char, 2nd char followed by 2nd char, first char
    1. `(..)\\1` : 2 chars repeated twice
    1. `"(.).\\1.\\1"` : chars shows-up 3 times with one character between each
    1. `"(.)(.)(.).*\\3\\2\\1"` : 3 chars in one order with * chars between, then 3 chars with 3 letters in the reverse order of what it started

    
    ```r
    x <- c("steefddff", "ssdfsdfffsdasdlkd", "DLKKJIOWdkl", "klnlsd", "t11", "(.)\1\1")
    
    str_view_all(x, "(.)\\1\\1", match = TRUE) #xxx
    ```
    
    <!--html_preserve--><div id="htmlwidget-e2694262fd6f8223808c" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-e2694262fd6f8223808c">{"x":{"html":"<ul>\n  <li>ssdfsd<span class='match'>fff<\/span>sdasdlkd<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
    
    ```r
    str_view_all(fruit, "(.)(.)\\2\\1", match = TRUE) #xyyx
    ```
    
    <!--html_preserve--><div id="htmlwidget-5ef23ee4331317c66513" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-5ef23ee4331317c66513">{"x":{"html":"<ul>\n  <li>bell p<span class='match'>eppe<\/span>r<\/li>\n  <li>chili p<span class='match'>eppe<\/span>r<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
    
    ```r
    str_view_all(fruit, "(..)\\1", match = TRUE) #xxyy
    ```
    
    <!--html_preserve--><div id="htmlwidget-413159c1a57cba56d43a" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-413159c1a57cba56d43a">{"x":{"html":"<ul>\n  <li>b<span class='match'>anan<\/span>a<\/li>\n  <li><span class='match'>coco<\/span>nut<\/li>\n  <li><span class='match'>cucu<\/span>mber<\/li>\n  <li><span class='match'>juju<\/span>be<\/li>\n  <li><span class='match'>papa<\/span>ya<\/li>\n  <li>s<span class='match'>alal<\/span> berry<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
    
    ```r
    str_view(stringr::words, "(.).\\1.\\1", match = TRUE) #x.x.x
    ```
    
    <!--html_preserve--><div id="htmlwidget-9672b3da7b9319a6e8bc" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-9672b3da7b9319a6e8bc">{"x":{"html":"<ul>\n  <li><span class='match'>eleve<\/span>n<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
    
    ```r
    str_view(stringr::words, "(.)(.)(.).*\\3\\2\\1", match = TRUE) #xyz.*zyx
    ```
    
    <!--html_preserve--><div id="htmlwidget-c9e78af2c106feb9d6d2" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-c9e78af2c106feb9d6d2">{"x":{"html":"<ul>\n  <li><span class='match'>paragrap<\/span>h<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

1.  *Construct regular expressions to match words that:*

    1. *Start and end with the same character.*
    
    ```r
    str_view(stringr::words, "^(.).*\\1$", match = TRUE)
    ```
    
    2. *Contain a repeated pair of letters*
       (e.g. "church" contains "ch" repeated twice.)
    
    ```r
    str_view(stringr::words, "(..).*\\1", match = TRUE)
    ```
    
    3. *Contain one letter repeated in at least three places*
       (e.g. "eleven" contains three "e"s.)
    
    ```r
    str_view(stringr::words, "(.).+\\1.+\\1", match = TRUE)
    ```


## 14.4 Tools


```r
noun <- "(a|the) ([^ \\.]+)"

has_noun <- sentences %>%
  str_subset(noun) %>%
  head(10) 

has_noun %>% 
  str_extract_all(noun, simplify = TRUE)

#creates split into seperate pieces
has_noun %>% 
  str_match_all(noun)

#Can make dataframe with, but need to name all
tibble(has_noun = has_noun) %>% 
  extract(has_noun, into = c("article", "noun"), regex = noun)
```

* When using `boundary()` with `str_split` can set to  "character", "line", "sentence", and "word" and gives alternative to splitting by pattern.

### 14.4.2

1.  *For each of the following challenges, try solving it by using both a single*
    *regular expression, and a combination of multiple `str_detect()` calls.*
    
    1.  *Find all words that start or end with `x`.*
    
    ```r
    str_subset(words, "^x|x$")
    ```
    
    ```
    ## [1] "box" "sex" "six" "tax"
    ```
    
    2.  *Find all words that start with a vowel and end with a consonant.*
    
    ```r
    str_subset(words, "^[aeiou].*[^aeiouy]$")
    ```
    
    ```
    ##   [1] "about"       "accept"      "account"     "across"      "act"        
    ##   [6] "actual"      "add"         "address"     "admit"       "affect"     
    ##  [11] "afford"      "after"       "afternoon"   "again"       "against"    
    ##  [16] "agent"       "air"         "all"         "allow"       "almost"     
    ##  [21] "along"       "alright"     "although"    "always"      "amount"     
    ##  [26] "and"         "another"     "answer"      "apart"       "apparent"   
    ##  [31] "appear"      "appoint"     "approach"    "arm"         "around"     
    ##  [36] "art"         "as"          "ask"         "at"          "attend"     
    ##  [41] "awful"       "each"        "east"        "eat"         "effect"     
    ##  [46] "egg"         "eight"       "either"      "elect"       "electric"   
    ##  [51] "eleven"      "end"         "english"     "enough"      "enter"      
    ##  [56] "environment" "equal"       "especial"    "even"        "evening"    
    ##  [61] "ever"        "exact"       "except"      "exist"       "expect"     
    ##  [66] "explain"     "express"     "if"          "important"   "in"         
    ##  [71] "indeed"      "individual"  "inform"      "instead"     "interest"   
    ##  [76] "invest"      "it"          "item"        "obvious"     "occasion"   
    ##  [81] "odd"         "of"          "off"         "offer"       "often"      
    ##  [86] "old"         "on"          "open"        "or"          "order"      
    ##  [91] "original"    "other"       "ought"       "out"         "over"       
    ##  [96] "own"         "under"       "understand"  "union"       "unit"       
    ## [101] "unless"      "until"       "up"          "upon"        "usual"
    ```
    
    Counted `y` as a vowel if ending with, but not to start. This does not work perfect. For example words like `ygritte` would still be included even though `y` is activng as a vowel there whereas words like `boy` would be excluded even though acting as a consonant there. From here on out I am going to always exclude `y`.
    
    3.  *Are there any words that contain at least one of each different vowel?*
    
    ```r
    vowels <- c("a","e","i","o","u")
    words[str_detect(words, "a") &
            str_detect(words, "e") &
            str_detect(words, "i") &
            str_detect(words, "o") & 
            str_detect(words, "u")]
    ```
    
    ```
    ## character(0)
    ```
    
    No. 

1.  *What word has the highest number of vowels? What word has the highest*
    *proportion of vowels? (Hint: what is the denominator?)*
    
    ```r
    vowel_counts <- tibble(words = words, 
                           n_string = str_length(words),
                           n_vowel = str_count(words, vowels),
                           prop_vowel = n_vowel / n_string)
    ```
    
    'Experience' has the most vowels
    
    ```r
    vowel_counts %>% 
      arrange(desc(n_vowel))
    ```
    
    ```
    ## # A tibble: 980 x 4
    ##    words      n_string n_vowel prop_vowel
    ##    <chr>         <int>   <int>      <dbl>
    ##  1 experience       10       4      0.4  
    ##  2 individual       10       3      0.3  
    ##  3 achieve           7       2      0.286
    ##  4 actual            6       2      0.333
    ##  5 afternoon         9       2      0.222
    ##  6 against           7       2      0.286
    ##  7 already           7       2      0.286
    ##  8 america           7       2      0.286
    ##  9 benefit           7       2      0.286
    ## 10 choose            6       2      0.333
    ## # ... with 970 more rows
    ```
    
    'a' has the highest proportion
    
    ```r
    vowel_counts %>% 
      arrange(desc(prop_vowel))
    ```
    
    ```
    ## # A tibble: 980 x 4
    ##    words n_string n_vowel prop_vowel
    ##    <chr>    <int>   <int>      <dbl>
    ##  1 a            1       1      1    
    ##  2 too          3       2      0.667
    ##  3 wee          3       2      0.667
    ##  4 feed         4       2      0.5  
    ##  5 in           2       1      0.5  
    ##  6 look         4       2      0.5  
    ##  7 need         4       2      0.5  
    ##  8 room         4       2      0.5  
    ##  9 so           2       1      0.5  
    ## 10 soon         4       2      0.5  
    ## # ... with 970 more rows
    ```

### 14.4.3.1

1.  *In the previous example, you might have noticed that the regular*
    *expression matched "flickered", which is not a colour. Modify the *
    *regex to fix the problem.*
    
    Add space in front of colors:
    
    ```r
    colours <- c("red", "orange", "yellow", "green", "blue", "purple") %>% 
      paste0(" ", .)
    
    colour_match <- str_c(colours, collapse = "|")
    
    more <- sentences[str_count(sentences, colour_match) > 1]
    str_view_all(more, colour_match)
    ```
    
    <!--html_preserve--><div id="htmlwidget-1b17c9a675525b801798" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-1b17c9a675525b801798">{"x":{"html":"<ul>\n  <li>It is hard to erase<span class='match'> blue<\/span> or<span class='match'> red<\/span> ink.<\/li>\n  <li>The sky in the west is tinged with<span class='match'> orange<\/span><span class='match'> red<\/span>.<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
    
1.  *From the Harvard sentences data, extract:*

    1. *The first word from each sentence.*
    
    
    ```r
    str_extract(sentences, "[A-z]*")
    ```
    
    2. *All words ending in `ing`.*
    
    
    ```r
    #ends in "ing" or "ing."
    sent_ing <- str_subset(sentences, ".*ing(\\.|\\s)")  
    str_extract_all(sent_ing, "[A-z]+ing", simplify=TRUE)
    ```
    
    3. *All plurals.*

    
    ```r
    str_subset(sentences, "[A-z]*s(\\.|\\s)") %>% #take all sentences that have a word ending in s
      str_extract_all("[A-z]*s\\b", simplify = TRUE) %>%
      .[str_length(.) > 3] %>%  #get rid of the short words
      str_subset(".*[^s]s$") %>%   #get rid of words ending in 'ss'
      str_subset(".*[^i]s$")  #get rid of 'this'
    ```


### 14.4.4.1

1. *Find all words that come after a "number" like "one", "two", "three" etc.*
   *Pull out both the number and the word.*
    
    ```r
    #Create regex expression
    nums <- c("one", "two", "three", "four", "five", "six", "seven", "eight", "nine")
    
    nums_c <- str_c(nums, collapse = "|")
    
    # see stringr cheatsheet: "(?<![:alpha:])" means not preceded by
    re <- str_c("(", "(?<![:alpha:])", "(", nums_c, "))", " ", "([^ \\.]+)", 
                sep = "")
    
    sentences %>% 
      str_subset(regex(re, ignore_case = TRUE)) %>% 
      str_extract_all(regex(re, ignore_case = TRUE)) %>% 
      unlist() %>% 
      tibble::enframe(name = NULL) %>% 
      separate(col = "value", 
               into = c("num", "following"),
               remove = FALSE)
    ```
    
    ```
    ## # A tibble: 30 x 3
    ##    value       num   following
    ##    <chr>       <chr> <chr>    
    ##  1 Four hours  Four  hours    
    ##  2 Two blue    Two   blue     
    ##  3 seven books seven books    
    ##  4 two met     two   met      
    ##  5 two factors two   factors  
    ##  6 three lists three lists    
    ##  7 Two plus    Two   plus     
    ##  8 seven is    seven is       
    ##  9 two when    two   when     
    ## 10 Eight miles Eight miles    
    ## # ... with 20 more rows
    ```
    
    * I'd initially appended `"\\b"` in front of each number to prevent things like "someone" being captured -- however this didn't work with cases where a sentence started with a number -- hence switched to using the *not preceded by* method in the [stringr cheatsheet](https://www.rstudio.com/resources/cheatsheets/).

1. *Find all contractions. Separate out the pieces before and after the *
   *apostrophe.*

    
    ```r
    #note the () facilitate the split with functions
    contr <- "([^ \\.]+)'([^ \\.]*)"
    sentences %>% 
      #note the improvement this word definition is to the above [^ ]+ 
      str_subset(contr) %>% 
      str_match_all(contr)
    ```
    
    ```
    ## [[1]]
    ##      [,1]   [,2] [,3]
    ## [1,] "It's" "It" "s" 
    ## 
    ## [[2]]
    ##      [,1]    [,2]  [,3]
    ## [1,] "man's" "man" "s" 
    ## 
    ## [[3]]
    ##      [,1]    [,2]  [,3]
    ## [1,] "don't" "don" "t" 
    ## 
    ## [[4]]
    ##      [,1]      [,2]    [,3]
    ## [1,] "store's" "store" "s" 
    ## 
    ## [[5]]
    ##      [,1]        [,2]      [,3]
    ## [1,] "workmen's" "workmen" "s" 
    ## 
    ## [[6]]
    ##      [,1]    [,2]  [,3]
    ## [1,] "Let's" "Let" "s" 
    ## 
    ## [[7]]
    ##      [,1]    [,2]  [,3]
    ## [1,] "sun's" "sun" "s" 
    ## 
    ## [[8]]
    ##      [,1]      [,2]    [,3]
    ## [1,] "child's" "child" "s" 
    ## 
    ## [[9]]
    ##      [,1]     [,2]   [,3]
    ## [1,] "king's" "king" "s" 
    ## 
    ## [[10]]
    ##      [,1]   [,2] [,3]
    ## [1,] "It's" "It" "s" 
    ## 
    ## [[11]]
    ##      [,1]    [,2]  [,3]
    ## [1,] "don't" "don" "t" 
    ## 
    ## [[12]]
    ##      [,1]      [,2]    [,3]
    ## [1,] "queen's" "queen" "s" 
    ## 
    ## [[13]]
    ##      [,1]    [,2]  [,3]
    ## [1,] "don't" "don" "t" 
    ## 
    ## [[14]]
    ##      [,1]       [,2]     [,3]
    ## [1,] "pirate's" "pirate" "s" 
    ## 
    ## [[15]]
    ##      [,1]         [,2]       [,3]
    ## [1,] "neighbor's" "neighbor" "s"
    ```

### 14.4.5.1

1.  *Replace all forward slashes in a string with backslashes.*

    
    ```r
    x <- c("test/dklsk/")
    str_replace_all(x, "/", "\\\\") %>% 
      writeLines()
    ```
    
    ```
    ## test\dklsk\
    ```
    
1.  *Implement a simple version of `str_to_lower()` using `replace_all()`.*

    
    ```r
    x <- c("BIdklsKOS")
    str_replace_all(x, "([A-Z])", tolower)
    ```
    
    ```
    ## [1] "bidklskos"
    ```

1.  *Switch the first and last letters in `words`. Which of those strings*
    *are still words?*

    
    ```r
    str_replace(words, "(^.)(.*)(.$)", "\\3\\2\\1")
    ```
    
    Any words that start and end with the same letter, e.g. 'treat', as well as a few other examples like, war --> raw .

### 14.4.6.1

1.  *Split up a string like `"apples, pears, and bananas"` into individual*
    *components.*
    
    
    ```r
    x <- "apples, pears, and bananas"
    str_split(x, ",* ")  #note that regular expression works to handle commas as well
    ```
    
    ```
    ## [[1]]
    ## [1] "apples"  "pears"   "and"     "bananas"
    ```
    
    
1.  *Why is it better to split up by `boundary("word")` than `" "`?*

    Handles commas and punctuation^[I still sometimes prefer to use patterns where possible over `boundary` function. Regex is more generally applicabale as well outside of R.].
    
    ```r
    str_split(x, boundary("word"))
    ```
    
    ```
    ## [[1]]
    ## [1] "apples"  "pears"   "and"     "bananas"
    ```

1.  *What does splitting with an empty string (`""`) do? Experiment, and*
    *then read the documentation.*
    Splitting by an empty string splits up each character.
    
    ```r
    str_split(x,"")
    ```
    
    ```
    ## [[1]]
    ##  [1] "a" "p" "p" "l" "e" "s" "," " " "p" "e" "a" "r" "s" "," " " "a" "n"
    ## [18] "d" " " "b" "a" "n" "a" "n" "a" "s"
    ```
    
    * splits each character into an individual element (and creates elements for spaces between strings)

## 14.5: Other types of patterns

`regex` args to know: 

*   `ignore_case = TRUE` allows characters to match either their uppercase or 
    lowercase forms. This always uses the current locale.
*   `multiline = TRUE` allows `^` and `$` to match the start and end of each
    line rather than the start and end of the complete string.
*   `comments = TRUE` allows you to use comments and white space to make 
    complex regular expressions more understandable. Spaces are ignored, as is 
    everything after `#`. To match a literal space, you'll need to escape it: 
    `"\\ "`.
*   `dotall = TRUE` allows `.` to match everything, including `\n`. 
  
Alternatives to `regex()`:
*   `fixed()`: matches exactly the specified sequence of bytes. It ignores
    all special regular expressions and operates at a very low level. 
    This allows you to avoid complex escaping and can be much faster than 
    regular expressions.
*   `coll()`: compare strings using standard **coll**ation rules. This is 
    useful for doing case insensitive matching. Note that `coll()` takes a
    `locale` parameter that controls which rules are used for comparing
    characters.

### 14.5.1

1.  *How would you find all strings containing `\` with `regex()` vs.*
    *with `fixed()`?*
    would be `\\` instead of `\\\\`
    
    ```r
    str_view_all("so \\ the party is on\\ right?", fixed("\\"))
    ```
    
    <!--html_preserve--><div id="htmlwidget-01f5faf4a8b97574797f" style="width:960px;height:100%;" class="str_view html-widget"></div>
    <script type="application/json" data-for="htmlwidget-01f5faf4a8b97574797f">{"x":{"html":"<ul>\n  <li>so <span class='match'>\\<\/span> the party is on<span class='match'>\\<\/span> right?<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

1.  *What are the five most common words in `sentences`?*
    
    ```r
    str_extract_all(sentences, boundary("word"), simplify = TRUE) %>%
      as_tibble() %>%
      gather(V1:V12, value = "words", key = "order") %>%
      mutate(words = str_to_lower(words)) %>%
      filter(!words == "") %>%
      count(words, sort = TRUE) %>%
      head(5)
    ```
    
    ```
    ## Warning: `as_tibble.matrix()` requires a matrix with column names or a `.name_repair` argument. Using compatibility `.name_repair`.
    ## This warning is displayed once per session.
    ```
    
    ```
    ## # A tibble: 5 x 2
    ##   words     n
    ##   <chr> <int>
    ## 1 the     751
    ## 2 a       202
    ## 3 of      132
    ## 4 to      123
    ## 5 and     118
    ```

## 14.7: stringi

Other functions:

* `apropos` searches all objects available from the global environment--useful if you can't remember fun name. 

Check those that start with `replace`:

```r
apropos("^(replace)")
```

```
## [1] "replace"    "replace_na"
```

Check those that start with `str`, but not `stri`

```r
apropos("^(str)[^i]")
```

```
##  [1] "str_c"           "str_conv"        "str_count"      
##  [4] "str_detect"      "str_dup"         "str_extract"    
##  [7] "str_extract_all" "str_flatten"     "str_glue"       
## [10] "str_glue_data"   "str_interp"      "str_length"     
## [13] "str_locate"      "str_locate_all"  "str_match"      
## [16] "str_match_all"   "str_order"       "str_pad"        
## [19] "str_remove"      "str_remove_all"  "str_replace"    
## [22] "str_replace_all" "str_replace_na"  "str_sort"       
## [25] "str_split"       "str_split_fixed" "str_squish"     
## [28] "str_sub"         "str_sub<-"       "str_subset"     
## [31] "str_to_lower"    "str_to_title"    "str_to_upper"   
## [34] "str_trim"        "str_trunc"       "str_view"       
## [37] "str_view_all"    "str_which"       "str_wrap"       
## [40] "strcapture"      "strftime"        "strheight"      
## [43] "strOptions"      "strptime"        "strrep"         
## [46] "strsplit"        "strtoi"          "strtrim"        
## [49] "StructTS"        "structure"       "strwidth"       
## [52] "strwrap"
```

### 14.7.1

1.  *Find the stringi functions that:*
    1. *Count the number of words.* -- `stri_count`
    2. *Find duplicated strings.* -- `stri_duplicated`
    3. *Generate random text.* -- `str_rand_strings`

1.  *How do you control the language that `stri_sort()` uses for sorting?*
    
    The `decreasing` argument
    
## Appendix

### 14.4.2.3

One way of doing this using iteration methods:

```r
vowels <- c("a","e","i","o","u")

tibble(vowels = vowels, words = list(words)) %>% 
  mutate(detect_vowels = purrr::map2(words, vowels, str_detect)) %>% 
  spread(key = vowels, value = detect_vowels) %>% 
  unnest() %>% 
  mutate(unique_vowels = rowSums(.[2:6])) %>% 
  arrange(desc(unique_vowels))
```

```
## # A tibble: 980 x 7
##    words       a     e     i     o     u     unique_vowels
##    <chr>       <lgl> <lgl> <lgl> <lgl> <lgl>         <dbl>
##  1 absolute    TRUE  TRUE  FALSE TRUE  TRUE              4
##  2 appropriate TRUE  TRUE  TRUE  TRUE  FALSE             4
##  3 associate   TRUE  TRUE  TRUE  TRUE  FALSE             4
##  4 authority   TRUE  FALSE TRUE  TRUE  TRUE              4
##  5 colleague   TRUE  TRUE  FALSE TRUE  TRUE              4
##  6 continue    FALSE TRUE  TRUE  TRUE  TRUE              4
##  7 encourage   TRUE  TRUE  FALSE TRUE  TRUE              4
##  8 introduce   FALSE TRUE  TRUE  TRUE  TRUE              4
##  9 organize    TRUE  TRUE  TRUE  TRUE  FALSE             4
## 10 previous    FALSE TRUE  TRUE  TRUE  TRUE              4
## # ... with 970 more rows
```

```r
#seems that nothing gets over 4
```
