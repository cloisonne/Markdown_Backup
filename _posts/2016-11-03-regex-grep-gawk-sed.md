---
title: 正则表达式和一些常用文本处理命令
categories:
  - Notes
tags:
  - Linux
permalink: regex_grep_gawk_sed
abbrlink: 61374
date: 2016-11-03 11:16:11
---

<h2 id="intro">前言</h2>一直懒于整理基础的一些东西，比如grep会用就好了，regex写不出可以查一下嘛，前些日子被问到如何在20万数据找到符合要求的数据，sed？egrep？原来我的基础命令水平这么差。

<!-- more -->

---------------

先给出一条有意思的命令：

``` bash
history |awk '{print $2}' |sort|uniq -c|sort -rn|head -10
```

可以看出最近你在忙什么，这是我的结果：

``` bash
    219 sudo
    151 cd
    113 hexo
    100 ls
     34 cat
     24 gcc
     21 man
     18 whereis
     14 ping
     14 ./clock
```

所以我还是一个权限控，每天sudo输这么多遍也是挺累的。

还有个命令总是记不住：`nautilus` ，用来直接打开文件管理器，真想写个alias，还是记单词吧。

```
nautilus - Bing dictionary
UK['nɔːtɪləs]

    n.鹦鹉螺
    鹦鹉螺号；鹦鹉螺属；文件管理器
```

---

### 正则表达式

一张图总结正则表达式，图太大，请点击：

[正则表达式](http://o7myibfc7.bkt.clouddn.com/034d5a7e-058f-328c-b5f4-dfad3725027e.jpg)

正则表达式30分钟入门教程（常用正则表达式）：

[外链到jb51网](http://www.jb51.net/tools/zhengze.html/)

---

### grep gawk sed

```
GREP(1)                     General Commands Manual                    GREP(1)

NAME
       grep, egrep, fgrep, rgrep - print lines matching a pattern

SYNOPSIS
       grep [OPTIONS] PATTERN [FILE...]
       grep [OPTIONS] [-e PATTERN]...  [-f FILE]...  [FILE...]

DESCRIPTION
       grep searches the named input FILEs for lines containing a match to the
       given PATTERN.  If no files are specified, or if the file “-” is given,
       grep  searches  standard  input.   By default, grep prints the matching
       lines.

       In addition, the variant programs egrep, fgrep and rgrep are  the  same
       as  grep -E,  grep -F,  and  grep -r, respectively.  These variants are
       deprecated, but are provided for backward compatibility.

OPTIONS
   Generic Program Information
       --help Output a usage message and exit.

       -V, --version
              Output the version number of grep and exit.

   Matcher Selection
       -E, --extended-regexp  // 元字符拓展集 egrep
              Interpret PATTERN as an extended regular  expression  (ERE,  see
              below).

       -F, --fixed-strings // 速度更快 简单字符串
              Interpret PATTERN as a list of fixed strings (instead of regular
              expressions), separated by newlines,  any  of  which  is  to  be
              matched.

       -G, --basic-regexp
              Interpret  PATTERN  as  a  basic  regular  expression  (BRE, see
              below).  This is the default.

       -P, --perl-regexp
              Interpret the pattern as a  Perl-compatible  regular  expression
              (PCRE).   This  is  highly  experimental and grep -P may warn of
              unimplemented features.

   Matching Control
       -e PATTERN, --regexp=PATTERN
              Use PATTERN as the pattern.  If this  option  is  used  multiple
              times or is combined with the -f (--file) option, search for all
              patterns given.  This option can be used to  protect  a  pattern
              beginning with “-”.

       -f FILE, --file=FILE
              Obtain patterns from FILE, one per line.  If this option is used
              multiple times or is combined with  the  -e  (--regexp)  option,
              search  for  all  patterns  given.  The empty file contains zero
              patterns, and therefore matches nothing.

       -i, --ignore-case // 忽略大小写
              Ignore case distinctions in  both  the  PATTERN  and  the  input
              files.

       -v, --invert-match // 反检索
              Invert the sense of matching, to select non-matching lines.

       -w, --word-regexp
              Select  only  those  lines  containing  matches  that form whole
              words.  The test is that the matching substring must  either  be
              at  the  beginning  of  the  line,  or  preceded  by  a non-word
              constituent character.  Similarly, it must be either at the  end
              of  the  line  or  followed by a non-word constituent character.
              Word-constituent  characters  are  letters,  digits,   and   the
              underscore.

       -x, --line-regexp
              Select  only  those  matches  that exactly match the whole line.
              For a regular expression pattern, this  is  like  parenthesizing
              the pattern and then surrounding it with ^ and $.

       -y     Obsolete synonym for -i.

   General Output Control
       -c, --count
              Suppress  normal output; instead print a count of matching lines
              for each input file.  With the -v,  --invert-match  option  (see
              below), count non-matching lines.

       --color[=WHEN], --colour[=WHEN]
              Surround   the  matched  (non-empty)  strings,  matching  lines,
              context lines, file  names,  line  numbers,  byte  offsets,  and
              separators  (for fields and groups of context lines) with escape
              sequences to display them in color on the terminal.  The  colors
              are  defined  by  the  environment  variable  GREP_COLORS.   The
              deprecated environment variable GREP_COLOR is  still  supported,
              but  its setting does not have priority.  WHEN is never, always,
              or auto.

       -L, --files-without-match
              Suppress normal output; instead print the  name  of  each  input
              file from which no output would normally have been printed.  The
              scanning will stop on the first match.

       -l, --files-with-matches
              Suppress normal output; instead print the  name  of  each  input
              file  from  which  output would normally have been printed.  The
              scanning will stop on the first match.

       -m NUM, --max-count=NUM
              Stop reading a file after NUM matching lines.  If the  input  is
              standard  input  from a regular file, and NUM matching lines are
              output, grep ensures that the standard input  is  positioned  to
              just  after the last matching line before exiting, regardless of
              the presence of trailing context lines.  This enables a  calling
              process  to resume a search.  When grep stops after NUM matching
              lines, it outputs any trailing context lines.  When  the  -c  or
              --count  option  is  also  used,  grep  does  not output a count
              greater than NUM.  When the -v or --invert-match option is  also
              used, grep stops after outputting NUM non-matching lines.

       -o, --only-matching
              Print  only  the  matched  (non-empty) parts of a matching line,
              with each such part on a separate output line.

       -q, --quiet, --silent
              Quiet;  do  not  write  anything  to  standard   output.    Exit
              immediately  with  zero status if any match is found, even if an
              error was detected.  Also see the -s or --no-messages option.

       -s, --no-messages
              Suppress error messages about nonexistent or unreadable files.

   Output Line Prefix Control
       -b, --byte-offset
              Print the 0-based byte offset within the input file before  each
              line of output.  If -o (--only-matching) is specified, print the
              offset of the matching part itself.

       -H, --with-filename
              Print the file name for each match.  This is  the  default  when
              there is more than one file to search.

       -h, --no-filename
              Suppress  the  prefixing  of  file names on output.  This is the
              default when there is only one file (or only standard input)  to
              search.

       --label=LABEL
              Display  input  actually  coming  from  standard  input as input
              coming  from  file  LABEL.   This  is  especially  useful   when
              implementing  tools  like  zgrep,  e.g.,  gzip -cd foo.gz | grep
              --label=foo -H something.  See also the -H option.

       -n, --line-number
              Prefix each line of output with the 1-based line  number  within
              its input file.

       -T, --initial-tab
              Make  sure  that the first character of actual line content lies
              on a tab stop, so that the alignment of tabs looks normal.  This
              is  useful  with  options that prefix their output to the actual
              content: -H,-n, and -b.  In order  to  improve  the  probability
              that lines from a single file will all start at the same column,
              this also causes the line number and byte offset (if present) to
              be printed in a minimum size field width.

       -u, --unix-byte-offsets
              Report  Unix-style  byte  offsets.   This  switch causes grep to
              report byte offsets as if the file were a Unix-style text  file,
              i.e.,  with  CR  characters  stripped  off.   This  will produce
              results identical to running  grep  on  a  Unix  machine.   This
              option  has  no  effect unless -b option is also used; it has no
              effect on platforms other than MS-DOS and MS-Windows.

       -Z, --null
              Output a zero byte (the ASCII  NUL  character)  instead  of  the
              character  that normally follows a file name.  For example, grep
              -lZ outputs a zero byte after each  file  name  instead  of  the
              usual  newline.   This option makes the output unambiguous, even
              in the presence of file names containing unusual characters like
              newlines.   This  option  can  be  used  with commands like find
              -print0, perl -0, sort -z, and xargs  -0  to  process  arbitrary
              file names, even those that contain newline characters.

   Context Line Control
       -A NUM, --after-context=NUM
              Print  NUM  lines  of  trailing  context  after  matching lines.
              Places  a  line  containing  a  group  separator  (--)   between
              contiguous  groups  of  matches.  With the -o or --only-matching
              option, this has no effect and a warning is given.

       -B NUM, --before-context=NUM
              Print NUM  lines  of  leading  context  before  matching  lines.
              Places   a  line  containing  a  group  separator  (--)  between
              contiguous groups of matches.  With the  -o  or  --only-matching
              option, this has no effect and a warning is given.

       -C NUM, -NUM, --context=NUM
              Print  NUM  lines of output context.  Places a line containing a
              group separator (--) between contiguous groups of matches.  With
              the  -o  or  --only-matching  option,  this  has no effect and a
              warning is given.

   File and Directory Selection
       -a, --text
              Process a binary file as if it were text; this is equivalent  to
              the --binary-files=text option.

       --binary-files=TYPE
              If the first few bytes of a file indicate that the file contains
              binary data, assume that the file is of type TYPE.  By  default,
              TYPE  is  binary,  and  grep  normally outputs either a one-line
              message saying that a binary file  matches,  or  no  message  if
              there  is no match.  If TYPE is without-match, grep assumes that
              a binary file does not match;  this  is  equivalent  to  the  -I
              option.   If TYPE is text, grep processes a binary file as if it
              were text; this is equivalent to the -a option.  When processing
              binary  data, grep may treat non-text bytes as line terminators;
              for example, the pattern '.' (period) might  not  match  a  null
              byte,  as  the  null byte might be treated as a line terminator.
              Warning: grep --binary-files=text might output  binary  garbage,
              which  can  have  nasty side effects if the output is a terminal
              and if the terminal driver interprets some of it as commands.

       -D ACTION, --devices=ACTION
              If an input file is a device, FIFO  or  socket,  use  ACTION  to
              process  it.   By  default,  ACTION  is  read,  which means that
              devices are read just as if they were ordinary files.  If ACTION
              is skip, devices are silently skipped.

       -d ACTION, --directories=ACTION
              If  an  input file is a directory, use ACTION to process it.  By
              default, ACTION is read, i.e., read directories just as if  they
              were   ordinary   files.   If  ACTION  is  skip,  silently  skip
              directories.  If ACTION is recurse, read all  files  under  each
              directory,  recursively,  following  symbolic links only if they
              are on the command line.  This is equivalent to the -r option.

       --exclude=GLOB
              Skip  files  whose  base  name  matches  GLOB  (using   wildcard
              matching).   A  file-name  glob  can  use  *,  ?,  and [...]  as
              wildcards, and \ to quote  a  wildcard  or  backslash  character
              literally.

       --exclude-from=FILE
              Skip  files  whose  base name matches any of the file-name globs
              read from FILE  (using  wildcard  matching  as  described  under
              --exclude).

       --exclude-dir=DIR
              Exclude  directories  matching  the  pattern  DIR from recursive
              searches.

       -I     Process a binary file as if it did not  contain  matching  data;
              this is equivalent to the --binary-files=without-match option.

       --include=GLOB
              Search  only  files whose base name matches GLOB (using wildcard
              matching as described under --exclude).

       -r, --recursive
              Read all files  under  each  directory,  recursively,  following
              symbolic  links only if they are on the command line.  Note that
              if  no  file  operand  is  given,  grep  searches  the   working
              directory.  This is equivalent to the -d recurse option.

       -R, --dereference-recursive
              Read  all  files  under each directory, recursively.  Follow all
              symbolic links, unlike -r.

   Other Options
       --line-buffered
              Use line buffering on output.   This  can  cause  a  performance
              penalty.

       -U, --binary
              Treat  the  file(s) as binary.  By default, under MS-DOS and MS-
              Windows, grep guesses the file type by looking at  the  contents
              of  the first 32KB read from the file.  If grep decides the file
              is a text file, it strips the CR characters  from  the  original
              file  contents  (to  make  regular expressions with ^ and $ work
              correctly).  Specifying -U overrules this guesswork, causing all
              files  to be read and passed to the matching mechanism verbatim;
              if the file is a text file with CR/LF pairs at the end  of  each
              line,  this  will  cause some regular expressions to fail.  This
              option has no effect on platforms  other  than  MS-DOS  and  MS-
              Windows.

       -z, --null-data
              Treat  the  input  as  a set of lines, each terminated by a zero
              byte (the ASCII NUL character) instead of a newline.   Like  the
              -Z  or --null option, this option can be used with commands like
              sort -z to process arbitrary file names.

REGULAR EXPRESSIONS
       A regular expression is a pattern that  describes  a  set  of  strings.
       Regular   expressions   are   constructed   analogously  to  arithmetic
       expressions, by using various operators to combine smaller expressions.

       grep understands three different versions of regular expression syntax:
       “basic”  (BRE), “extended” (ERE) and “perl” (PCRE).  In GNU grep, there
       is no difference in available functionality between basic and  extended
       syntaxes.  In other implementations, basic regular expressions are less
       powerful.   The  following  description  applies  to  extended  regular
       expressions;  differences  for basic regular expressions are summarized
       afterwards.   Perl-compatible  regular  expressions   give   additional
       functionality,  and are documented in pcresyntax(3) and pcrepattern(3),
       but work only if PCRE is available in the system.

       The fundamental building blocks are the regular expressions that  match
       a single character.  Most characters, including all letters and digits,
       are regular expressions that match themselves.  Any meta-character with
       special meaning may be quoted by preceding it with a backslash.

       The period . matches any single character.

   Character Classes and Bracket Expressions
       A  bracket  expression is a list of characters enclosed by [ and ].  It
       matches any single character in that list; if the  first  character  of
       the  list is the caret ^ then it matches any character not in the list.
       For example, the regular expression  [0123456789]  matches  any  single
       digit.

       Within  a  bracket  expression,  a  range  expression  consists  of two
       characters separated by a hyphen.  It matches any single character that
       sorts  between  the  two  characters,  inclusive,  using  the  locale's
       collating sequence and character set.  For example, in  the  default  C
       locale, [a-d] is equivalent to [abcd].  Many locales sort characters in
       dictionary  order,  and  in  these  locales  [a-d]  is  typically   not
       equivalent to [abcd]; it might be equivalent to [aBbCcDd], for example.
       To obtain the traditional interpretation of  bracket  expressions,  you
       can  use the C locale by setting the LC_ALL environment variable to the
       value C.

       Finally, certain named classes  of  characters  are  predefined  within
       bracket expressions, as follows.  Their names are self explanatory, and
       they  are  [:alnum:],  [:alpha:],  [:cntrl:],   [:digit:],   [:graph:],
       [:lower:],  [:print:], [:punct:], [:space:], [:upper:], and [:xdigit:].
       For example, [[:alnum:]] means  the  character  class  of  numbers  and
       letters  in the current locale. In the C locale and ASCII character set
       encoding, this is the same as [0-9A-Za-z].  (Note that the brackets  in
       these  class names are part of the symbolic names, and must be included
       in addition to the brackets delimiting the bracket  expression.)   Most
       meta-characters  lose their special meaning inside bracket expressions.
       To include a literal ] place it  first  in  the  list.   Similarly,  to
       include a literal ^ place it anywhere but first.  Finally, to include a
       literal - place it last.

   Anchoring
       The caret ^ and the dollar sign $ are meta-characters that respectively
       match the empty string at the beginning and end of a line.

   The Backslash Character and Special Expressions
       The  symbols  \<  and  \>  respectively  match  the empty string at the
       beginning and end of a word.  The symbol \b matches the empty string at
       the  edge  of a word, and \B matches the empty string provided it's not
       at the edge of a word.  The symbol \w is a synonym for [_[:alnum:]] and
       \W is a synonym for [^_[:alnum:]].

   Repetition
       A  regular  expression  may  be  followed  by one of several repetition
       operators:
       ?      The preceding item is optional and matched at most once.
       *      The preceding item will be matched zero or more times.
       +      The preceding item will be matched one or more times.
       {n}    The preceding item is matched exactly n times.
       {n,}   The preceding item is matched n or more times.
       {,m}   The preceding item is matched at most m times.  This  is  a  GNU
              extension.
       {n,m}  The  preceding  item  is  matched at least n times, but not more
              than m times.

   Concatenation
       Two regular expressions may  be  concatenated;  the  resulting  regular
       expression  matches  any  string formed by concatenating two substrings
       that respectively match the concatenated expressions.

   Alternation
       Two regular expressions may be joined by  the  infix  operator  |;  the
       resulting   regular  expression  matches  any  string  matching  either
       alternate expression.

   Precedence
       Repetition takes precedence over concatenation,  which  in  turn  takes
       precedence  over  alternation.   A  whole expression may be enclosed in
       parentheses  to  override   these   precedence   rules   and   form   a
       subexpression.

   Back References and Subexpressions
       The back-reference \n, where n is a single digit, matches the substring
       previously matched  by  the  nth  parenthesized  subexpression  of  the
       regular expression.

   Basic vs Extended Regular Expressions
       In  basic  regular expressions the meta-characters ?, +, {, |, (, and )
       lose their special meaning; instead use the  backslashed  versions  \?,
       \+, \{, \|, \(, and \).

ENVIRONMENT VARIABLES
       The   behavior  of  grep  is  affected  by  the  following  environment
       variables.

       The locale for category LC_foo is  specified  by  examining  the  three
       environment  variables  LC_ALL, LC_foo, LANG, in that order.  The first
       of these variables that is set specifies the locale.  For  example,  if
       LC_ALL  is not set, but LC_MESSAGES is set to pt_BR, then the Brazilian
       Portuguese locale is used for the LC_MESSAGES category.  The  C  locale
       is  used  if none of these environment variables are set, if the locale
       catalog is not installed, or if grep was  not  compiled  with  national
       language support (NLS).

       GREP_OPTIONS
              This variable specifies default options to be placed in front of
              any explicit options.  As  this  causes  problems  when  writing
              portable  scripts,  this  feature  will  be  removed in a future
              release of grep, and grep warns if it is used.   Please  use  an
              alias or script instead.

       GREP_COLOR
              This  variable  specifies  the  color  used to highlight matched
              (non-empty) text.  It is deprecated in favor of GREP_COLORS, but
              still supported.  The mt, ms, and mc capabilities of GREP_COLORS
              have priority over it.  It can only specify the  color  used  to
              highlight  the  matching  non-empty text in any matching line (a
              selected line when the -v command-line option is omitted,  or  a
              context line when -v is specified).  The default is 01;31, which
              means a bold red  foreground  text  on  the  terminal's  default
              background.

       GREP_COLORS
              Specifies  the  colors  and  other  attributes used to highlight
              various parts of the output.  Its  value  is  a  colon-separated
              list       of       capabilities      that      defaults      to
              ms=01;31:mc=01;31:sl=:cx=:fn=35:ln=32:bn=32:se=36  with  the  rv
              and  ne  boolean  capabilities omitted (i.e., false).  Supported
              capabilities are as follows.

              sl=    SGR substring for whole selected  lines  (i.e.,  matching
                     lines when the -v command-line option is omitted, or non-
                     matching lines when -v is  specified).   If  however  the
                     boolean  rv capability and the -v command-line option are
                     both specified, it  applies  to  context  matching  lines
                     instead.   The  default  is  empty  (i.e., the terminal's
                     default color pair).

              cx=    SGR substring for whole context lines (i.e., non-matching
                     lines  when  the  -v  command-line  option is omitted, or
                     matching lines when -v is  specified).   If  however  the
                     boolean  rv capability and the -v command-line option are
                     both specified, it applies to selected non-matching lines
                     instead.   The  default  is  empty  (i.e., the terminal's
                     default color pair).

              rv     Boolean value that reverses (swaps) the meanings  of  the
                     sl=  and cx= capabilities when the -v command-line option
                     is specified.  The default is false (i.e., the capability
                     is omitted).

              mt=01;31
                     SGR substring for matching non-empty text in any matching
                     line (i.e., a selected  line  when  the  -v  command-line
                     option   is  omitted,  or  a  context  line  when  -v  is
                     specified).  Setting this is equivalent to  setting  both
                     ms=  and mc= at once to the same value.  The default is a
                     bold  red  text  foreground   over   the   current   line
                     background.

              ms=01;31
                     SGR  substring  for matching non-empty text in a selected
                     line.  (This is only used when the -v command-line option
                     is  omitted.)   The  effect  of  the  sl=  (or cx= if rv)
                     capability  remains  active  when  this  kicks  in.   The
                     default  is  a  bold red text foreground over the current
                     line background.

              mc=01;31
                     SGR substring for matching non-empty text  in  a  context
                     line.  (This is only used when the -v command-line option
                     is specified.)  The effect of the  cx=  (or  sl=  if  rv)
                     capability  remains  active  when  this  kicks  in.   The
                     default is a bold red text foreground  over  the  current
                     line background.

              fn=35  SGR  substring for file names prefixing any content line.
                     The  default  is  a  magenta  text  foreground  over  the
                     terminal's default background.

              ln=32  SGR  substring  for  line  numbers  prefixing any content
                     line.  The default is a green text  foreground  over  the
                     terminal's default background.

              bn=32  SGR  substring  for  byte  offsets  prefixing any content
                     line.  The default is a green text  foreground  over  the
                     terminal's default background.

              se=36  SGR  substring  for  separators that are inserted between
                     selected line fields (:), between  context  line  fields,
                     (-),  and  between  groups of adjacent lines when nonzero
                     context is specified (--).  The default is  a  cyan  text
                     foreground over the terminal's default background.

              ne     Boolean  value  that prevents clearing to the end of line
                     using Erase in Line (EL) to Right  (\33[K)  each  time  a
                     colorized  item  ends.   This  is  needed on terminals on
                     which EL is not supported.  It  is  otherwise  useful  on
                     terminals  for  which  the back_color_erase (bce) boolean
                     terminfo capability  does  not  apply,  when  the  chosen
                     highlight colors do not affect the background, or when EL
                     is too slow or causes too much flicker.  The  default  is
                     false (i.e., the capability is omitted).

              Note  that  boolean  capabilities  have no =...  part.  They are
              omitted (i.e., false) by default and become true when specified.

              See  the  Select  Graphic  Rendition  (SGR)   section   in   the
              documentation  of  the  text terminal that is used for permitted
              values  and  their  meaning  as  character  attributes.    These
              substring  values are integers in decimal representation and can
              be concatenated with semicolons.  grep takes care of  assembling
              the  result  into  a  complete  SGR sequence (\33[...m).  Common
              values to concatenate include 1 for bold, 4 for underline, 5 for
              blink,  7 for inverse, 39 for default foreground color, 30 to 37
              for foreground colors, 90 to 97  for  16-color  mode  foreground
              colors,  38;5;0  to  38;5;255  for  88-color and 256-color modes
              foreground colors, 49 for default background color, 40 to 47 for
              background  colors,  100  to  107  for  16-color mode background
              colors, and 48;5;0 to 48;5;255 for 88-color and 256-color  modes
              background colors.

       LC_ALL, LC_COLLATE, LANG
              These  variables specify the locale for the LC_COLLATE category,
              which determines the collating sequence used to interpret  range
              expressions like [a-z].

       LC_ALL, LC_CTYPE, LANG
              These  variables  specify  the locale for the LC_CTYPE category,
              which determines the type of characters, e.g., which  characters
              are whitespace.

       LC_ALL, LC_MESSAGES, LANG
              These variables specify the locale for the LC_MESSAGES category,
              which determines the language that grep uses for messages.   The
              default C locale uses American English messages.

       POSIXLY_CORRECT
              If  set, grep behaves as POSIX requires; otherwise, grep behaves
              more like other GNU programs.  POSIX requires that options  that
              follow  file  names  must  be treated as file names; by default,
              such options are permuted to the front of the operand  list  and
              are  treated as options.  Also, POSIX requires that unrecognized
              options be diagnosed as “illegal”, but since they are not really
              against  the  law  the default is to diagnose them as “invalid”.
              POSIXLY_CORRECT  also   disables   _N_GNU_nonoption_argv_flags_,
              described below.

       _N_GNU_nonoption_argv_flags_
              (Here  N is grep's numeric process ID.)  If the ith character of
              this environment variable's value is 1, do not consider the  ith
              operand  of  grep to be an option, even if it appears to be one.
              A shell can put  this  variable  in  the  environment  for  each
              command  it  runs,  specifying which operands are the results of
              file name wildcard expansion and therefore should not be treated
              as  options.   This  behavior  is  available only with the GNU C
              library, and only when POSIXLY_CORRECT is not set.

EXIT STATUS
       Normally the exit status is 0 if a line is selected, 1 if no lines were
       selected, and 2 if an error occurred.  However, if the -q or --quiet or
       --silent is used and a line is selected, the exit status is 0  even  if
       an error occurred.

COPYRIGHT
       Copyright 1998-2000, 2002, 2005-2016 Free Software Foundation, Inc.

       This is free software; see the source for copying conditions.  There is
       NO warranty; not even for MERCHANTABILITY or FITNESS FOR  A  PARTICULAR
       PURPOSE.

BUGS
   Reporting Bugs
       Email bug reports to the bug-reporting address ⟨bug-grep@gnu.org⟩.  An
       email archive ⟨http://lists.gnu.org/mailman/listinfo/bug-grep⟩ and a
       bug tracker ⟨http://debbugs.gnu.org/cgi/pkgreport.cgi?package=grep⟩ are
       available.

   Known Bugs
       Large repetition counts in the {n,m} construct may cause  grep  to  use
       lots of memory.  In addition, certain other obscure regular expressions
       require exponential time and space, and may cause grep to  run  out  of
       memory.

       Back-references are very slow, and may require exponential time.

SEE ALSO
   Regular Manual Pages
       awk(1),  cmp(1),  diff(1),  find(1), gzip(1), perl(1), sed(1), sort(1),
       xargs(1), zgrep(1), read(2),  pcre(3),  pcresyntax(3),  pcrepattern(3),
       terminfo(5), glob(7), regex(7).

   POSIX Programmer's Manual Page
       grep(1p).

   Full Documentation
       A   complete   manual   ⟨http://www.gnu.org/software/grep/manual/⟩   is
       available.  If the info and grep programs  are  properly  installed  at
       your site, the command

              info grep

       should give you access to the complete manual.

NOTES
       This  man  page  is maintained only fitfully; the full documentation is
       often more up-to-date.

User Commands                    GNU grep 2.25                         GREP(1)
```

---


```
GAWK(1)                        Utility Commands                        GAWK(1)

NAME
       gawk - pattern scanning and processing language

SYNOPSIS
       gawk [ POSIX or GNU style options ] -f program-file [ -- ] file ...
       gawk [ POSIX or GNU style options ] [ -- ] program-text file ...

DESCRIPTION
       Gawk  is  the  GNU Project's implementation of the AWK programming lan‐
       guage.  It conforms to the definition of  the  language  in  the  POSIX
       1003.1  Standard.   This version in turn is based on the description in
       The AWK Programming Language, by Aho, Kernighan, and Weinberger.   Gawk
       provides  the additional features found in the current version of Brian
       Kernighan's awk and a number of GNU-specific extensions.

       The command line consists of options to gawk itself,  the  AWK  program
       text  (if  not supplied via the -f or --file options), and values to be
       made available in the ARGC and ARGV pre-defined AWK variables.

       When gawk is invoked with the --profile  option,  it  starts  gathering
       profiling statistics from the execution of the program.  Gawk runs more
       slowly in this mode, and automatically produces an execution profile in
       the file awkprof.out when done.  See the --profile option, below.

       Gawk  also has an integrated debugger. An interactive debugging session
       can be started by supplying the --debug option to the command line.  In
       this mode of execution, gawk loads the AWK source code and then prompts
       for debugging commands.  Gawk can only debug AWK  program  source  pro‐
       vided  with  the -f option.  The debugger is documented in GAWK: Effec‐
       tive AWK Programming.

OPTION FORMAT
       Gawk options may be either traditional POSIX-style one letter  options,
       or  GNU-style  long  options.   POSIX  options start with a single “-”,
       while long options start with “--”.  Long options are provided for both
       GNU-specific features and for POSIX-mandated features.

       Gawk-specific  options  are  typically used in long-option form.  Argu‐
       ments to long options are either joined with the option by an  =  sign,
       with no intervening spaces, or they may be provided in the next command
       line argument.  Long options may be abbreviated, as long as the  abbre‐
       viation remains unique.

       Additionally,  every  long  option has a corresponding short option, so
       that the option's functionality may be used from within #!   executable
       scripts.

OPTIONS
       Gawk accepts the following options.  Standard options are listed first,
       followed by options for gawk extensions, listed alphabetically by short
       option.

       -f program-file
       --file program-file
              Read  the AWK program source from the file program-file, instead
              of from the  first  command  line  argument.   Multiple  -f  (or
              --file) options may be used.

       -F fs
       --field-separator fs
              Use fs for the input field separator (the value of the FS prede‐
              fined variable).

       -v var=val
       --assign var=val
              Assign the value val to the variable var,  before  execution  of
              the  program  begins.  Such variable values are available to the
              BEGIN rule of an AWK program.

       -b
       --characters-as-bytes
              Treat all input data as single-byte characters. In other  words,
              don't  pay any attention to the locale information when attempt‐
              ing to process strings as  multibyte  characters.   The  --posix
              option overrides this one.

       -c
       --traditional
              Run  in compatibility mode.  In compatibility mode, gawk behaves
              identically to Brian Kernighan's awk; none of  the  GNU-specific
              extensions  are recognized.  See GNU EXTENSIONS, below, for more
              information.

       -C
       --copyright
              Print the short version of the GNU copyright information message
              on the standard output and exit successfully.

       -d[file]
       --dump-variables[=file]
              Print  a  sorted list of global variables, their types and final
              values to file.  If no file is provided, gawk uses a file  named
              awkvars.out in the current directory.
              Having  a list of all the global variables is a good way to look
              for typographical errors in your programs.  You would  also  use
              this option if you have a large program with a lot of functions,
              and you want to be sure that your functions don't  inadvertently
              use  global  variables  that  you meant to be local.  (This is a
              particularly easy mistake to make  with  simple  variable  names
              like i, j, and so on.)

       -D[file]
       --debug[=file]
              Enable  debugging  of  AWK  programs.   By default, the debugger
              reads commands interactively from the keyboard (standard input).
              The  optional file argument specifies a file with a list of com‐
              mands for the debugger to execute non-interactively.

       -e program-text
       --source program-text
              Use program-text as AWK program source code.  This option allows
              the  easy  intermixing of library functions (used via the -f and
              --file options) with source code entered on  the  command  line.
              It  is  intended primarily for medium to large AWK programs used
              in shell scripts.

       -E file
       --exec file
              Similar to -f, however, this is option  is  the  last  one  pro‐
              cessed.   This should be used with #!  scripts, particularly for
              CGI applications, to avoid passing in options or source code (!)
              on  the  command line from a URL.  This option disables command-
              line variable assignments.

       -g
       --gen-pot
              Scan and parse the AWK program, and generate a GNU .pot  (Porta‐
              ble Object Template) format file on standard output with entries
              for all localizable strings in the program.  The program  itself
              is  not  executed.   See  the  GNU gettext distribution for more
              information on .pot files.

       -h
       --help Print a relatively short summary of the available options on the
              standard  output.   (Per the GNU Coding Standards, these options
              cause an immediate, successful exit.)

       -i include-file
       --include include-file
              Load an awk source library.  This searches for the library using
              the  AWKPATH environment variable.  If the initial search fails,
              another attempt will be made after appending  the  .awk  suffix.
              The  file  will be loaded only once (i.e., duplicates are elimi‐
              nated), and the  code  does  not  constitute  the  main  program
              source.

       -l lib
       --load lib
              Load  a shared library lib.  This searches for the library using
              the AWKLIBPATH environment  variable.   If  the  initial  search
              fails,  another attempt will be made after appending the default
              shared library suffix for the platform.  The library initializa‐
              tion routine is expected to be named dl_load().

       -L [value]
       --lint[=value]
              Provide warnings about constructs that are dubious or non-porta‐
              ble to other AWK implementations.  With an optional argument  of
              fatal,  lint warnings become fatal errors.  This may be drastic,
              but its use will certainly encourage the development of  cleaner
              AWK  programs.  With an optional argument of invalid, only warn‐
              ings about things that are actually invalid are issued. (This is
              not fully implemented yet.)

       -M
       --bignum
              Force arbitrary precision arithmetic on numbers. This option has
              no effect if gawk is not compiled to use the  GNU  MPFR  and  MP
              libraries.

       -n
       --non-decimal-data
              Recognize  octal and hexadecimal values in input data.  Use this
              option with great caution!

       -N
       --use-lc-numeric
              This forces gawk to use the  locale's  decimal  point  character
              when  parsing  input data.  Although the POSIX standard requires
              this behavior, and gawk does so when --posix is in  effect,  the
              default  is  to  follow traditional behavior and use a period as
              the decimal point, even in locales where the period is  not  the
              decimal  point  character.   This  option  overrides the default
              behavior, without the full draconian strictness of  the  --posix
              option.

       -o[file]
       --pretty-print[=file]
              Output  a  pretty printed version of the program to file.  If no
              file is provided, gawk uses a file named awkprof.out in the cur‐
              rent directory.

       -O
       --optimize
              Enable  optimizations  upon  the  internal representation of the
              program.  Currently, this includes simple constant-folding,  and
              tail  call  elimination  for recursive functions. The gawk main‐
              tainer hopes to add additional optimizations over time.

       -p[prof-file]
       --profile[=prof-file]
              Start a profiling session, and send the profiling data to  prof-
              file.   The default is awkprof.out.  The profile contains execu‐
              tion counts of each statement in the program in the left  margin
              and function call counts for each user-defined function.

       -P
       --posix
              This  turns on compatibility mode, with the following additional
              restrictions:

              · \x escape sequences are not recognized.

              · Only space and tab act as field separators when FS is set to a
                single space, newline does not.

              · You cannot continue lines after ?  and :.

              · The synonym func for the keyword function is not recognized.

              · The operators ** and **= cannot be used in place of ^ and ^=.

       -r
       --re-interval
              Enable  the  use  of  interval expressions in regular expression
              matching (see Regular Expressions, below).  Interval expressions
              were not traditionally available in the AWK language.  The POSIX
              standard added them, to make awk and egrep consistent with  each
              other.  They are enabled by default, but this option remains for
              use with --traditional.

       -S
       --sandbox
              Runs gawk in sandbox  mode,  disabling  the  system()  function,
              input  redirection  with  getline, output redirection with print
              and printf, and loading dynamic extensions.   Command  execution
              (through pipelines) is also disabled.  This effectively blocks a
              script from accessing local  resources  (except  for  the  files
              specified on the command line).

       -t
       --lint-old
              Provide  warnings  about constructs that are not portable to the
              original version of UNIX awk.

       -V
       --version
              Print version information for this particular copy  of  gawk  on
              the  standard  output.  This is useful mainly for knowing if the
              current copy of gawk on your system is up to date  with  respect
              to  whatever the Free Software Foundation is distributing.  This
              is also useful when reporting bugs.  (Per the GNU  Coding  Stan‐
              dards, these options cause an immediate, successful exit.)

       --     Signal the end of options. This is useful to allow further argu‐
              ments to the AWK program itself to start with a “-”.  This  pro‐
              vides  consistency  with the argument parsing convention used by
              most other POSIX programs.

       In compatibility mode, any other options are flagged  as  invalid,  but
       are  otherwise  ignored.   In normal operation, as long as program text
       has been supplied, unknown options are passed on to the AWK program  in
       the ARGV array for processing.  This is particularly useful for running
       AWK programs via the “#!” executable interpreter mechanism.

       For POSIX compatibility, the -W option may be  used,  followed  by  the
       name of a long option.

AWK PROGRAM EXECUTION
       An  AWK program consists of a sequence of pattern-action statements and
       optional function definitions.

              @include "filename"
              @load "filename"
              pattern   { action statements }
              function name(parameter list) { statements }

       Gawk first reads the program source from the program-file(s) if  speci‐
       fied, from arguments to --source, or from the first non-option argument
       on the command line.  The -f and --source options may be used  multiple
       times  on  the command line.  Gawk reads the program text as if all the
       program-files and command  line  source  texts  had  been  concatenated
       together.   This  is  useful  for  building libraries of AWK functions,
       without having to include them in each new AWK program that uses  them.
       It also provides the ability to mix library functions with command line
       programs.

       In addition, lines beginning with @include may be used to include other
       source  files  into your program, making library use even easier.  This
       is equivalent to using the -i option.

       Lines beginning with @load may be used to load  shared  libraries  into
       your program.  This is equivalent to using the -l option.

       The  environment  variable  AWKPATH specifies a search path to use when
       finding source files named with the -f and -i options.  If  this  vari‐
       able  does  not  exist,  the  default path is ".:/usr/local/share/awk".
       (The actual directory may vary, depending upon how gawk was  built  and
       installed.)  If a file name given to the -f option contains a “/” char‐
       acter, no path search is performed.

       The environment variable AWKLIBPATH specifies a search path to use when
       finding  source  files named with the -l option.  If this variable does
       not exist, the default  path  is  "/usr/local/lib/gawk".   (The  actual
       directory may vary, depending upon how gawk was built and installed.)

       Gawk executes AWK programs in the following order.  First, all variable
       assignments specified via the -v option are performed.  Next, gawk com‐
       piles  the program into an internal form.  Then, gawk executes the code
       in the BEGIN rule(s) (if any), and then  proceeds  to  read  each  file
       named  in  the  ARGV  array  (up to ARGV[ARGC]).  If there are no files
       named on the command line, gawk reads the standard input.

       If a filename on the command line has the form var=val it is treated as
       a  variable  assignment.   The  variable var will be assigned the value
       val.  (This happens after any BEGIN rule(s) have  been  run.)   Command
       line  variable assignment is most useful for dynamically assigning val‐
       ues to the variables AWK uses to  control  how  input  is  broken  into
       fields  and records.  It is also useful for controlling state if multi‐
       ple passes are needed over a single data file.

       If the value of a particular element of ARGV is empty (""), gawk  skips
       over it.

       For  each  input  file,  if  a BEGINFILE rule exists, gawk executes the
       associated code before processing the contents of the file.  Similarly,
       gawk  executes  the  code  associated with ENDFILE after processing the
       file.

       For each record in the input, gawk tests to see if it matches any  pat‐
       tern  in  the  AWK  program.  For each pattern that the record matches,
       gawk executes the associated action.  The patterns are  tested  in  the
       order they occur in the program.

       Finally,  after  all  the input is exhausted, gawk executes the code in
       the END rule(s) (if any).

   Command Line Directories
       According to POSIX, files named on the awk command line  must  be  text
       files.   The  behavior is ``undefined'' if they are not.  Most versions
       of awk treat a directory on the command line as a fatal error.

       Starting with version 4.0 of gawk, a directory on the command line pro‐
       duces a warning, but is otherwise skipped.  If either of the --posix or
       --traditional options is given, then gawk reverts to treating  directo‐
       ries on the command line as a fatal error.

VARIABLES, RECORDS AND FIELDS
       AWK variables are dynamic; they come into existence when they are first
       used.  Their values are either floating-point numbers  or  strings,  or
       both,  depending  upon how they are used.  AWK also has one dimensional
       arrays; arrays with multiple dimensions may be  simulated.   Gawk  pro‐
       vides  true  arrays  of arrays; see Arrays, below.  Several pre-defined
       variables are set as a program runs; these are described as needed  and
       summarized below.

   Records
       Normally, records are separated by newline characters.  You can control
       how records are separated by assigning values to the built-in  variable
       RS.   If  RS is any single character, that character separates records.
       Otherwise, RS is a regular expression.  Text in the input that  matches
       this  regular expression separates the record.  However, in compatibil‐
       ity mode, only the first character of its string value is used for sep‐
       arating  records.   If  RS  is set to the null string, then records are
       separated by blank lines.  When RS is set to the null string, the  new‐
       line  character  always acts as a field separator, in addition to what‐
       ever value FS may have.

   Fields
       As each input record is read, gawk splits the record into fields, using
       the value of the FS variable as the field separator.  If FS is a single
       character, fields are separated by that character.  If FS is  the  null
       string,  then each individual character becomes a separate field.  Oth‐
       erwise, FS is expected to be a full regular expression.  In the special
       case  that FS is a single space, fields are separated by runs of spaces
       and/or tabs and/or newlines.  (But see the section POSIX COMPATIBILITY,
       below).   NOTE:  The  value  of IGNORECASE (see below) also affects how
       fields are split when FS is a regular expression, and how  records  are
       separated when RS is a regular expression.

       If  the  FIELDWIDTHS  variable is set to a space separated list of num‐
       bers, each field is expected to have fixed width, and  gawk  splits  up
       the  record  using  the  specified widths.  The value of FS is ignored.
       Assigning a new value to FS or FPAT overrides the use of FIELDWIDTHS.

       Similarly, if the FPAT variable is set to a string representing a regu‐
       lar expression, each field is made up of text that matches that regular
       expression. In this case, the regular expression describes  the  fields
       themselves, instead of the text that separates the fields.  Assigning a
       new value to FS or FIELDWIDTHS overrides the use of FPAT.

       Each field in the input record may be referenced by its  position:  $1,
       $2,  and so on.  $0 is the whole record.  Fields need not be referenced
       by constants:

              n = 5
              print $n

       prints the fifth field in the input record.

       The variable NF is set to the total  number  of  fields  in  the  input
       record.

       References  to non-existent fields (i.e., fields after $NF) produce the
       null-string.  However, assigning to a non-existent field (e.g., $(NF+2)
       = 5) increases the value of NF, creates any intervening fields with the
       null string as their values, and causes the value of $0  to  be  recom‐
       puted, with the fields being separated by the value of OFS.  References
       to negative numbered fields  cause  a  fatal  error.   Decrementing  NF
       causes  the  values  of  fields  past the new value to be lost, and the
       value of $0 to be recomputed, with the fields being  separated  by  the
       value of OFS.

       Assigning  a  value  to an existing field causes the whole record to be
       rebuilt when $0 is referenced.  Similarly,  assigning  a  value  to  $0
       causes the record to be resplit, creating new values for the fields.

   Built-in Variables
       Gawk's built-in variables are:

       ARGC        The  number  of  command  line  arguments (does not include
                   options to gawk, or the program source).

       ARGIND      The index in ARGV of the current file being processed.

       ARGV        Array of command line arguments.  The array is indexed from
                   0  to  ARGC - 1.  Dynamically changing the contents of ARGV
                   can control the files used for data.

       BINMODE     On non-POSIX systems, specifies use of  “binary”  mode  for
                   all  file  I/O.  Numeric values of 1, 2, or 3, specify that
                   input files, output  files,  or  all  files,  respectively,
                   should  use binary I/O.  String values of "r", or "w" spec‐
                   ify that input files, or output files, respectively, should
                   use binary I/O.  String values of "rw" or "wr" specify that
                   all files should use binary I/O.  Any other string value is
                   treated as "rw", but generates a warning message.

       CONVFMT     The conversion format for numbers, "%.6g", by default.

       ENVIRON     An  array containing the values of the current environment.
                   The array is indexed by  the  environment  variables,  each
                   element  being  the  value  of  that  variable (e.g., ENVI‐
                   RON["HOME"] might be "/home/arnold").  Changing this  array
                   does not affect the environment seen by programs which gawk
                   spawns via redirection or the system() function.

       ERRNO       If a system error occurs either  doing  a  redirection  for
                   getline,  during  a  read for getline, or during a close(),
                   then ERRNO will contain a string describing the error.  The
                   value is subject to translation in non-English locales.

       FIELDWIDTHS A  whitespace  separated  list  of field widths.  When set,
                   gawk parses the input into fields of fixed  width,  instead
                   of  using the value of the FS variable as the field separa‐
                   tor.  See Fields, above.

       FILENAME    The name of the current input file.  If no files are speci‐
                   fied  on  the  command  line, the value of FILENAME is “-”.
                   However,  FILENAME  is  undefined  inside  the  BEGIN  rule
                   (unless set by getline).

       FNR         The input record number in the current input file.

       FPAT        A  regular expression describing the contents of the fields
                   in a record.  When set, gawk parses the input into  fields,
                   where  the  fields match the regular expression, instead of
                   using the value of the FS variable as the field  separator.
                   See Fields, above.

       FS          The input field separator, a space by default.  See Fields,
                   above.

       FUNCTAB     An array whose indices and  corresponding  values  are  the
                   names of all the user-defined or extension functions in the
                   program.  NOTE: You may not use the delete  statement  with
                   the FUNCTAB array.

       IGNORECASE  Controls the case-sensitivity of all regular expression and
                   string operations.  If IGNORECASE  has  a  non-zero  value,
                   then  string  comparisons  and  pattern  matching in rules,
                   field splitting with FS and FPAT,  record  separating  with
                   RS, regular expression matching with ~ and !~, and the gen‐
                   sub(), gsub(), index(), match(), patsplit(),  split(),  and
                   sub() built-in functions all ignore case when doing regular
                   expression operations.  NOTE:  Array  subscripting  is  not
                   affected.   However, the asort() and asorti() functions are
                   affected.
                   Thus, if IGNORECASE is not equal to zero, /aB/ matches  all
                   of the strings "ab", "aB", "Ab", and "AB".  As with all AWK
                   variables, the initial value of IGNORECASE is zero, so  all
                   regular expression and string operations are normally case-
                   sensitive.

       LINT        Provides dynamic control of the --lint option  from  within
                   an AWK program.  When true, gawk prints lint warnings. When
                   false,  it  does  not.   When  assigned  the  string  value
                   "fatal",  lint  warnings  become fatal errors, exactly like
                   --lint=fatal.  Any other true value just prints warnings.

       NF          The number of fields in the current input record.

       NR          The total number of input records seen so far.

       OFMT        The output format for numbers, "%.6g", by default.

       OFS         The output field separator, a space by default.

       ORS         The output record separator, by default a newline.

       PREC        The working precision of arbitrary precision floating-point
                   numbers, 53 by default.

       PROCINFO    The  elements  of  this array provide access to information
                   about the running AWK program.  On some systems, there  may
                   be  elements  in  the  array, "group1" through "groupn" for
                   some n, which is the number of  supplementary  groups  that
                   the  process  has.   Use  the in operator to test for these
                   elements.  The following  elements  are  guaranteed  to  be
                   available:

                   PROCINFO["egid"]    The  value  of  the  getegid(2)  system
                                       call.

                   PROCINFO["strftime"]
                                       The  default  time  format  string  for
                                       strftime().

                   PROCINFO["euid"]    The  value  of  the  geteuid(2)  system
                                       call.

                   PROCINFO["FS"]      "FS" if field splitting with FS  is  in
                                       effect,  "FPAT" if field splitting with
                                       FPAT is in effect, or "FIELDWIDTHS"  if
                                       field  splitting with FIELDWIDTHS is in
                                       effect.

                   PROCINFO["identifiers"]
                                       A subarray, indexed by the names of all
                                       identifiers used in the text of the AWK
                                       program.  The values indicate what gawk
                                       knows  about  the  identifiers after it
                                       has finished parsing the program;  they
                                       are not updated while the program runs.
                                       For each identifier, the value  of  the
                                       element is one of the following:

                                       "array"
                                              The identifier is an array.

                                       "builtin"
                                              The  identifier  is  a  built-in
                                              function.

                                       "extension"
                                              The identifier is  an  extension
                                              function loaded via @load or -l.

                                       "scalar"
                                              The identifier is a scalar.

                                       "untyped"
                                              The identifier is untyped (could
                                              be used as a  scalar  or  array,
                                              gawk doesn't know yet).

                                       "user" The identifier is a user-defined
                                              function.

                   PROCINFO["gid"]     The value of the getgid(2) system call.

                   PROCINFO["pgrpid"]  The process group  ID  of  the  current
                                       process.

                   PROCINFO["pid"]     The process ID of the current process.

                   PROCINFO["ppid"]    The  parent  process  ID of the current
                                       process.

                   PROCINFO["uid"]     The value of the getuid(2) system call.

                   PROCINFO["sorted_in"]
                                       If this  element  exists  in  PROCINFO,
                                       then  its  value  controls the order in
                                       which array elements are  traversed  in
                                       for   loops.    Supported   values  are
                                       "@ind_str_asc",         "@ind_num_asc",
                                       "@val_type_asc",        "@val_str_asc",
                                       "@val_num_asc",        "@ind_str_desc",
                                       "@ind_num_desc",      "@val_type_desc",
                                       "@val_str_desc",  "@val_num_desc",  and
                                       "@unsorted".  The value can also be the
                                       name of any comparison function defined
                                       as follows:

                                            function cmp_func(i1, v1, i2, v2)

                                       where i1 and i2 are the indices, and v1
                                       and v2 are the corresponding values  of
                                       the  two  elements  being compared.  It
                                       should return a number less than, equal
                                       to, or greater than 0, depending on how
                                       the elements of the  array  are  to  be
                                       ordered.

                   PROCINFO["input", "READ_TIMEOUT"]
                                       The timeout in milliseconds for reading
                                       data from input, where input is a redi‐
                                       rection  string  or a filename. A value
                                       of zero or  less  than  zero  means  no
                                       timeout.

                   PROCINFO["mpfr_version"]
                                       The  version  of  the  GNU MPFR library
                                       used  for  arbitrary  precision  number
                                       support  in  gawk.   This  entry is not
                                       present if MPFR support is not compiled
                                       into gawk.

                   PROCINFO["gmp_version"]
                                       The  version of the GNU MP library used
                                       for arbitrary precision number  support
                                       in  gawk.  This entry is not present if
                                       MPFR support is not compiled into gawk.

                   PROCINFO["prec_max"]
                                       The maximum precision supported by  the
                                       GNU  MPFR  library for arbitrary preci‐
                                       sion  floating-point   numbers.    This
                                       entry is not present if MPFR support is
                                       not compiled into gawk.

                   PROCINFO["prec_min"]
                                       The minimum precision  allowed  by  the
                                       GNU  MPFR  library for arbitrary preci‐
                                       sion  floating-point   numbers.    This
                                       entry is not present if MPFR support is
                                       not compiled into gawk.

                   PROCINFO["api_major"]
                                       The major version of the extension API.
                                       This  entry  is  not present if loading
                                       dynamic extensions is not available.

                   PROCINFO["api_minor"]
                                       The minor version of the extension API.
                                       This  entry  is  not present if loading
                                       dynamic extensions is not available.

                   PROCINFO["version"] the version of gawk.

       ROUNDMODE   The rounding mode to use for arbitrary precision arithmetic
                   on numbers, by default "N" (IEEE-754 roundTiesToEven mode).
                   The accepted values are "N" or "n" for roundTiesToEven, "U"
                   or "u" for roundTowardPositive, "D" or "d" for roundToward‐
                   Negative, "Z" or "z" for roundTowardZero, and if your  ver‐
                   sion  of  GNU  MPFR  library  supports  it,  "A" or "a" for
                   roundTiesToAway.

       RS          The input record separator, by default a newline.

       RT          The record terminator.  Gawk sets RT to the input text that
                   matched  the  character  or regular expression specified by
                   RS.

       RSTART      The index of the first character matched by match();  0  if
                   no  match.   (This  implies that character indices start at
                   one.)

       RLENGTH     The length of the string  matched  by  match();  -1  if  no
                   match.

       SUBSEP      The character used to separate multiple subscripts in array
                   elements, by default "\034".

       SYMTAB      An array whose indices  are  the  names  of  all  currently
                   defined  global  variables  and arrays in the program.  The
                   array may be used for indirect access to read or write  the
                   value of a variable:

                        foo = 5
                        SYMTAB["foo"] = 4
                        print foo    # prints 4

                   The isarray() function may be used to test if an element in
                   SYMTAB is an array.  You may not use the  delete  statement
                   with the SYMTAB array.

       TEXTDOMAIN  The text domain of the AWK program; used to find the local‐
                   ized translations for the program's strings.

   Arrays
       Arrays are subscripted with an expression between  square  brackets  ([
       and ]).  If the expression is an expression list (expr, expr ...)  then
       the array subscript is a string consisting of the concatenation of  the
       (string) value of each expression, separated by the value of the SUBSEP
       variable.  This facility  is  used  to  simulate  multiply  dimensioned
       arrays.  For example:

              i = "A"; j = "B"; k = "C"
              x[i, j, k] = "hello, world\n"

       assigns the string "hello, world\n" to the element of the array x which
       is indexed by the string "A\034B\034C".  All arrays in AWK are associa‐
       tive, i.e., indexed by string values.

       The  special  operator  in may be used to test if an array has an index
       consisting of a particular value:

              if (val in array)
                   print array[val]

       If the array has multiple subscripts, use (i, j) in array.

       The in construct may also be used in a for loop to iterate over all the
       elements  of  an  array.   However,  the (i, j) in array construct only
       works in tests, not in for loops.

       An element may be deleted from an array  using  the  delete  statement.
       The  delete statement may also be used to delete the entire contents of
       an array, just by specifying the array name without a subscript.

       gawk supports true multidimensional arrays. It does  not  require  that
       such arrays be ``rectangular'' as in C or C++.  For example:

              a[1] = 5
              a[2][1] = 6
              a[2][2] = 7

       NOTE:  You may need to tell gawk that an array element is really a sub‐
       array in order to use it where gawk expects an array (such  as  in  the
       second argument to split()).  You can do this by creating an element in
       the subarray and then deleting it with the delete statement.

   Variable Typing And Conversion
       Variables and fields may be (floating point) numbers,  or  strings,  or
       both.  How the value of a variable is interpreted depends upon its con‐
       text.  If used in a numeric expression, it will be treated as a number;
       if used as a string it will be treated as a string.

       To force a variable to be treated as a number, add 0 to it; to force it
       to be treated as a string, concatenate it with the null string.

       Uninitialized variables have the numeric value 0 and the  string  value
       "" (the null, or empty, string).

       When  a  string must be converted to a number, the conversion is accom‐
       plished using strtod(3).  A number is converted to a  string  by  using
       the  value  of  CONVFMT  as  a  format  string for sprintf(3), with the
       numeric value of the variable as the argument.   However,  even  though
       all  numbers in AWK are floating-point, integral values are always con‐
       verted as integers.  Thus, given

              CONVFMT = "%2.2f"
              a = 12
              b = a ""

       the variable b has a string value of "12" and not "12.00".

       NOTE: When operating in POSIX mode (such as with the  --posix  option),
       beware  that locale settings may interfere with the way decimal numbers
       are treated: the decimal separator of the numbers you  are  feeding  to
       gawk  must  conform to what your locale would expect, be it a comma (,)
       or a period (.).

       Gawk performs comparisons as follows: If  two  variables  are  numeric,
       they  are  compared numerically.  If one value is numeric and the other
       has a string value that is a “numeric  string,”  then  comparisons  are
       also  done numerically.  Otherwise, the numeric value is converted to a
       string and a string comparison is performed.  Two strings are compared,
       of course, as strings.

       Note that string constants, such as "57", are not numeric strings, they
       are string constants.  The idea of “numeric  string”  only  applies  to
       fields,  getline  input,  FILENAME, ARGV elements, ENVIRON elements and
       the elements of an array created by  split()  or  patsplit()  that  are
       numeric  strings.   The  basic  idea  is that user input, and only user
       input, that looks numeric, should be treated that way.

   Octal and Hexadecimal Constants
       You may use C-style octal and hexadecimal constants in your AWK program
       source  code.   For example, the octal value 011 is equal to decimal 9,
       and the hexadecimal value 0x11 is equal to decimal 17.

   String Constants
       String constants in AWK are sequences of  characters  enclosed  between
       double quotes (like "value").  Within strings, certain escape sequences
       are recognized, as in C.  These are:

       \\   A literal backslash.

       \a   The “alert” character; usually the ASCII BEL character.

       \b   Backspace.

       \f   Form-feed.

       \n   Newline.

       \r   Carriage return.

       \t   Horizontal tab.

       \v   Vertical tab.

       \xhex digits
            The character represented by the string of hexadecimal digits fol‐
            lowing  the \x.  As in ISO C, all following hexadecimal digits are
            considered part of the escape sequence.  (This feature should tell
            us something about language design by committee.)  E.g., "\x1B" is
            the ASCII ESC (escape) character.

       \ddd The character represented by the 1-, 2-, or  3-digit  sequence  of
            octal digits.  E.g., "\033" is the ASCII ESC (escape) character.

       \c   The literal character c.

       The  escape  sequences may also be used inside constant regular expres‐
       sions (e.g., /[ \t\f\n\r\v]/ matches whitespace characters).

       In compatibility mode, the characters represented by octal and hexadec‐
       imal  escape  sequences  are  treated  literally  when  used in regular
       expression constants.  Thus, /a\52b/ is equivalent to /a\*b/.

PATTERNS AND ACTIONS
       AWK is a line-oriented language.  The pattern comes first, and then the
       action.  Action statements are enclosed in { and }.  Either the pattern
       may be missing, or the action may be missing, but, of course, not both.
       If  the  pattern  is  missing,  the action is executed for every single
       record of input.  A missing action is equivalent to

              { print }

       which prints the entire record.

       Comments begin with the # character, and continue until the end of  the
       line.   Blank  lines  may  be used to separate statements.  Normally, a
       statement ends with a newline, however, this is not the case for  lines
       ending in a comma, {, ?, :, &&, or ||.  Lines ending in do or else also
       have their statements automatically continued on  the  following  line.
       In  other  cases,  a  line can be continued by ending it with a “\”, in
       which case the newline is ignored.

       Multiple statements may be put on one line by separating  them  with  a
       “;”.   This  applies to both the statements within the action part of a
       pattern-action pair (the usual case), and to the pattern-action  state‐
       ments themselves.

   Patterns
       AWK patterns may be one of the following:

              BEGIN
              END
              BEGINFILE
              ENDFILE
              /regular expression/
              relational expression
              pattern && pattern
              pattern || pattern
              pattern ? pattern : pattern
              (pattern)
              ! pattern
              pattern1, pattern2

       BEGIN  and  END  are two special kinds of patterns which are not tested
       against the input.  The action parts of all BEGIN patterns  are  merged
       as if all the statements had been written in a single BEGIN rule.  They
       are executed before any of the input is read.  Similarly, all  the  END
       rules are merged, and executed when all the input is exhausted (or when
       an exit statement is executed).  BEGIN and END patterns cannot be  com‐
       bined  with  other patterns in pattern expressions.  BEGIN and END pat‐
       terns cannot have missing action parts.

       BEGINFILE and ENDFILE are additional special patterns whose bodies  are
       executed  before  reading  the  first record of each command line input
       file and after reading the last record of each file.  Inside the BEGIN‐
       FILE  rule, the value of ERRNO will be the empty string if the file was
       opened successfully.  Otherwise, there is some problem  with  the  file
       and  the code should use nextfile to skip it. If that is not done, gawk
       produces its usual fatal error for files that cannot be opened.

       For /regular expression/ patterns, the associated statement is executed
       for  each  input  record  that matches the regular expression.  Regular
       expressions are the same as  those  in  egrep(1),  and  are  summarized
       below.

       A  relational  expression may use any of the operators defined below in
       the section on actions.  These generally test  whether  certain  fields
       match certain regular expressions.

       The  &&,  ||, and !  operators are logical AND, logical OR, and logical
       NOT, respectively, as in C.  They do short-circuit evaluation, also  as
       in  C,  and  are used for combining more primitive pattern expressions.
       As in most languages, parentheses may be used to change  the  order  of
       evaluation.

       The  ?:  operator is like the same operator in C.  If the first pattern
       is true then the pattern used for testing is the second pattern, other‐
       wise  it  is  the  third.  Only one of the second and third patterns is
       evaluated.

       The pattern1, pattern2 form of an expression is called a range pattern.
       It  matches  all input records starting with a record that matches pat‐
       tern1, and continuing until a record that matches pattern2,  inclusive.
       It does not combine with any other sort of pattern expression.

   Regular Expressions
       Regular  expressions  are  the  extended kind found in egrep.  They are
       composed of characters as follows:

       c          Matches the non-metacharacter c.

       \c         Matches the literal character c.

       .          Matches any character including newline.

       ^          Matches the beginning of a string.

       $          Matches the end of a string.

       [abc...]   A character list: matches any of the characters abc....  You
                  may  include a range of characters by separating them with a
                  dash.

       [^abc...]  A negated  character  list:  matches  any  character  except
                  abc....

       r1|r2      Alternation: matches either r1 or r2.

       r1r2       Concatenation: matches r1, and then r2.

       r+         Matches one or more r's.

       r*         Matches zero or more r's.

       r?         Matches zero or one r's.

       (r)        Grouping: matches r.

       r{n}
       r{n,}
       r{n,m}     One  or two numbers inside braces denote an interval expres‐
                  sion.  If there is one number in the braces,  the  preceding
                  regular  expression r is repeated n times.  If there are two
                  numbers separated by a comma, r is repeated n  to  m  times.
                  If  there  is  one  number  followed  by  a comma, then r is
                  repeated at least n times.

       \y         Matches the empty string at either the beginning or the  end
                  of a word.

       \B         Matches the empty string within a word.

       \<         Matches the empty string at the beginning of a word.

       \>         Matches the empty string at the end of a word.

       \s         Matches any whitespace character.

       \S         Matches any nonwhitespace character.

       \w         Matches  any  word-constituent  character (letter, digit, or
                  underscore).

       \W         Matches any character that is not word-constituent.

       \`         Matches the empty  string  at  the  beginning  of  a  buffer
                  (string).

       \'         Matches the empty string at the end of a buffer.

       The  escape  sequences  that  are valid in string constants (see String
       Constants) are also valid in regular expressions.

       Character classes are a feature introduced in the  POSIX  standard.   A
       character  class  is a special notation for describing lists of charac‐
       ters that have a specific attribute, but where  the  actual  characters
       themselves  can  vary from country to country and/or from character set
       to character set.  For example, the notion of  what  is  an  alphabetic
       character differs in the USA and in France.

       A  character  class  is  only  valid in a regular expression inside the
       brackets of a character list.  Character classes consist of [:, a  key‐
       word  denoting the class, and :].  The character classes defined by the
       POSIX standard are:

       [:alnum:]  Alphanumeric characters.

       [:alpha:]  Alphabetic characters.

       [:blank:]  Space or tab characters.

       [:cntrl:]  Control characters.

       [:digit:]  Numeric characters.

       [:graph:]  Characters that are both printable and visible.  (A space is
                  printable, but not visible, while an a is both.)

       [:lower:]  Lowercase alphabetic characters.

       [:print:]  Printable  characters (characters that are not control char‐
                  acters.)

       [:punct:]  Punctuation characters (characters that are not letter, dig‐
                  its, control characters, or space characters).

       [:space:]  Space  characters (such as space, tab, and formfeed, to name
                  a few).

       [:upper:]  Uppercase alphabetic characters.

       [:xdigit:] Characters that are hexadecimal digits.

       For example, before the POSIX standard, to match  alphanumeric  charac‐
       ters, you would have had to write /[A-Za-z0-9]/.  If your character set
       had other alphabetic characters in it, this would not match  them,  and
       if  your  character set collated differently from ASCII, this might not
       even match the ASCII alphanumeric characters.  With the POSIX character
       classes,  you  can write /[[:alnum:]]/, and this matches the alphabetic
       and numeric characters in your character set, no matter what it is.

       Two additional special sequences can appear in character lists.   These
       apply  to  non-ASCII  character  sets,  which  can  have single symbols
       (called collating elements) that are represented  with  more  than  one
       character,  as  well as several characters that are equivalent for col‐
       lating, or sorting, purposes.  (E.g., in French,  a  plain  “e”  and  a
       grave-accented “`” are equivalent.)

       Collating Symbols
              A  collating  symbol  is  a  multi-character  collating  element
              enclosed in [.  and .].  For example, if ch is a collating  ele‐
              ment,  then  [[.ch.]]  is a regular expression that matches this
              collating element, while  [ch]  is  a  regular  expression  that
              matches either c or h.

       Equivalence Classes
              An  equivalence  class  is  a locale-specific name for a list of
              characters that are equivalent.  The name is enclosed in [=  and
              =].   For  example, the name e might be used to represent all of
              “e,” “´,” and “`.”  In this case, [[=e=]] is a  regular  expres‐
              sion that matches any of e, ´, or `.

       These  features are very valuable in non-English speaking locales.  The
       library functions that gawk uses for regular expression  matching  cur‐
       rently  only  recognize  POSIX character classes; they do not recognize
       collating symbols or equivalence classes.

       The \y, \B, \<, \>, \s, \S, \w, \W, \`, and \' operators  are  specific
       to  gawk;  they  are  extensions based on facilities in the GNU regular
       expression libraries.

       The various command line options control how gawk interprets characters
       in regular expressions.

       No options
              In  the  default case, gawk provides all the facilities of POSIX
              regular expressions and the  GNU  regular  expression  operators
              described above.

       --posix
              Only  POSIX regular expressions are supported, the GNU operators
              are not special.  (E.g., \w matches a literal w).

       --traditional
              Traditional UNIX awk regular expressions are matched.   The  GNU
              operators  are  not  special,  and  interval expressions are not
              available.  Characters described by octal and hexadecimal escape
              sequences  are treated literally, even if they represent regular
              expression metacharacters.

       --re-interval
              Allow interval  expressions  in  regular  expressions,  even  if
              --traditional has been provided.

   Actions
       Action  statements  are enclosed in braces, { and }.  Action statements
       consist of the usual assignment, conditional,  and  looping  statements
       found  in  most  languages.   The  operators,  control  statements, and
       input/output statements available are patterned after those in C.

   Operators
       The operators in AWK, in order of decreasing precedence, are:

       (...)       Grouping

       $           Field reference.

       ++ --       Increment and decrement, both prefix and postfix.

       ^           Exponentiation (** may  also  be  used,  and  **=  for  the
                   assignment operator).

       + - !       Unary plus, unary minus, and logical negation.

       * / %       Multiplication, division, and modulus.

       + -         Addition and subtraction.

       space       String concatenation.

       |   |&      Piped I/O for getline, print, and printf.

       < > <= >= != ==
                   The regular relational operators.

       ~ !~        Regular  expression match, negated match.  NOTE: Do not use
                   a constant regular expression (/foo/) on the left-hand side
                   of  a  ~  or !~.  Only use one on the right-hand side.  The
                   expression /foo/ ~ exp has  the  same  meaning  as  (($0  ~
                   /foo/) ~ exp).  This is usually not what you want.

       in          Array membership.

       &&          Logical AND.

       ||          Logical OR.

       ?:          The  C  conditional  expression.  This has the form expr1 ?
                   expr2 : expr3.  If expr1 is true, the value of the  expres‐
                   sion  is  expr2,  otherwise it is expr3.  Only one of expr2
                   and expr3 is evaluated.

       = += -= *= /= %= ^=
                   Assignment.  Both absolute assignment  (var  =  value)  and
                   operator-assignment (the other forms) are supported.

   Control Statements
       The control statements are as follows:

              if (condition) statement [ else statement ]
              while (condition) statement
              do statement while (condition)
              for (expr1; expr2; expr3) statement
              for (var in array) statement
              break
              continue
              delete array[index]
              delete array
              exit [ expression ]
              { statements }
              switch (expression) {
              case value|regex : statement
              ...
              [ default: statement ]
              }

   I/O Statements
       The input/output statements are as follows:

       close(file [, how])   Close file, pipe or co-process.  The optional how
                             should only be used when closing  one  end  of  a
                             two-way  pipe  to  a  co-process.   It  must be a
                             string value, either "to" or "from".

       getline               Set $0 from next input record; set NF,  NR,  FNR,
                             RT.

       getline <file         Set $0 from next record of file; set NF, RT.

       getline var           Set var from next input record; set NR, FNR, RT.

       getline var <file     Set var from next record of file, RT.

       command | getline [var]
                             Run  command  piping the output either into $0 or
                             var, as above, and RT.

       command |& getline [var]
                             Run command as a  co-process  piping  the  output
                             either into $0 or var, as above, and RT.  Co-pro‐
                             cesses are a gawk extension.  (command  can  also
                             be  a  socket.   See  the subsection Special File
                             Names, below.)

       next                  Stop processing the current  input  record.   The
                             next  input  record is read and processing starts
                             over with the first pattern in the  AWK  program.
                             Upon  reaching  the  end  of the input data, gawk
                             executes any END rule(s).

       nextfile              Stop processing the current input file.  The next
                             input record read comes from the next input file.
                             FILENAME and ARGIND are updated, FNR is reset  to
                             1, and processing starts over with the first pat‐
                             tern in the AWK program.  Upon reaching  the  end
                             of the input data, gawk executes any END rule(s).

       print                 Print  the  current record.  The output record is
                             terminated with the value of ORS.

       print expr-list       Print expressions.  Each expression is  separated
                             by the value of OFS.  The output record is termi‐
                             nated with the value of ORS.

       print expr-list >file Print expressions on file.   Each  expression  is
                             separated by the value of OFS.  The output record
                             is terminated with the value of ORS.

       printf fmt, expr-list Format and  print.   See  The  printf  Statement,
                             below.

       printf fmt, expr-list >file
                             Format and print on file.

       system(cmd-line)      Execute the command cmd-line, and return the exit
                             status.  (This may not be available on  non-POSIX
                             systems.)

       fflush([file])        Flush any buffers associated with the open output
                             file or pipe file.  If file is missing or  if  it
                             is  the  null  string, then flush all open output
                             files and pipes.

       Additional output redirections are allowed for print and printf.

       print ... >> file
              Appends output to the file.

       print ... | command
              Writes on a pipe.

       print ... |& command
              Sends data to a co-process or socket.  (See also the  subsection
              Special File Names, below.)

       The  getline  command returns 1 on success, 0 on end of file, and -1 on
       an error.  Upon an error, ERRNO is set to a string describing the prob‐
       lem.

       NOTE:  Failure in opening a two-way socket results in a non-fatal error
       being returned to the calling function. If using a pipe, co-process, or
       socket  to getline, or from print or printf within a loop, you must use
       close() to create new instances of the command or socket.  AWK does not
       automatically  close  pipes,  sockets, or co-processes when they return
       EOF.

   The printf Statement
       The AWK versions of the printf statement and  sprintf()  function  (see
       below) accept the following conversion specification formats:

       %c      A single character.  If the argument used for %c is numeric, it
               is treated as a character and printed.  Otherwise, the argument
               is assumed to be a string, and the only first character of that
               string is printed.

       %d, %i  A decimal number (the integer part).

       %e, %E  A floating point number of the form [-]d.dddddde[+-]dd.  The %E
               format uses E instead of e.

       %f, %F  A floating point number of the form [-]ddd.dddddd.  If the sys‐
               tem library supports it, %F is available as well. This is  like
               %f,  but  uses  capital  letters for special “not a number” and
               “infinity” values. If %F is not available, gawk uses %f.

       %g, %G  Use %e or %f conversion, whichever is shorter, with nonsignifi‐
               cant zeros suppressed.  The %G format uses %E instead of %e.

       %o      An unsigned octal number (also an integer).

       %u      An unsigned decimal number (again, an integer).

       %s      A character string.

       %x, %X  An  unsigned  hexadecimal  number  (an integer).  The %X format
               uses ABCDEF instead of abcdef.

       %%      A single % character; no argument is converted.

       Optional, additional parameters may lie between the % and  the  control
       letter:

       count$ Use the count'th argument at this point in the formatting.  This
              is called a positional specifier and is intended  primarily  for
              use  in translated versions of format strings, not in the origi‐
              nal text of an AWK program.  It is a gawk extension.

       -      The expression should be left-justified within its field.

       space  For numeric conversions, prefix positive values  with  a  space,
              and negative values with a minus sign.

       +      The  plus sign, used before the width modifier (see below), says
              to always supply a sign for numeric  conversions,  even  if  the
              data  to  be  formatted  is positive.  The + overrides the space
              modifier.

       #      Use an “alternate form” for certain control  letters.   For  %o,
              supply  a  leading zero.  For %x, and %X, supply a leading 0x or
              0X for a nonzero result.  For %e, %E,  %f  and  %F,  the  result
              always contains a decimal point.  For %g, and %G, trailing zeros
              are not removed from the result.

       0      A leading 0 (zero) acts as a flag, that indicates output  should
              be  padded  with zeroes instead of spaces.  This applies only to
              the numeric output formats.  This flag only has an  effect  when
              the field width is wider than the value to be printed.

       '      A  single  quote character instructs gawk to insert the locale's
              thousands-separator character into decimal numbers, and to  also
              use  the  locale's  decimal  point character with floating point
              formats.  This requires correct locale support in the C  library
              and in the definition of the current locale.

       width  The field should be padded to this width.  The field is normally
              padded with spaces.  With the 0 flag, it is padded with zeroes.

       .prec  A number that specifies the precision to use when printing.  For
              the  %e,  %E,  %f  and %F, formats, this specifies the number of
              digits you want printed to the right of the decimal point.   For
              the  %g, and %G formats, it specifies the maximum number of sig‐
              nificant digits.  For the %d, %i, %o, %u, %x, and %X formats, it
              specifies  the  minimum  number  of digits to print.  For %s, it
              specifies the maximum number of characters from the string  that
              should be printed.

       The  dynamic width and prec capabilities of the ISO C printf() routines
       are supported.  A * in place of either the width or prec specifications
       causes  their  values  to  be taken from the argument list to printf or
       sprintf().  To use a positional specifier with a dynamic width or  pre‐
       cision,  supply the count$ after the * in the format string.  For exam‐
       ple, "%3$*2$.*1$s".

   Special File Names
       When doing I/O redirection from either print or printf into a file,  or
       via  getline  from  a  file,  gawk recognizes certain special filenames
       internally.  These filenames allow  access  to  open  file  descriptors
       inherited  from  gawk's parent process (usually the shell).  These file
       names may also be used on the command line to  name  data  files.   The
       filenames are:

       -           The standard input.

       /dev/stdin  The standard input.

       /dev/stdout The standard output.

       /dev/stderr The standard error output.

       /dev/fd/n   The file associated with the open file descriptor n.

       These are particularly useful for error messages.  For example:

              print "You blew it!" > "/dev/stderr"

       whereas you would otherwise have to use

              print "You blew it!" | "cat 1>&2"

       The  following  special  filenames  may  be used with the |& co-process
       operator for creating TCP/IP network connections:

       /inet/tcp/lport/rhost/rport
       /inet4/tcp/lport/rhost/rport
       /inet6/tcp/lport/rhost/rport
              Files for a TCP/IP connection on local port lport to remote host
              rhost  on remote port rport.  Use a port of 0 to have the system
              pick a port.  Use /inet4 to force an IPv4 connection, and /inet6
              to  force  an  IPv6  connection.   Plain  /inet  uses the system
              default (most likely IPv4).

       /inet/udp/lport/rhost/rport
       /inet4/udp/lport/rhost/rport
       /inet6/udp/lport/rhost/rport
              Similar, but use UDP/IP instead of TCP/IP.

   Numeric Functions
       AWK has the following built-in arithmetic functions:

       atan2(y, x)   Return the arctangent of y/x in radians.

       cos(expr)     Return the cosine of expr, which is in radians.

       exp(expr)     The exponential function.

       int(expr)     Truncate to integer.

       log(expr)     The natural logarithm function.

       rand()        Return a random number N, between 0 and 1, such that 0  ≤
                     N < 1.

       sin(expr)     Return the sine of expr, which is in radians.

       sqrt(expr)    Return the square root of expr.

       srand([expr]) Use expr as the new seed for the random number generator.
                     If no expr is provided, use the time of day.  Return  the
                     previous seed for the random number generator.

   String Functions
       Gawk has the following built-in string functions:

       asort(s [, d [, how] ]) Return  the  number  of  elements in the source
                               array s.  Sort the contents of s  using  gawk's
                               normal  rules for comparing values, and replace
                               the indices of the sorted values s with sequen‐
                               tial  integers starting with 1. If the optional
                               destination array d is specified, first  dupli‐
                               cate  s  into  d,  and then sort d, leaving the
                               indices of the source array  s  unchanged.  The
                               optional  string how controls the direction and
                               the comparison mode.  Valid values for how  are
                               any     of     the     strings     valid    for
                               PROCINFO["sorted_in"].  It can also be the name
                               of   a   user-defined  comparison  function  as
                               described in PROCINFO["sorted_in"].

       asorti(s [, d [, how] ])
                               Return the number of  elements  in  the  source
                               array  s.   The behavior is the same as that of
                               asort(), except that the array indices are used
                               for  sorting, not the array values.  When done,
                               the array is indexed numerically, and the  val‐
                               ues  are  those  of  the original indices.  The
                               original values are lost; thus provide a second
                               array  if  you  wish  to preserve the original.
                               The purpose of the optional string how  is  the
                               same as described in asort() above.

       gensub(r, s, h [, t])   Search  the  target string t for matches of the
                               regular expression r.  If h is a string  begin‐
                               ning with g or G, then replace all matches of r
                               with s.  Otherwise, h is  a  number  indicating
                               which  match of r to replace.  If t is not sup‐
                               plied, use $0 instead.  Within the  replacement
                               text  s,  the  sequence  \n, where n is a digit
                               from 1 to 9, may be used to indicate  just  the
                               text that matched the n'th parenthesized subex‐
                               pression.   The  sequence  \0  represents   the
                               entire  matched  text, as does the character &.
                               Unlike sub() and gsub(), the modified string is
                               returned as the result of the function, and the
                               original target string is not changed.

       gsub(r, s [, t])        For each substring matching the regular expres‐
                               sion  r  in the string t, substitute the string
                               s, and return the number of substitutions.   If
                               t  is  not  supplied,  use  $0.   An  &  in the
                               replacement text is replaced with the text that
                               was  actually matched.  Use \& to get a literal
                               &.  (This must be typed  as  "\\&";  see  GAWK:
                               Effective  AWK Programming for a fuller discus‐
                               sion of the rules for &'s  and  backslashes  in
                               the replacement text of sub(), gsub(), and gen‐
                               sub().)

       index(s, t)             Return the index of the string t in the  string
                               s,  or  0  if  t is not present.  (This implies
                               that character indices start at one.)  It is  a
                               fatal error to use a regexp constant for t.

       length([s])             Return  the  length  of  the  string  s, or the
                               length of $0 if s is not supplied.  As  a  non-
                               standard  extension,  with  an  array argument,
                               length() returns the number of elements in  the
                               array.

       match(s, r [, a])       Return  the  position  in  s  where the regular
                               expression r occurs, or 0 if r is not  present,
                               and set the values of RSTART and RLENGTH.  Note
                               that the argument order is the same as for  the
                               ~  operator: str ~ re.  If array a is provided,
                               a is cleared and then elements 1 through n  are
                               filled  with  the  portions of s that match the
                               corresponding parenthesized subexpression in r.
                               The 0'th element of a contains the portion of s
                               matched by the  entire  regular  expression  r.
                               Subscripts  a[n,  "start"],  and a[n, "length"]
                               provide the starting index in  the  string  and
                               length  respectively,  of  each  matching  sub‐
                               string.

       patsplit(s, a [, r [, seps] ])
                               Split the string s into the  array  a  and  the
                               separators array seps on the regular expression
                               r, and return the number  of  fields.   Element
                               values  are  the  portions of s that matched r.
                               The value of  seps[i]  is  the  separator  that
                               appeared  in front of a[i+1].  If r is omitted,
                               FPAT is used instead.  The arrays  a  and  seps
                               are  cleared  first.  Splitting behaves identi‐
                               cally to field splitting with  FPAT,  described
                               above.

       split(s, a [, r [, seps] ])
                               Split  the  string  s  into the array a and the
                               separators array seps on the regular expression
                               r,  and  return  the number of fields.  If r is
                               omitted, FS is used instead.  The arrays a  and
                               seps  are  cleared first.  seps[i] is the field
                               separator matched by r between a[i] and a[i+1].
                               If r is a single space, then leading whitespace
                               in s goes into the extra array element  seps[0]
                               and  trailing  whitespace  goes  into the extra
                               array element seps[n], where n  is  the  return
                               value  of  split(s,  a,  r,  seps).   Splitting
                               behaves   identically   to   field   splitting,
                               described above.

       sprintf(fmt, expr-list) Print  expr-list  according  to fmt, and return
                               the resulting string.

       strtonum(str)           Examine str, and return its numeric value.   If
                               str  begins  with  a  leading 0, treat it as an
                               octal number.  If str begins with a leading  0x
                               or  0X, treat it as a hexadecimal number.  Oth‐
                               erwise, assume it is a decimal number.

       sub(r, s [, t])         Just like gsub(), but replace  only  the  first
                               matching substring.

       substr(s, i [, n])      Return  the  at most n-character substring of s
                               starting at i.  If n is omitted, use  the  rest
                               of s.

       tolower(str)            Return  a  copy of the string str, with all the
                               uppercase characters in str translated to their
                               corresponding   lowercase  counterparts.   Non-
                               alphabetic characters are left unchanged.

       toupper(str)            Return a copy of the string str, with  all  the
                               lowercase characters in str translated to their
                               corresponding  uppercase  counterparts.    Non-
                               alphabetic characters are left unchanged.

       Gawk  is  multibyte aware.  This means that index(), length(), substr()
       and match() all work in terms of characters, not bytes.

   Time Functions
       Since one of the primary uses of AWK programs is processing  log  files
       that  contain time stamp information, gawk provides the following func‐
       tions for obtaining time stamps and formatting them.

       mktime(datespec)
                 Turn datespec into a time stamp of the same form as  returned
                 by  systime(),  and  return  the  result.   The datespec is a
                 string of the form YYYY MM DD HH MM SS[ DST].   The  contents
                 of  the  string are six or seven numbers representing respec‐
                 tively the full year including century, the month from  1  to
                 12,  the  day  of the month from 1 to 31, the hour of the day
                 from 0 to 23, the minute from 0 to 59, the second from  0  to
                 60,  and  an  optional  daylight  saving flag.  The values of
                 these numbers need not be within the  ranges  specified;  for
                 example,  an  hour  of  -1 means 1 hour before midnight.  The
                 origin-zero Gregorian calendar is assumed, with year  0  pre‐
                 ceding  year  1  and  year  -1 preceding year 0.  The time is
                 assumed to be in the local timezone.  If the daylight  saving
                 flag  is  positive, the time is assumed to be daylight saving
                 time; if zero, the time is assumed to be standard  time;  and
                 if  negative  (the  default),  mktime() attempts to determine
                 whether daylight saving time is in effect for  the  specified
                 time.  If datespec does not contain enough elements or if the
                 resulting time is out of range, mktime() returns -1.

       strftime([format [, timestamp[, utc-flag]]])
                 Format timestamp according to the  specification  in  format.
                 If  utc-flag  is  present  and  is  non-zero or non-null, the
                 result is in UTC, otherwise the result is in local time.  The
                 timestamp  should  be  of  the  same form as returned by sys‐
                 time().  If timestamp is missing, the current time of day  is
                 used.   If  format is missing, a default format equivalent to
                 the output of date(1) is used.  The default format is  avail‐
                 able  in PROCINFO["strftime"].  See the specification for the
                 strftime() function in ISO C for the format conversions  that
                 are guaranteed to be available.

       systime() Return the current time of day as the number of seconds since
                 the Epoch (1970-01-01 00:00:00 UTC on POSIX systems).

   Bit Manipulations Functions
       Gawk supplies the following bit manipulation functions.  They  work  by
       converting  double-precision  floating  point values to uintmax_t inte‐
       gers, doing the operation, and  then  converting  the  result  back  to
       floating point.  The functions are:

       and(v1, v2 [, ...]) Return  the  bitwise  AND of the values provided in
                           the argument list.  There must be at least two.

       compl(val)          Return the bitwise complement of val.

       lshift(val, count)  Return the value of  val,  shifted  left  by  count
                           bits.

       or(v1, v2 [, ...])  Return the bitwise OR of the values provided in the
                           argument list.  There must be at least two.

       rshift(val, count)  Return the value of val,  shifted  right  by  count
                           bits.

       xor(v1, v2 [, ...]) Return  the  bitwise  XOR of the values provided in
                           the argument list.  There must be at least two.

   Type Function
       The following function is for use with multidimensional arrays.

       isarray(x)
              Return true if x is an array, false otherwise.

   Internationalization Functions
       The following functions may be used from within your  AWK  program  for
       translating strings at run-time.  For full details, see GAWK: Effective
       AWK Programming.

       bindtextdomain(directory [, domain])
              Specify the directory where gawk looks for the  .gmo  files,  in
              case they will not or cannot be placed in the ``standard'' loca‐
              tions (e.g., during testing).  It returns  the  directory  where
              domain is ``bound.''
              The  default domain is the value of TEXTDOMAIN.  If directory is
              the null string (""), then bindtextdomain() returns the  current
              binding for the given domain.

       dcgettext(string [, domain [, category]])
              Return  the  translation  of  string  in  text domain domain for
              locale category category.  The default value for domain  is  the
              current  value of TEXTDOMAIN.  The default value for category is
              "LC_MESSAGES".
              If you supply a value for category, it must be a string equal to
              one  of the known locale categories described in GAWK: Effective
              AWK Programming.  You must  also  supply  a  text  domain.   Use
              TEXTDOMAIN if you want to use the current domain.

       dcngettext(string1, string2, number [, domain [, category]])
              Return  the  plural  form  used for number of the translation of
              string1 and string2 in text domain domain  for  locale  category
              category.   The default value for domain is the current value of
              TEXTDOMAIN.  The default value for category is "LC_MESSAGES".
              If you supply a value for category, it must be a string equal to
              one  of the known locale categories described in GAWK: Effective
              AWK Programming.  You must  also  supply  a  text  domain.   Use
              TEXTDOMAIN if you want to use the current domain.

USER-DEFINED FUNCTIONS
       Functions in AWK are defined as follows:

              function name(parameter list) { statements }

       Functions  are executed when they are called from within expressions in
       either patterns or actions.  Actual parameters supplied in the function
       call  are  used  to  instantiate  the formal parameters declared in the
       function.  Arrays are passed by reference, other variables  are  passed
       by value.

       Since  functions were not originally part of the AWK language, the pro‐
       vision for local variables is rather clumsy: They are declared as extra
       parameters  in the parameter list.  The convention is to separate local
       variables from real parameters by extra spaces in the  parameter  list.
       For example:

              function  f(p, q,     a, b)   # a and b are local
              {
                   ...
              }

              /abc/     { ... ; f(1, 2) ; ... }

       The left parenthesis in a function call is required to immediately fol‐
       low the function name, without any intervening whitespace.  This avoids
       a  syntactic  ambiguity with the concatenation operator.  This restric‐
       tion does not apply to the built-in functions listed above.

       Functions may call each other and may be recursive.   Function  parame‐
       ters used as local variables are initialized to the null string and the
       number zero upon function invocation.

       Use return expr to return a value from a function.  The return value is
       undefined if no value is provided, or if the function returns by “fall‐
       ing off” the end.

       As a gawk extension, functions may be called indirectly.  To  do  this,
       assign  the  name of the function to be called, as a string, to a vari‐
       able.  Then use the variable as if it were the name of a function, pre‐
       fixed with an @ sign, like so:
              function myfunc()
              {
                   print "myfunc called"
                   ...
              }

              {    ...
                   the_func = "myfunc"
                   @the_func()    # call through the_func to myfunc
                   ...
              }
       As  of  version 4.1.2, this works with user-defined functions, built-in
       functions, and extension functions.

       If --lint has been provided, gawk warns about calls to undefined  func‐
       tions  at  parse  time,  instead  of at run time.  Calling an undefined
       function at run time is a fatal error.

       The word func may be used in place of function, although this is depre‐
       cated.

DYNAMICALLY LOADING NEW FUNCTIONS
       You  can  dynamically  add  new  built-in functions to the running gawk
       interpreter with the @load statement.  The full details are beyond  the
       scope of this manual page; see GAWK: Effective AWK Programming.

SIGNALS
       The  gawk  profiler  accepts  two signals.  SIGUSR1 causes it to dump a
       profile and function call stack to the profile file,  which  is  either
       awkprof.out,  or whatever file was named with the --profile option.  It
       then continues to run.  SIGHUP causes gawk  to  dump  the  profile  and
       function call stack and then exit.

INTERNATIONALIZATION
       String constants are sequences of characters enclosed in double quotes.
       In non-English speaking environments, it is possible to mark strings in
       the AWK program as requiring translation to the local natural language.
       Such strings are marked in the AWK program with  a  leading  underscore
       (“_”).  For example,

              gawk 'BEGIN { print "hello, world" }'

       always prints hello, world.  But,

              gawk 'BEGIN { print _"hello, world" }'

       might print bonjour, monde in France.

       There are several steps involved in producing and running a localizable
       AWK program.

       1.  Add a BEGIN action to assign a value to the TEXTDOMAIN variable  to
           set the text domain to a name associated with your program:

                BEGIN { TEXTDOMAIN = "myprog" }

           This  allows  gawk  to find the .gmo file associated with your pro‐
           gram.  Without this step, gawk uses the messages text domain, which
           likely does not contain translations for your program.

       2.  Mark  all  strings  that  should  be translated with leading under‐
           scores.

       3.  If necessary, use the dcgettext() and/or bindtextdomain() functions
           in your program, as appropriate.

       4.  Run  gawk  --gen-pot  -f myprog.awk > myprog.pot to generate a .pot
           file for your program.

       5.  Provide appropriate translations, and build and install the  corre‐
           sponding .gmo files.

       The internationalization features are described in full detail in GAWK:
       Effective AWK Programming.

POSIX COMPATIBILITY
       A primary goal for gawk is compatibility with the  POSIX  standard,  as
       well as with the latest version of Brian Kernighan's awk.  To this end,
       gawk incorporates the following user visible  features  which  are  not
       described  in  the AWK book, but are part of the Brian Kernighan's ver‐
       sion of awk, and are in the POSIX standard.

       The book indicates that command line variable assignment  happens  when
       awk  would  otherwise  open  the argument as a file, which is after the
       BEGIN rule is executed.  However, in earlier implementations, when such
       an assignment appeared before any file names, the assignment would hap‐
       pen before the BEGIN rule was run.  Applications came to depend on this
       “feature.”   When  awk  was  changed to match its documentation, the -v
       option for assigning variables before program execution  was  added  to
       accommodate  applications  that  depended upon the old behavior.  (This
       feature was agreed upon by both  the  Bell  Laboratories  and  the  GNU
       developers.)

       When  processing arguments, gawk uses the special option “--” to signal
       the end of arguments.  In compatibility mode, it warns about but other‐
       wise  ignores  undefined  options.  In normal operation, such arguments
       are passed on to the AWK program for it to process.

       The AWK book does not define the return value of  srand().   The  POSIX
       standard has it return the seed it was using, to allow keeping track of
       random number sequences.  Therefore srand() in gawk  also  returns  its
       current seed.

       Other  new features are: The use of multiple -f options (from MKS awk);
       the ENVIRON array; the \a, and \v escape sequences (done originally  in
       gawk  and  fed  back into the Bell Laboratories version); the tolower()
       and toupper() built-in functions (from the Bell Laboratories  version);
       and  the  ISO  C conversion specifications in printf (done first in the
       Bell Laboratories version).

HISTORICAL FEATURES
       There is one feature of historical AWK implementations that  gawk  sup‐
       ports:  It  is possible to call the length() built-in function not only
       with no argument, but even without parentheses!  Thus,

              a = length     # Holy Algol 60, Batman!

       is the same as either of

              a = length()
              a = length($0)

       Using this feature is poor practice, and gawk issues  a  warning  about
       its use if --lint is specified on the command line.

GNU EXTENSIONS
       Gawk  has  a  too-large  number  of  extensions to POSIX awk.  They are
       described in this section.  All the extensions described  here  can  be
       disabled by invoking gawk with the --traditional or --posix options.

       The following features of gawk are not available in POSIX awk.

       · No  path  search  is  performed  for  files  named via the -f option.
         Therefore the AWKPATH environment variable is not special.

       · There is no facility for doing file inclusion (gawk's @include mecha‐
         nism).

       · There  is no facility for dynamically adding new functions written in
         C (gawk's @load mechanism).

       · The \x escape sequence.  (Disabled with --posix.)

       · The ability to  continue  lines  after  ?   and  :.   (Disabled  with
         --posix.)

       · Octal and hexadecimal constants in AWK programs.

       · The ARGIND, BINMODE, ERRNO, LINT, RT and TEXTDOMAIN variables are not
         special.

       · The IGNORECASE variable and its side-effects are not available.

       · The FIELDWIDTHS variable and fixed-width field splitting.

       · The FPAT variable and field splitting based on field values.

       · The PROCINFO array is not available.

       · The use of RS as a regular expression.

       · The special file names available for I/O redirection are  not  recog‐
         nized.

       · The |& operator for creating co-processes.

       · The BEGINFILE and ENDFILE special patterns are not available.

       · The  ability to split out individual characters using the null string
         as the value of FS, and as the third argument to split().

       · An optional fourth argument  to  split()  to  receive  the  separator
         texts.

       · The optional second argument to the close() function.

       · The optional third argument to the match() function.

       · The ability to use positional specifiers with printf and sprintf().

       · The ability to pass an array to length().

       · The and(), asort(), asorti(), bindtextdomain(), compl(), dcgettext(),
         dcngettext(),  gensub(),  lshift(),   mktime(),   or(),   patsplit(),
         rshift(), strftime(), strtonum(), systime() and xor() functions.

       · Localizable strings.

       The  AWK book does not define the return value of the close() function.
       Gawk's close() returns the value from  fclose(3),  or  pclose(3),  when
       closing an output file or pipe, respectively.  It returns the process's
       exit status when closing an input pipe.  The return value is -1 if  the
       named file, pipe or co-process was not opened with a redirection.

       When  gawk is invoked with the --traditional option, if the fs argument
       to the -F option is “t”, then FS is set to  the  tab  character.   Note
       that  typing  gawk  -F\t ...  simply causes the shell to quote the “t,”
       and does not pass “\t” to the -F option.  Since this is a  rather  ugly
       special  case, it is not the default behavior.  This behavior also does
       not occur if --posix has been specified.  To really get a tab character
       as  the  field  separator, it is best to use single quotes: gawk -F'\t'
       ....

ENVIRONMENT VARIABLES
       The AWKPATH environment variable can be  used  to  provide  a  list  of
       directories that gawk searches when looking for files named via the -f,
       --file, -i and --include options.  If the  initial  search  fails,  the
       path is searched again after appending .awk to the filename.

       The  AWKLIBPATH  environment  variable can be used to provide a list of
       directories that gawk searches when looking for files named via the  -l
       and --load options.

       The  GAWK_READ_TIMEOUT  environment  variable  can be used to specify a
       timeout in milliseconds for reading input from a terminal, pipe or two-
       way communication including sockets.

       For  connection to a remote host via socket, GAWK_SOCK_RETRIES controls
       the number of retries, and GAWK_MSEC_SLEEP  and  the  interval  between
       retries.   The interval is in milliseconds. On systems that do not sup‐
       port usleep(3), the value is rounded up to an integral number  of  sec‐
       onds.

       If POSIXLY_CORRECT exists in the environment, then gawk behaves exactly
       as if --posix had been specified on the command line.   If  --lint  has
       been specified, gawk issues a warning message to this effect.

EXIT STATUS
       If  the  exit  statement is used with a value, then gawk exits with the
       numeric value given to it.

       Otherwise, if there were no problems during execution, gawk exits  with
       the value of the C constant EXIT_SUCCESS.  This is usually zero.

       If  an  error  occurs,  gawk  exits  with  the  value of the C constant
       EXIT_FAILURE.  This is usually one.

       If gawk exits because of a fatal error, the exit status is 2.  On  non-
       POSIX systems, this value may be mapped to EXIT_FAILURE.

VERSION INFORMATION
       This man page documents gawk, version 4.1.

AUTHORS
       The original version of UNIX awk was designed and implemented by Alfred
       Aho, Peter Weinberger, and Brian Kernighan of Bell Laboratories.  Brian
       Kernighan continues to maintain and enhance it.

       Paul  Rubin  and  Jay  Fenlason, of the Free Software Foundation, wrote
       gawk, to be compatible with the original version of awk distributed  in
       Seventh  Edition  UNIX.   John Woods contributed a number of bug fixes.
       David Trueman, with contributions from Arnold Robbins, made  gawk  com‐
       patible  with  the new version of UNIX awk.  Arnold Robbins is the cur‐
       rent maintainer.

       See GAWK: Effective AWK Programming for a full list of the contributors
       to gawk and its documentation.

       See the README file in the gawk distribution for up-to-date information
       about maintainers and which ports are currently supported.

BUG REPORTS
       If you find a  bug  in  gawk,  please  send  electronic  mail  to  bug-
       gawk@gnu.org.   Please  include your operating system and its revision,
       the version of gawk (from gawk --version), which C compiler you used to
       compile  it,  and a test program and data that are as small as possible
       for reproducing the problem.

       Before sending a bug report, please do the  following  things.   First,
       verify  that  you  have the latest version of gawk.  Many bugs (usually
       subtle ones) are fixed at each release, and if yours is  out  of  date,
       the  problem  may already have been solved.  Second, please see if set‐
       ting the environment variable  LC_ALL  to  LC_ALL=C  causes  things  to
       behave  as  you  expect. If so, it's a locale issue, and may or may not
       really be a bug.  Finally, please read this man page and the  reference
       manual  carefully  to  be  sure that what you think is a bug really is,
       instead of just a quirk in the language.

       Whatever you do, do NOT post a bug report in comp.lang.awk.  While  the
       gawk  developers  occasionally read this newsgroup, posting bug reports
       there is an unreliable way to report bugs.   Instead,  please  use  the
       electronic mail addresses given above.  Really.

       If you're using a GNU/Linux or BSD-based system, you may wish to submit
       a bug report to the vendor of  your  distribution.   That's  fine,  but
       please send a copy to the official email address as well, since there's
       no guarantee that the bug report will be forwarded to  the  gawk  main‐
       tainer.

BUGS
       The  -F option is not necessary given the command line variable assign‐
       ment feature; it remains only for backwards compatibility.

SEE ALSO
       egrep(1),  sed(1),  getpid(2),   getppid(2),   getpgrp(2),   getuid(2),
       geteuid(2), getgid(2), getegid(2), getgroups(2), usleep(3)

       The  AWK Programming Language, Alfred V. Aho, Brian W. Kernighan, Peter
       J. Weinberger, Addison-Wesley, 1988.  ISBN 0-201-07981-X.

       GAWK: Effective AWK Programming, Edition 4.1,  shipped  with  the  gawk
       source.   The  current  version of this document is available online at
       http://www.gnu.org/software/gawk/manual.

EXAMPLES
       Print and sort the login names of all users:

            BEGIN     { FS = ":" }
                 { print $1 | "sort" }

       Count lines in a file:

                 { nlines++ }
            END  { print nlines }

       Precede each line by its number in the file:

            { print FNR, $0 }

       Concatenate and line number (a variation on a theme):

            { print NR, $0 }

       Run an external command for particular lines of data:

            tail -f access_log |
            awk '/myhome.html/ { system("nmap " $1 ">> logdir/myhome.html") }'

ACKNOWLEDGEMENTS
       Brian Kernighan provided valuable assistance during testing and  debug‐
       ging.  We thank him.

COPYING PERMISSIONS
       Copyright © 1989, 1991, 1992, 1993, 1994, 1995, 1996, 1997, 1998, 1999,
       2001, 2002, 2003, 2004, 2005, 2007, 2009, 2010, 2011, 2012, 2013,  2014
       Free Software Foundation, Inc.

       Permission  is  granted  to make and distribute verbatim copies of this
       manual page provided the copyright notice and  this  permission  notice
       are preserved on all copies.

       Permission  is granted to copy and distribute modified versions of this
       manual page under the conditions for verbatim  copying,  provided  that
       the  entire  resulting derived work is distributed under the terms of a
       permission notice identical to this one.

       Permission is granted to copy and distribute translations of this  man‐
       ual page into another language, under the above conditions for modified
       versions, except that this permission notice may be stated in a  trans‐
       lation approved by the Foundation.

Free Software Foundation          Aug 03 2014                          GAWK(1)
```

---


```
SED(1)                           User Commands                          SED(1)

NAME
       sed - stream editor for filtering and transforming text

SYNOPSIS
       sed [OPTION]... {script-only-if-no-other-script} [input-file]...

DESCRIPTION
       Sed  is a stream editor.  A stream editor is used to perform basic text
       transformations on an input stream (a file or input from  a  pipeline).
       While  in  some  ways similar to an editor which permits scripted edits
       (such as ed), sed works by making only one pass over the input(s),  and
       is consequently more efficient.  But it is sed's ability to filter text
       in a pipeline which particularly distinguishes it from other  types  of
       editors.

       -n, --quiet, --silent

              suppress automatic printing of pattern space

       -e script, --expression=script

              add the script to the commands to be executed

       -f script-file, --file=script-file

              add the contents of script-file to the commands to be executed

       --follow-symlinks

              follow symlinks when processing in place

       -i[SUFFIX], --in-place[=SUFFIX]

              edit files in place (makes backup if SUFFIX supplied)

       -l N, --line-length=N

              specify the desired line-wrap length for the `l' command

       --posix

              disable all GNU extensions.

       -r, --regexp-extended

              use extended regular expressions in the script.

       -s, --separate

              consider  files  as  separate rather than as a single continuous
              long stream.

       -u, --unbuffered

              load minimal amounts of data from the input files and flush  the
              output buffers more often

       -z, --null-data

              separate lines by NUL characters

       --help
              display this help and exit

       --version
              output version information and exit

       If  no  -e, --expression, -f, or --file option is given, then the first
       non-option argument is taken as  the  sed  script  to  interpret.   All
       remaining  arguments  are  names  of input files; if no input files are
       specified, then the standard input is read.

       GNU sed home page:  <http://www.gnu.org/software/sed/>.   General  help
       using  GNU software: <http://www.gnu.org/gethelp/>.  E-mail bug reports
       to: <bug-sed@gnu.org>.  Be sure to include the word  ``sed''  somewhere
       in the ``Subject:'' field.

COMMAND SYNOPSIS
       This is just a brief synopsis of sed commands to serve as a reminder to
       those who already know sed; other documentation (such  as  the  texinfo
       document) must be consulted for fuller descriptions.

   Zero-address ``commands''
       : label
              Label for b and t commands.

       #comment
              The  comment  extends until the next newline (or the end of a -e
              script fragment).

       }      The closing bracket of a { } block.

   Zero- or One- address commands
       =      Print the current line number.

       a \

       text   Append text, which has each embedded newline preceded by a back‐
              slash.

       i \

       text   Insert text, which has each embedded newline preceded by a back‐
              slash.

       q [exit-code]
              Immediately quit the sed  script  without  processing  any  more
              input,  except  that  if  auto-print is not disabled the current
              pattern space will be printed.  The exit code argument is a  GNU
              extension.

       Q [exit-code]
              Immediately  quit  the  sed  script  without processing any more
              input.  This is a GNU extension.

       r filename
              Append text read from filename.

       R filename
              Append a line read from filename.  Each invocation of  the  com‐
              mand reads a line from the file.  This is a GNU extension.

   Commands which accept address ranges
       {      Begin a block of commands (end with a }).

       b label
              Branch to label; if label is omitted, branch to end of script.

       c \

       text   Replace  the  selected  lines with text, which has each embedded
              newline preceded by a backslash.

       d      Delete pattern space.  Start next cycle.

       D      If pattern space contains no newline, start a normal  new  cycle
              as  if  the d command was issued.  Otherwise, delete text in the
              pattern space up to the first newline, and  restart  cycle  with
              the  resultant  pattern  space,  without  reading  a new line of
              input.

       h H    Copy/append pattern space to hold space.

       g G    Copy/append hold space to pattern space.

       l      List out the current line in a ``visually unambiguous'' form.

       l width
              List out the current line in a  ``visually  unambiguous''  form,
              breaking it at width characters.  This is a GNU extension.

       n N    Read/append the next line of input into the pattern space.

       p      Print the current pattern space.

       P      Print  up  to  the first embedded newline of the current pattern
              space.

       s/regexp/replacement/
              Attempt to match regexp against the pattern space.  If  success‐
              ful,   replace  that  portion  matched  with  replacement.   The
              replacement may contain the special character & to refer to that
              portion  of  the  pattern  space  which matched, and the special
              escapes \1 through \9 to refer  to  the  corresponding  matching
              sub-expressions in the regexp.

       t label
              If  a  s///  has  done  a successful substitution since the last
              input line was read and since the last  t  or  T  command,  then
              branch to label; if label is omitted, branch to end of script.

       T label
              If  no  s///  has  done a successful substitution since the last
              input line was read and since the last  t  or  T  command,  then
              branch  to  label; if label is omitted, branch to end of script.
              This is a GNU extension.

       w filename
              Write the current pattern space to filename.

       W filename
              Write the first line of the current pattern space  to  filename.
              This is a GNU extension.

       x      Exchange the contents of the hold and pattern spaces.

       y/source/dest/
              Transliterate  the  characters in the pattern space which appear
              in source to the corresponding character in dest.

Addresses
       Sed commands can be given with no addresses, in which case the  command
       will  be  executed for all input lines; with one address, in which case
       the command will only be executed for  input  lines  which  match  that
       address;  or with two addresses, in which case the command will be exe‐
       cuted for all input lines which match  the  inclusive  range  of  lines
       starting  from  the first address and continuing to the second address.
       Three things to note about address ranges: the  syntax  is  addr1,addr2
       (i.e.,  the  addresses  are separated by a comma); the line which addr1
       matched will always be accepted, even if addr2 selects an earlier line;
       and  if  addr2 is a regexp, it will not be tested against the line that
       addr1 matched.

       After the address (or address-range), and before the command, a !   may
       be inserted, which specifies that the command shall only be executed if
       the address (or address-range) does not match.

       The following address types are supported:

       number Match only the specified line number (which  increments  cumula‐
              tively  across  files,  unless the -s option is specified on the
              command line).

       first~step
              Match every step'th line starting with line first.  For example,
              ``sed  -n  1~2p''  will  print all the odd-numbered lines in the
              input stream, and the address 2~5 will match every  fifth  line,
              starting  with the second.  first can be zero; in this case, sed
              operates as if it were equal to step.  (This is an extension.)

       $      Match the last line.

       /regexp/
              Match lines matching the regular expression regexp.

       \cregexpc
              Match lines matching the regular expression regexp.  The  c  may
              be any character.

       GNU sed also supports some special 2-address forms:

       0,addr2
              Start  out  in  "matched  first  address"  state, until addr2 is
              found.  This is similar to 1,addr2, except that if addr2 matches
              the very first line of input the 0,addr2 form will be at the end
              of its range, whereas the 1,addr2 form  will  still  be  at  the
              beginning of its range.  This works only when addr2 is a regular
              expression.

       addr1,+N
              Will match addr1 and the N lines following addr1.

       addr1,~N
              Will match addr1 and the lines following addr1  until  the  next
              line whose input line number is a multiple of N.

REGULAR EXPRESSIONS
       POSIX.2 BREs should be supported, but they aren't completely because of
       performance problems.  The \n sequence in a regular expression  matches
       the newline character, and similarly for \a, \t, and other sequences.

BUGS
       E-mail bug reports to bug-sed@gnu.org.  Also, please include the output
       of ``sed --version'' in the body of your report if at all possible.

AUTHOR
       Written by Jay Fenlason, Tom Lord, Ken Pizzini, and Paolo Bonzini.  GNU
       sed  home page: <http://www.gnu.org/software/sed/>.  General help using
       GNU software: <http://www.gnu.org/gethelp/>.  E-mail  bug  reports  to:
       <bug-sed@gnu.org>.   Be  sure  to include the word ``sed'' somewhere in
       the ``Subject:'' field.

COPYRIGHT
       Copyright © 2012 Free Software Foundation, Inc.   License  GPLv3+:  GNU
       GPL version 3 or later <http://gnu.org/licenses/gpl.html>.
       This  is  free  software:  you  are free to change and redistribute it.
       There is NO WARRANTY, to the extent permitted by law.

SEE ALSO
       awk(1), ed(1), grep(1), tr(1),  perlre(1),  sed.info,  any  of  various
       books on sed, the sed FAQ (http://sed.sf.net/grabbag/tutorials/sed‐
       faq.txt), http://sed.sf.net/grabbag/.

       The full documentation for sed is maintained as a Texinfo manual.  If
       the info and sed programs are properly installed at your site, the com‐
       mand

              info sed

       should give you access to the complete manual.

sed 4.2.2                        February 2016                          SED(1)
```

---

### 文本编辑常用命令

- cat -b显示行号
- ed　行文本编辑器
- fmt　重排
- gedit　
- jed　ed改进
- cut　cat改进
- colrm　
- comm
- od
- joe
- aspell　检查拼写
- col
- view　只读
- more　分屏
- head　部分显示
- cmp　文件属性比较
- diff　文件内容比较
- diffstat　
- patch
- csplit　按行数分割
- uniq　显示重复出现的行
- pr　
- pico
- tail　末尾
- tac　反序
- less
- sort　排序
- indent
- tr
- join
- spell
- tee　读取标准输入到标准输出或文件
- wc　统计文本信息

---

### 小结

说好的总结基础，最后还是复制粘贴，毕竟时间宝贵，前人总结好的东西拿来用就是了，想起来一个段子：

> 人生苦短，要学Python

