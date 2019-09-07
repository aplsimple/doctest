

# What is this?


This allows you to doctest Tcl scripts.

To say shortly, the doctests are special comments inserted directly into your Tcl code. The doctest blocks are closely related to the code of module and used for testing and documenting it. You run this doctest on them and get the results of testing (OK or FAILED). Just so simple.

This allows you to keep your code in a working state each time you modify it.

The features of this doctest:

 - a full body of Tcl module can be used for doctesting
 - selected named blocks can be used for doctesting
 - several commands can produce one result to be checked
 - the results and commands can be multi-lined (continued with \)
 - the doctest blocks can contain several results to be checked
 - the block result is estimated OK if all its results are OK
 - the doctesting can be performed in safe/unsafe interpreter
 - the outputs modes are verbose, short or silent
 - only 'silent' output mode means 'hide OK, show FAILED if any'


# How to do this?


The test blocks include the test examples concerning the current script and are *quoted* with the following *doctest-begin* and *doctest-end* Tcl comments:

    #% doctest
    ... (tested code) ...
    #> doctest

The commands of `... (tested code) ...` are marked with *#%* and are followed with their results that are marked with *#>*. For example:

    # these two lines are a command and its result
    #% somecommand
    #> "result of somecommand"

So, we place the commands and their results between *doctest quotes*. Let us see how to do it:

    #% doctest (put here any title/name/number/comment)
  
    ############ here we have two commands and their waited results
    ############ (note how a command/result begins and ends)
    #% command1
    #> result of command1
    #% command2
    #> result of command2
  
    ############ command33 needs command31, command32 to be run before
    ############ (their results are ignored if not raising exceptions):
    #% command31
    #% command32
    #% command33
    #> result of command33
  
    ############ command4 returns a multiline result
    ############ (in particular, you should use this when the test raises
    ############ an exception so that you copy-paste it as the waited result)
    #% command4
    #> 1st line of result of command4
    #> 2nd line of result of command4
    #> 3rd line of result of command4
  
    ############ command may be continued with "\" as its last character.
    #% command-so-loooooong - begin \
    #% command-so-loooooong - cont. \
    #% command-so-loooooong - cont. \
    #% command-so-loooooong - end
    #> result of command-so-loooooong
  
    #> doctest

You can have as many test blocks as you need. If there are no *doctest quotes*, all of the text is considered as a giant test block containing *#%* and *#>* lines to be tested.

The block is tested OK when all its test cases (*#%* through *#>*) result in OK. The whole doctest is considered OK when all its blocks result in OK.

Do not include into the test blocks the commands that cannot be run outside of their context (calls of external procedures etc.).

The most fit to *doctest* are the procedures with more or less complex and error-prone algorithms of pure computing. The typical though trivial example is *factorial* (its example below).

Note:
This doctest was tested under Linux (Debian) and Windows. All bug fixes and corrections for other platforms would be appreciated.


# Tips and traps


PLEASE, NOTICE AGAIN: Do not include into the test blocks the commands that cannot be run or are unavailable (calls of external procedures etc.).

If the whole of module should be spanned for doctesting, do not use *#% doctest* quotes. Use only docstrings (#% command, %> result) at that.

If the last *#% doctest* quote isn't paired with lower *#> doctest* quote, the test block continues to the end of text.

The middle unpaired *#% doctest* and the unpaired *#> doctest* are considered as errors making the test impossible.

Results of commands are checked literally, though the starting and tailing spaces of *#%* and *#>* lines are ignored.

If a command's result should contain starting/tailing spaces, it should be quoted with double quotes. The following `someformat` command

    #% someformat 123
    #> "  123"

should return "  123" for the test to be OK.

The following two tests are identical and both return the empty string:

    #% set _ ""  ;# test #1
    #> ""
    #% set _ ""  ;# test #2
    #>

The absence of resulting *#>* means that a result isn't important (e.g. for GUI tests) and no messages are displayed in non-verbose doctest.

At that, when "exec" is used the "&" might be fit at the end of command:

    #% exec wish ./GUImodule.tcl arg1 arg2 &
    # ------ no result is waited here ------

Of course, "exec" and similar commands need executing the doctest in unsafe interpreter (see "Usage" below).

NOTE: the successive *#%* commands form the suite with the result returned by the last command. See the example of command31-32-33 suite above.

A tested command can throw an exception considered as its normal result under some conditions. See the example below.

Run the doctest on this text to see how it works.

This thing might be helpful, namely: the doctest's usage isn't restricted with a code. A data file allowing #- or multi-line comments, might include the doctest strings for testing its contents, e.g. through something like:

    #% exec tclsh module.tcl this_datafile.txt


# Usage


To run the doctest, use the command:

    tclsh doctest.tcl ?options? filename

where:

    filename - path to Tcl source file
    options:
      -s 1 | 0      - safe (default) | unsafe interpreter execution
      -v 1 | 0 | -1 - verbose (default) | short | silent mode
      -b block      - name of block to be tested
      --            - switches options off (for filename)

The -b option may be repeated. If -b omitted, all of the file is checked for the doctest blocks to execute.

Examples:

    tclsh doctest.tcl ~/PG/projects/pave/paveme.tcl
    tclsh doctest.tcl -v -1 ~/PG/projects/pave/paveme.tcl
    tclsh doctest.tcl -s 0 -b 2 ~/PG/projects/pave/paveme.tcl
    tclsh doctest.tcl ~/PG/projects/doctest/README.md
    tclsh doctest.tcl -b factorial ~/PG/projects/doctest/README.md


# Examples


Though being trivial, the factorial procedure should check some conditions to return a proper result.

    #% doctest factorial

    ############## Calculate factorial of integer N (1 * 2 * 3 * ... * N)
    proc factorial {i} {
      if {$i<0} {        ;# btw checks if i is a number
        throw {ARITH {factorial expects a positive integer}} \
        "expected positive integer but got \"$i\""
      }
      if {"$i" eq "0" || "$i" eq "1"} {return 1}
      return [expr {$i * [factorial [incr i -1]]}] ;# btw checks if i is integer
    }
    #% factorial 0
    #> 1
    #% factorial 1
    #> 1
    #% factorial 10
    #> 3628800
    #% factorial 50
    #> 30414093201713378043612608166064768844377641568960512000000000000
    #
    # (:=test for test:=)
    #% expr 1*2*3*4*5*6*7*8*9*10*11*12*13*14*15*16*17*18*19*20* \
    #%      21*22*23*24*25*26*27*28*29*30*31*32*33*34*35*36*37*38*39*40* \
    #%      41*42*43*44*45*46*47*48*49*50
    #> 30414093201713378043612608166064768844377641568960512000000000000
    #% expr [factorial 50] == \
    #%      1*2*3*4*5*6*7*8*9*10*11*12*13*14*15*16*17*18*19*20* \
    #%      21*22*23*24*25*26*27*28*29*30*31*32*33*34*35*36*37*38*39*40* \
    #%      41*42*43*44*45*46*47*48*49*50
    #> 1
    # (:=do not try factorial 1000, nevermore, the raven croaked:=)
    #
    #% factorial 1.1
    #> expected integer but got "1.1"
    #% factorial 0.1
    #> expected integer but got "0.1"
    #% factorial -1
    #> expected positive integer but got "-1"
    #% factorial -1.1
    #> expected positive integer but got "-1.1"
    #% factorial abc
    #> expected integer but got "abc"
    #% factorial
    #> wrong # args: should be "factorial i"
    #> doctest


Another example could make you smile:

    #% doctest 1
    #%   set a "123 \\\\\\\\ 45"
    #%   eval append b {*}$a   ;# guten Appetit
    #>   123\45
    #> doctest


# Links


The home page:

>  [doctest](https://aplsimple.github.io/en/tcl/doctest)

TKE editor (written in Tcl/Tk, tremendous tool for Tclers):

>  [TKE editor](https://sourceforge.net/projects/tke/)

TKE editor has its own doctest plugin that provides the additional facilities:

 - doctesting the selected lines of code
 - inserting the doctest template into the code
 - menu driven
 - message boxes for results
 - hotkeys for all operations

TKE sets an example how to employ the doctest while editing Tcl modules.
