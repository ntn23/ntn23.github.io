# Back to Basics Part 1

I got motivated to start blogging my learning experience while doing a course on Numerical Linear Algebra by Rachel Thomas (Details [here!](https://nbviewer.jupyter.org/github/fastai/numerical-linear-algebra/blob/master/nbs/0.%20Course%20Logistics.ipynb#Writing-Assignment)). So here it is my first blog on things I learned recently (which actually should have learnt much much before).

## [The Missing Semester](https://missing.csail.mit.edu/)

As the title says it should have been the missing semester that all of us developers/computer engineers/programmers should have been taught. Practical things that make life easy on the job rather than theoretical concepts. So this was a course by a bunch of MIT students on topics including shell, shell scripting, ssh, cryptography, data wrangling among others. All in a hands on practical manner. Do check out the course if interested! 

### Data Wrangling and /usr/share/dict/words ([More Here](https://en.wikipedia.org/wiki/Words_(Unix)))

So here I was after learning some basic regex (which I was familiar with but didn't go in depth) and the use of pipe operator along with sed, trying to solve the problems. One particular problem (reproduced below) seemed complex enough (for explaining in a blog post) and simple enough to be solved by me! :relaxed:


> Find the number of words (in /usr/share/dict/words) that contain at least three as and don’t have a 's ending. What are the three most common last two letters of those words? > sed’s y command, or the tr program, may help you with case insensitivity. How many of those two-letter combinations are there? And for a challenge: which combinations do not > occur?

So let's solve it step by step

### Basics

- Pipe operator (`|`) can be used to "feed" output of one command to another. Ex: `echo "Hello World" | wc -m`. echo outputs Hello World which is used by wc (word count) to calculate length
- Regex Basics
  - `*` use for matching 0 or more repetitions
  - `.` use for matching a single character (any character)
  - `()` use these to denote capturing groups
- `sed` Stream editor
  - Takes in input explicit or through a pipe and can edit it based on criteria defined ([More here](https://tldr.ostera.io/sed))
  - `grep` essentially allows us to search for patterns in a file
  
### All words with 3 a's and no 's
  
  This is the part that took me the most time. So I arrived at below regex
  
  > `(?!.*s).*a.*a.*a.*$`
  
  - `(?!.*s)` allows us to do a negative lookahead for `.*s`. (Since we don't want words ending with `s`)
  - Rest just enforces a minimum of 3 a's anywhere in the word
  
  Doing a simple grep and word count gives us our first result! :smiley: (Note: Using ggrep which is GNU grep for macOs as it supports special characters in regex [More Here](https://stackoverflow.com/questions/9197814/regex-lookahead-for-not-followed-by-in-grep/9198987#9198987)).
  
  > `ggrep -Pio '(?!.*s).*a.*a.*a.*$' /usr/share/dict/words`
  
### Getting a count of all possible 2 letter endings

Now we can use sed to get last 2 characters for all words using pipe with the output of our previous result. `sed` works by taking a `regex` and replacing it with `replace` string

> `sed 's/regex/replace/'`

So for our purposes we need to extract (capture) last 2 characters of all words. Regex to rescue again!

> `sed -E 's/(.*)(.{2})$/\2/'`

It is essentially doing 2 things - capturing 2 groups (as defined in regex inside `()` and replacing match with second one (specified using `\2`)

Post this our job is easy we can accumulate and sort to find most common 2 character endings using `sort`, `uniq` and `head` (for top 10 results)

Final result:

> `ggrep -Pio '(?!.*s).*a.*a.*a.*$' /usr/share/dict/words | sed -E 's/(.*)(.{2})$/\2/' | sort | uniq -c | sort -r | head -n10`

Output:

 > `786 al`
 > `610 an`
 > `597 ia`
 > `529 ae`
 > `219 on`
 > `204 te`
 > `200 ly`
 > `168 ta`
 > `148 ic`
 > `146 ra`


That's all folks!
