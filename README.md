
# Background

`perl` is famously cryptic and many loathe it because of its large number of implicit behaviors.  Contrast this with `python`, which strives to do nothing implicit by default.

I spent ten years with `perl` as my weapon of choice.  However, in 2007, I migrated 100% to `python` as my primary langauge, and forgot most of my `perl` chops.  Yet, fifteen years down that road, I've concluded that `perl` should have a place in my life... at least for some tasks (such as text manipulation and munging).

This repo will keep notes about `perl` use-cases.

# Use-case 1: Find and replace with `perl`

## Introduction

Let's do a common task with `perl`, find and replace text.

## Use-case-01: Find and replace text with perl

To illustrate, lets use `perl` to open a file and replace all occurances of 'free' with 'TREE'.  Consider EXAMPLE_SCRIPT_01_01...

```perl
$ # EXAMPLE_SCRIPT_01_01: Find and replace with perl
$ perl -pi.bak -e 's/free/TREE/;' my_file.txt
```

That's pretty short, which is good for now.  Find and replace shouldn't be a complicated task.

Let's dissect EXAMPLE_SCRIPT_01_01...

- The command-line flags are important...
  - The `-p` flag tells `perl` to implicitly loop over all lines in `my_file.txt`; each looped line will be assigned to a magic variable called `$_`.
  - The `-i.bak` flag tells `perl` to implicitly edit the `my_file.txt` in-place, and back up the original with a `.bak` extension.  The `-i` flag MUST have an extension or no backup is created.
  - The `-e` flag tells `perl` to execute the quoted command-line script

Now, consider EXAMPLE_SCRIPT_01_02.  With NO implicit behavior, EXAMPLE_SCRIPT_01_01 becomes...

```perl
$ # EXAMPLE_SCRIPT_01_02: Find-and-replace with perl
$ perl -e '`cp my_file.txt my_file.txt.bak`; open $fhin, "<", "my_file.txt"; open $fhout, ">", "my_file.txt.new"; foreach my $line (<$fhin>) { chomp $line; $line =~ s/free/TREE/g; print $fhout "$line\n"; } `mv my_file.txt.new my_file.txt`;'
```

I'll reformat it for our review...

```perl
# EXAMPLE_SCRIPT_01_02: Find-and-replace with perl
# WARNING this script is intentionally verbose to highlight perl's shortcuts
`cp my_file.txt my_file.txt.bak`;      # backup my_file.txt

open $fhin, "<", "my_file.txt";        # read from my_file.txt into $fhin
open $fhout, ">", "my_file.txt.new";   # write to my_file.txt.new with $fhout

foreach my $line (<$fhin>) {           # loop over $fhin
    chomp $line;                       # strip off new-lines in $line
    $line =~ s/free/TREE/g;            # replace 'free' with 'TREE'
    print $fhout "$line\n";            # write the modified line into $fhout
}

`mv my_file.txt.new my_file.txt`;
```

Now let's look back at EXAMPLE_SCRIPT_01_01...

```perl
$ # EXAMPLE_SCRIPT_01_01: Find-and-replace with perl
$ perl -pi.bak -e 's/free/TREE/;' my_file.txt
```

If I had to fix a file quickly, I'd DEFINITELY use EXAMPLE_SCRIPT_01_01.  However, if I had to maintain something for a long time, the verbosity in EXAMPLE_SCRIPT_01_02 is my choice.

# Use-case 2: Delete all files matching a regular expression with `perl`

```perl
unlink( grep { /\W\d+\.\d+/ } glob "*");
```
