---
title: "Regular Expressions"
teaching: 20
exercises: 25
questions:
- How can you imagine using regular expressions in your work?
objectives:
- Use regular expressions in searches
keypoints:
- Regular expressions are a method for pattern matching
- "Test regular expressions interactively with [regex101.com](https://regex101.com/) or [RegExr.com](http://www.regexr.com/), and visualise them with [regexper.com](https://regexper.com/)."
- "Test yourself with [RegexCrossword.com/](https://regexcrossword.com/)."
---

## Regular expressions

Regular expressions are a way to match patterns in text. Think find-and-replace, but more powerful.

Lots of programming languages use regular expressions. We can also use regular expressions in a variety of software applications that may already be familiar-- Google Sheets, Oxygen XML Editor, and Notepad++ are just a few.

In computation, a string is a contiguous sequence of symbols or values. For example, a word, a date, a set of numbers (such as a phone number), or an alphanumeric value (such as an identifier), are all strings.

Regular expressions will let you:

- Match on types of characters ("upper case letters", "digits", "spaces", etc.)
- Match characters based on their position in a string (beginning of line, end of line, etc.)
- Match patterns that repeat any number of times
- Capture the parts of the original string that match your pattern


### Use case #1: tidying titles
We're compiling a spreadsheet of metadata for a collection that's going to be digitized. We're able to draw most of the information from an existing inventory for the collection, which was completed many years ago.

It looks pretty good, but we notice a few inconsistencies in the formatting of the title field, and want to standardize it according to local guidelines. For example, our local metadata style guide says that titles shouldn't end in periods, and they shouldn't be enclosed in square brackets.

Doing a simple find-and-replace on periods and square brackets in the title column would get rid of the characters we don't want. But it also might get rid of some that we do want, like periods that are used in abbreviations, not at the end of a title.

### Use case #2: legacy EAD

You recently found some previously-unknown-to-you EAD files tucked away in a folder on a shared drive. You'd like to get them ingested into your current archival management system, but you're running into a number of errors. For example, there's a tag, `<bibseries>`, that appears in several of the files, that's deprecated in current versions of EAD.

You've determined you don't need to keep the data in the `<bibseries>` field, and want to remove it. But because each instance of `<bibseries>` contains different text (like `<bibseries>Water Resources Publications</bibseries>` and `<bibseries>Leadership</bibseries>`), you need to be able to find and replace any text that is between two of the unwanted tags.


### So how does this work?

Regular expressions use both literal characters-- characters that match themselves-- and metacharacters, or special characters-- symbols that can match a range or category of characters, or have other special meanings. This lets us construct regular expressions to find text that matches a pattern, rather than a specific string.

For example, say your organization wants to change the way they display telephone numbers on their website by removing the parentheses around the area code. Rather than search for each specific phone number (that could take forever and be prone to error) or searching for every open parenthesis character (could also take forever and return many false-positives), you could design a regular expression that searches for the pattern of a phone number.

Since regular expressions define some characters as "metacharacters" that have more than their literal meaning, it is also important to be able to "escape" these metacharacters to use them for their normal, literal meaning. For example, the period (\.) is a wildcard that means "match any character", but if you want to match an actual period (\.) then you will need to use a "\" in front of it to signal to the regular expression processor that you want to use the period as a plain old period and not a metacharacter. That notation is called "escaping" the special character.

A straightforward use of a regular expression would be to locate the same word spelled two different ways. For example, the regular expression `organi[sz]e` matches both "organise" and "organize". But it would also match `reorganise`, `reorganize`, `organises`, `organizes`, `organised`, `organized`, etc.

### Learning common metacharacters
Square brackets can be used to define a list or range of characters to be found. So:

- `[ABC]` matches A or B or C
- `[A-Z]` matches any upper case letter
- `[A-Za-z]` matches any upper or lower case letter
- `[A-Za-z0-9]` matches any upper or lower case letter or any digit

Then there are:

- `.` matches any character
- `\d` matches any single digit
- `\w` matches any part of word character (equivalent to `[A-Za-z0-9]`)
- `\s` matches any space, tab, or newline
- `\` used to escape the following character when that character is a special character. So, for example, a regular expression that found `.com` would be `\.com` because `.` is a special character that matches any character.
- `^` asserts the position at the start of the line. So what you put after the caret will only match if they are the first characters of a line. The caret is also known as a circumflex.
- `$` asserts the position at the end of the line. So what you put before it will only match if they are the last characters of a line.
- `\b` adds a word boundary. Putting this either side of a word stops the regular expression matching longer variants of words. So:
	- the regular expression `foobar` will match `foobar` and find `666foobar`, `foobar777`, `8thfoobar8th` et cetera
	- the regular expression `\bfoobar` will match `foobar` and find `foobar777`
	- the regular expression `foobar\b` will match `foobar` and find `666foobar`
	- the regular expression `\bfoobar\b` will find `foobar` but not `666foobar` or `foobar777`

So, what is `^[Oo]rgani.e\b` going to match?

> ## Using special characters in regular expression matches
> What will the regular expression `^[Oo]rgani.e\b` match?
>
> > ## Solution
> > ~~~
> > organise
> > organize
> > Organise
> > Organize
> > organife
> > Organike
> > ~~~
> > Or, any other string that starts a line, begins with a letter `o` in lower or capital case, proceeds with `rgani`, has any character in the 7th position, and ends with the letter `e`.
> {: .solution}
{: .challenge}

Other useful special characters are:

- `*` matches the preceding element zero or more times. For example, ab*c matches "ac", "abc", "abbbc", etc.
- `+` matches the preceding element one or more times. For example, ab+c matches "abc", "abbbc" but not "ac".
- `?` matches when the preceding character appears zero or one time.
- `{VALUE}` matches the preceding character the number of times defined by VALUE; ranges, say, 1-6, can be specified with the syntax `{VALUE,VALUE}`, e.g. `\d{1,9}` will match any number between one and nine digits in length.
- `|` means or.
- `/i` renders an expression case-insensitive (equivalent to `[A-Za-z]`)

So, what are these going to match?

> ## ^[Oo]rgani.e\w*
> What will the regular expression `^[Oo]rgani.e\w*` match?
>
> > ## Solution
> > ~~~
> > organise
> > Organize
> > organifer
> > Organi2ed111
> > ~~~
> > Or, any other string that starts a line, begins with a letter `o` in lower or capital case, proceeds with `rgani`, has any character in the 7th position, follows with letter `e` and zero or more characters from the range `[A-Za-z0-9]`.
> {: .solution}
{: .challenge}

> ## [Oo]rgani.e\w+$
> What will the regular expression `[Oo]rgani.e\w+$` match?
>
> > ## Solution
> > ~~~
> > organiser
> > Organized
> > organifer
> > Organi2ed111
> > ~~~
> > Or, any other string that ends a line, begins with a letter `o` in lower or capital case, proceeds with `rgani`, has any character in the 7th position, follows with letter `e` and **at least one or more** characters from the range `[A-Za-z0-9]`.
> {: .solution}
{: .challenge}

> ## ^[Oo]rgani.e\w?\b
> What will the regular expression `^[Oo]rgani.e\w?\b` match?
>
> > ## Solution
> > ~~~
> > organise
> > Organized
> > organifer
> > Organi2ek
> > ~~~
> > Or, any other string that starts a line, begins with a letter `o` in lower or capital case, proceeds with `rgani`, has any character in the 7th position, follows with letter `e`, and ends with **zero or one** characters from the range `[A-Za-z0-9]`.
> {: .solution}
{: .challenge}

> ## ^[Oo]rgani.e\w?$
> What will the regular expression `^[Oo]rgani.e\w?$` match?
>
> > ## Solution
> > ~~~
> > organise
> > Organized
> > organifer
> > Organi2ek
> > ~~~
> > Or, any other string that starts and ends a line, begins with a letter `o` in lower or capital case, proceeds with `rgani`, has any character in the 7th position, follows with letter `e` and **zero or one characters** from the range `[A-Za-z0-9]`.
> {: .solution}
{: .challenge}

> ## \b[Oo]rgani.e\w{2}\b
> What will the regular expression `\b[Oo]rgani.e\w{2}\b` match?
>
> > ## Solution
> > ~~~
> > organisers
> > Organizers
> > organifers
> > Organi2ek1
> > ~~~
> > Or, any other string that begins with a letter `o` in lower or capital case after a word boundary, proceeds with `rgani`, has any character in the 7th position, follows with letter `e`, and ends with **two** characters from the range `[A-Za-z0-9]`.
> {: .solution}
{: .challenge}

> ## \b[Oo]rgani.e\b|\b[Oo]rgani.e\w{1}\b
> What will the regular expression `\b[Oo]rgani.e\b|\b[Oo]rgani.e\w{1}\b` match?
>
> > ## Solution
> > ~~~
> > organise
> > Organi1e
> > Organizer
> > organifed
> > ~~~
> > Or, any other string that begins with a letter `o` in lower or capital case after a word boundary, proceeds with `rgani`, has any character in the 7th position, and end with letter `e`, or any other string that begins with a letter `o` in lower or capital case after a word boundary, proceeds with `rgani`, has any character in the 7th position, follows with letter `e`, and ends with a single character from the range `[A-Za-z0-9]`.
> {: .solution}
{: .challenge}

### Use cases

> ## Tidying titles
> Match a period only at the end of a line.
>
> > ## Solution
> > ~~~
> > \.$
> > ~~~
> > The backslash escapes the period, matching literal periods instead of using it as a wildcard. The dollar sign matches the end of a line.
> {: .solution}
{: .challenge}

> ## Tidying titles
> Match a left square bracket only at the beginning of a line.
>
> > ## Solution
> > ~~~
> > ^\[
> > ~~~
> > The caret matches the beginning of a line. The backslash escapes the square bracket, matching literal square brackets instead of using it as a metacharacter.
> {: .solution}
{: .challenge}

> ## Tidying titles
> Match a right square bracket only at the end of a line.
>
> > ## Solution
> > ~~~
> > \]$
> > ~~~
> > The backslash escapes the square bracket, matching literal square brackets instead of using it as a metacharacter. The dollar sign matches the end of a line.
> {: .solution}
{: .challenge}

> ## Legacy EAD
> Match all text that is enclosed in a `<bibseries>` tag, as well as the tags. For example, the solution should be able to match `<bibseries>Water Resources Publications</bibseries>`, `<bibseries>324832489</bibseries>`, and `<bibseries>**% Weird Legacy Formatting %**</bibseries>`.
>
> > ## Solution
> > ~~~
> > <bibseries>.\*<\/bibseries>
> > ~~~
> > By using the `.` metacharacter (match any character) and the `*` metacharacter (previous character repeated zero or more times) together, surrounded by the `<bibseries>` tag, we can match any combination of characters that appear between those two tags. We also have to be careful to escpae the slash in the closing `</bibseries>` tag,
> {: .solution}
{: .challenge}


### More exercises

We'll cover these in the workshop if we have time, working with partners or small groups. If not, you can refer back to these after the workshop to practice on your own.

> ## Using square brackets
> What will the regular expression `Fr[ea]nc[eh]` match?
>
> > ## Solution
> > ~~~
> > French
> > France
> > Frence
> > Franch
> > ~~~
> > This will also find words where there are characters either side of the solutions above, such as `Francer`, `foobarFrench`, and `Franch911`.
> {: .solution}
{: .challenge}

> ## Using dollar signs
> What will the regular expression `Fr[ea]nc[eh]$` match?
>
> > ## Solution
> > ~~~
> > French
> > France
> > Frence
> > Franch
> > ~~~
> > This will also find strings at the end of a line. It will find words where there were characters before these, for example `foobarFrench`.
> {: .solution}
{: .challenge}

> ## Introducing options
> What would match the strings `French` and `France` that appear at the beginning of a line?
>
> > ## Solution
> > ~~~
> > ^France|^French
> > ~~~
> > This will also find words where there were characters after `French` such as `Frenchness`.
> {: .solution}
{: .challenge}

> ## Case insensitivity
> How do you match the whole words `colour` and `color` (case insensitive)?
>
> > ## Solutions
> > ~~~
> > \b[Cc]olou?r\b|\bCOLOU?R\b
> > /colou?r/i
> > ~~~
> > In real life, you *should* only come across the case insensitive variations `colour`, `color`, `Colour`, `Color`, `COLOUR`, and `COLOR` (rather than, say, `coLour`). So based on what we know, the logical regular expression is `\b[Cc]olou?r\b|\bCOLOU?R\b`. An alternative more elegant option we've not discussed is to take advantage of the `/` delimiters and add an 'ignore case' flag: so `/colou?r/i` will match all case insensitive variants of `colour` and `color`.
> {: .solution}
{: .challenge}

> ## Word boundaries
> How would you find the whole word `headrest` and or `head rest` but not `head  rest` (that is, with two spaces between `head` and `rest`?
>
> > ## Solution
> > ~~~
> > \bhead ?rest\b
> > ~~~
> > Note that although `\bhead\s?rest\b` does work, it will also match zero or one tabs or newline characters between `head` and `rest`. So again, although in most real world cases it will be fine, it isn't strictly correct.
> {: .solution}
{: .challenge}

> ## Matching non-linguistic patterns
> How would you find a string that ends with 4 letters preceded by at least one zero?
>
> > ## Solution
> > ~~~
> > 0+[A-Za-z]{4}\b
> > ~~~
> {: .solution}
{: .challenge}

> ## Matching digits
> How do you match any 4-digit string anywhere?
>
> > ## Solution
> > ~~~
> > \d{4}
> > ~~~
> > Note: this will also match 4 digit strings within longer strings of numbers and letters.
> {: .solution}
{: .challenge}

> ## Matching dates
> How would you match the date format `dd-MM-yyyy`?
>
> > ## Solution
> > ~~~
> > \b\d{2}-\d{2}-\d{4}\b
> > ~~~
> > Depending on your data, you may chose to remove the word bounding.
> {: .solution}
{: .challenge}

> ## Matching multiple date formats
> How would you match the date format `dd-MM-yyyy` or `dd-MM-yy` at the end of a line only?
>
> > ## Solution
> > ~~~
> > \d{2}-\d{2}-\d{2,4}$
> > ~~~
> > Note this will also find strings such as `31-01-198` at the end of a line, so you may wish to check your data and revise the expression to exclude false positives. Depending on your data, you may chose to add word bounding at the start of the expression.
> {: .solution}
{: .challenge}

> ## Matching publication formats
> How would you match publication formats such as `British Library : London, 2015` and `Manchester University Press: Manchester, 1999`?
>
> > ## Solution
> > ~~~
> > .* ?: .\*, \d{4}
> > ~~~
> > Without word boundaries you will find that this matches any text you put before `British` or `Manchester`. Nevertheless, the regular expression does a good job on the first look up and may be need to be refined on a second, depending on your data.
> {: .solution}
{: .challenge}
