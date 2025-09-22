---
name: Zsh
filename: LearnZsh.sh
contributors:
    - ["imaPheven", "https://github.com/imaPheven"]
---

Z-shell "zsh" for short is a command interpreter for UNIX systems and popular
in linux distros. While similar to bash it adds advanced features and conveniences.

``` zsh
#!/usr/bin/env zsh
# Shebang tells the system to use zsh. Comments start with #

print "Hello from zsh"   # zsh has print (like echo but with predictable escapes)
echo  "Hello from zsh"   # echo also exists

# Commands can be on separate lines, or separated by ;
print first; print second

# ==== Variables & Parameters ====
name="Zed"
print $name        # => Zed
print "$name"      # quote to preserve spaces
print '$name'      # => $name (no expansion)


# Command Substitution $(...)
# Run a command and substitute it's output

# Run date and print it in the string
print "Today is $(date)"
# list the files in the directory and store as array
files=($(ls))
# Print the first file
print "First file: ${files[1]}"
# => Today is Wed Sep 17 18:36:18 2025
# => First file: CONTRIBUTING.md

# Parameter expansion and useful modifiers:
file="/var/log/nginx/error.log"
print ${file:h}
print ${file:t}
print ${file:r}
print ${file:e}
# dirname => /var/log/nginx
# basename => error.log
# root     => /var/log/nginx/error
# ext      => log

##  Default/alternate values
# prints fallback if $foo unset or empty
print ${foo:-"fallback"}   
# sets foo if unset, then prints it
print ${foo:="set-and-print"}  

## Substrings & length
s="abcdefg"
# Print length of string
print ${#s}        # => 7
# Slice by index range
print ${s[2,4]}    # => bcd

## ==== Options (setopt) & safer scripting ====
# Prefer these in scripts for safety:
setopt err_return           # return from functions on error
set -o errexit              # exit on command error
set -o nounset              # error on unset variables
set -o pipefail             # fail a pipeline if any command fails
setopt no_unset             # ensure ${undefined} errors out

## You can sandbox option changes inside a function with emulate:
myfn() {
  emulate -L zsh   # localize options; start from clean zsh defaults
  setopt extendedglob
  print "In myfn, extendedglob is on: ${(t)options[extendedglob]}"
}

## ==== Arrays & Associative arrays ====
# NOTE: arrays are 1-indexed by default. Enable ksh style if you prefer 0-index:
# setopt KSH_ARRAYS  # uncomment to make arrays 0-indexed

arr=(alpha beta gamma)
# Print first item
print ${arr[1]}          # => alpha
# Print all items
print ${arr[*]}          # => alpha beta gamma
# Digression on subscripts
print ${arr[-1]}         # => gamma
# Print length
print ${#arr}            # number of elements => 3
for x in $arr; do print $x; done
# Print a slices from array
print ${arr[2,3]}        # => beta gamma

# Associative arrays
typeset -A ages
ages=(alice 30 bob 28)
print ${ages[alice]}     # => 30
for k v in ${(kv)ages}; do print "$k -> $v"; done

# ==== Arithmetic ====
print $(( 10 + 5 ))      # => 15
integer i=42; (( i+=1 )); print $i

# ==== Globbing (patterns) ====
# Zsh has very powerful globbing. Turn on extended globbing to unlock more patterns:
setopt extendedglob
# all markdown files
print -- *.md
# everything EXCEPT .md or .txt   
print -- ^(*.md|*.txt)
# recursive (globstar is built in)
print -- **/*.md
# regular files only
print -- *(.)
# 5 most recent regular files (o=sort by mtime, m=descending)
print -- *(.)om[1,5]

# ==== Glob qualifiers ==== (after a pattern in parentheses):
# (.) file, (/) dir, (@) symlink, (X) executable, (Lk) size, (om) sort by mtime, etc.
## Expanded example
# The variable $fpath has for directories and we want to cat the 6th 
# file in 4th dirctory
cat $fpath[4]/*(.N[6])
# Explanation
# - `$fpath[4]`  = 4th file
# - `*`          = all entries
# - `(.N[6])`    = qualifiers
#     - `.`      = regular files
#     - `N`      = null-glob (ignore errors if no matches)
#     - `[6]`    = 6th match in the list

# ==== Regex matching ====
# [[ string =~ regex ]] works; matches populate $MATCH and $match array.
str="user@example.com"
if [[ $str =~ '^[^@]+@[^@]+\.[^@]+$' ]]; then
  print "looks like an email"
  print $MATCH           # full match
fi

# ==== Conditionals & tests ====
read -r who
if [[ -z $who ]]; then
  print "empty"
elif [[ $who == (Zed|Zoe) ]]; then  # pattern match with parentheses/alternation
  print "hi $who"
else
  print "hello, $who"
fi

# File tests: -e exists, -f regular file, -d directory, -x executable

# ==== Case/switch ====
case $TERM in
  (xterm*|rxvt*) print "xterm family" ;;
  (*)            print "other" ;;
esac

# ==== Loops ====
for n in {1..3}; do print $n; done
for f in **/*.log(.om[1,3]); do print $f; done   # 3 newest .log files

# C-style
for ((i=1; i<=3; i++)); do print $i; done

# while / until
i=0; while (( i < 3 )); do print $((i++)); done

# ==== Functions ====
mygreet() { 
  # Numerical parameter reference
  print "hello $1";
  # length of arguments and arguments as array
  print $# $*;
  # :u => uppercase, ${^@} => expand each arg
  print ${^@:u};
}

mygreet world
# => hello my
# => 3 my fun example
# => MY FUN EXAMPLE

# Return status & trapping
f() { false } ; f || print "f failed"
TRAPINT() { print "Ctrl-C!"; return 130 }

# Autoloadable functions (from $fpath)
autoload -Uz myhelper  # will load myhelper from $fpath upon first use

# ==== I/O redirection & here-docs ====
cat > hello.py <<'EOF'
#!/usr/bin/env python3
print("hello")
EOF
python3 hello.py >out.log 2>err.log
: > empty.file         # builtin null command creates/truncates file

# Process substitution
print <(print foo)     # shows a /dev/fd path
cat <(print one) <(print two)

# Tee and pipelines (with pipefail set earlier)
print one two three | tee list.txt | wc -w

# ==== Jobs & background ====
sleep 5 &            # run in background
jobs                 # list jobs
fg %1                # bring job 1 to foreground
kill %1              # kill job 1

# ==== Completion (interactive) ====
autoload -Uz compinit; compinit   # enable completion in interactive shells
# (Usually done in ~/.zshrc)

# ==== Modules ====
# Load zmodload features when needed
zmodload zsh/regex    # enhanced regex functions
zmodload zsh/mapfile  # map files to parameters

# ==== Useful builtins ====
hash -d proj=~/projects   # named directory; cd ~proj/src
whence -v ls              # where a command resolves
print -l *.md             # one per line

# ==== Script tips ====
# - Use /usr/bin/env zsh shebang.
# - Prefer $(...) over backticks.
# - Remember arrays are 1-indexed unless KSH_ARRAYS is set.
# - Use emulate -L zsh in functions to localize options.
# - Set safety options (errexit/nounset/pipefail, err_return) near the top of scripts.

# Detect shell at runtime
if [[ -n ${ZSH_VERSION-} ]]; then
  print "running under zsh $ZSH_VERSION"
fi
````

For more, see the [Zsh Documentation](https://zsh.sourceforge.io/Guide/zshguide.html)
