# February 13, 2026
## I wanted to make myself sort of a grep cheat sheet with reminders of the commands we learned this week.

Basic Searching
```
grep "Chrome" operating-systems.csv
```
Search for a literal string.

### Case Sensitivity
```
grep -i "chrome" operating-systems.csv
```
Ignore case.

### Invert Matching
```
grep -v "chrome" operating-systems.csv
```
Show lines that do not match.
```
grep -vi "chrome" operating-systems.csv
```
Invert match + ignore case.

### Start and End of Line (Regex)

```
grep "^@" scopus.bib
```
Match lines that start with @.

```
grep -vi "^os" operating-systems.csv
```
Exclude lines starting with OS.

```
grep -vi "year$" operating-systems.csv
```
Exclude lines ending with year.

### Count Matches

```
grep -ic "proprietary" operating-systems.csv
```
Count matching lines (ignore case).

```
grep -vic "year$" operating-systems.csv
```
Count data rows after excluding header.

### Alternate Matching (OR)
```
grep -Ei "(bsd|atari)" operating-systems.csv
```
Match one or more alternatives.

```
grep -Ei "(bsd|gpl|apache)" operating-systems.csv
```
Multiple alternatives.

### Whole Word Matching
```
grep -i "os" operating-systems.csv
```
Matches partial words (e.g., macOS).
```
grep -iw "os" operating-systems.csv
```
Match whole word only.

### Context Lines
```
grep -i "linux" -A2 operating-systems.csv
```
Show match + 2 lines after.
```
grep -i "linux" -B2 operating-systems.csv
```
Show match + 2 lines before.
```
grep -iw -C1 "bsd" operating-systems.csv
```
Show 1 line before and after.

### Limit Matches
```
grep -i -m1 "proprietary" operating-systems.csv
```
Stop after first match.

### Line Numbers
```
grep -in "proprietary" operating-systems.csv
```
Show line numbers.

### Extended Regular Expressions
```
grep -Eiw "[a-z]{5}" operating-systems.csv
```
Match 5-letter words.
```
grep -Eiw "[0-9]{4}" operating-systems.csv
```
Match 4-digit numbers (years).
```
grep -Eiw "m.{3}s" operating-systems.csv
```
Words starting with m, ending with s.
