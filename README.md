

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
 - large doctests can be sourced from files
 - doctesting any files, not only Tcl


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

    # ... or this way:
    #% command41
    #> 1st line of result of command41 \
       2nd line of result of command41 \
       3rd line of result of command41

    # ... or this way:
    #% command42
    #> "  1st line of result of command42 with initial spaces" \
       "   2nd line of result of command42 with initial spaces" \
       "    3rd line of result of command42 with initial spaces"

    ############ command may be continued with "\" as its last character
    #% command-so-loooooong - begin \
    #% command-so-loooooong - cont. \
    #% command-so-loooooong - end
    #> result of command-so-loooooong

    # ... or this way:
    #% command-so-loooooong - begin \
       command-so-loooooong - cont. \
       command-so-loooooong - end
    #> result of command-so-loooooong

    #> doctest

You can have as many test blocks as you need.

If there are no *doctest quotes*, all of the text is considered as a giant test block containing *#%* and *#>* lines to be tested.

<b>Note:</b> if there is a *quoted* doctest block, any outside #% and #> lines are ignored.

The block is tested OK when all its test cases (*#%* through *#>*) result in OK. The whole doctest is considered OK when all its blocks result in OK.

Do not include into the test blocks the commands that cannot be run outside of their context (calls of external procedures etc.).

The most fit to *doctest* are the procedures with more or less complex and error-prone algorithms of pure computing. The typical though trivial example is *factorial* (its example below).

Note:
This doctest was tested under Linux (Debian) and Windows. All bug fixes and corrections for other platforms would be appreciated.


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

You can use -DTs, -DTv and -DTb instead of -s, -v and -b accordingly, to avoid intersections with arguments of other scripts.

Examples:

    tclsh doctest.tcl ~/PG/projects/pave/paveme.tcl
    tclsh doctest.tcl -v -1 ~/PG/projects/pave/paveme.tcl
    tclsh doctest.tcl -s 0 -b 2 ~/PG/projects/pave/paveme.tcl
    tclsh doctest.tcl ~/PG/projects/doctest/README.md
    tclsh doctest.tcl -b factorial ~/PG/projects/doctest/README.md


# Usage in alited


In [alited editor](https://aplsimple.github.io/en/tcl/alited), doctest is implemented by [e_menu](https://aplsimple.github.io/en/tcl/e_menu)'s tools, available in two places:

 - "Tools / bar/menu / tests.mnu: Tests" menu item
 - "T" tool bar item

When you run those items, you get "Tests" menu containing four doctest's choices:

 - Doctest Safe
 - Doctest Safe verbose
 - Doctest
 - Doctest verbose

Any of them can be applied to a current text. Also, you can select a text snippet and doctest it.

The "List of Templates" of alited allows to put a template of doctest.

The "Tests" menu contains "Help" item to view the docs of doctest.


# Usage in TKE


TKE presents a good sample how to employ <i>doctest</i> while editing Tcl modules.

[TKE editor](https://github.com/phase1geo/tke/) has its own [doctest plugin](https://github.com/phase1geo/tke/tree/master/plugins/doctest) that provides the additional facilities:

 - doctesting the selected lines of code
 - inserting the doctest template into the code
 - menu driven
 - message boxes for results
 - hotkeys for all operations

Also, you can run TKE's [e_menu plugin](https://github.com/phase1geo/tke/tree/master/plugins/e_menu) and get to <i>doctest</i> by the menu path:

    Main menu / Utils / Test1


# Usage in Geany


[Geany IDE](https://www.geany.org) can enable the Tcl doctest facilites by two ways:

   * by setting a command in Build / Build Menu Commands

   * by setting a command in Edit / Preferences / Tools / Context action

In the first case, the command to run would be sort of this:

    tclsh ~/PG/github/doctest/doctest.tcl %f

Geany's <i>Build/Compile</i> commands depend on a current file extension. Because of Tcl scripts need no building/compiling, we can set the above command for any of "Build / Build Menu Commands".

The second way is a bit more complex and related to [e_menu](https://aplsimple.github.io/en/tcl/e_menu). Details are described [here](https://aplsimple.github.io/en/tcl/e_menu/index.html#detailed_geany).

You run <i>doctest</i> from Geany's context by the following  [e_menu](https://aplsimple.github.io/en/tcl/e_menu) path:

    Main menu / Utils / Test1

A nice feature of this way is that you can set the doctest menu "on top" to have it at hand.

In contrast to TKE, you cannot doctest a selected text of file edited by Geany. So, the whole edited file can be only doctested.


# Tips and traps


<b>Please, note again:</b>

Do not include into the test blocks the commands that cannot be run or are unavailable (calls of external procedures etc.).

***

If the whole of module should be spanned for doctesting, do not use *#% doctest* quotes.

Use only *#% command, %> result* at that.

***

If the last *#% doctest* quote isn't paired with *#> doctest* quote, the test block continues to the end of text.

The middle unpaired *#% doctest* and the unpaired *#> doctest* are considered to be errors making the test impossible.

***

Results of commands are checked literally, though the starting/tailing spaces of *#%* and *#>* lines are ignored.

If a command's result should contain starting/tailing spaces, it should be quoted with double quotes. The following `someformat` command

    #% someformat 123
    #> "  123"

should return <code>"  123"</code> for the test to be OK.

The following two tests are identical and both return the empty string:

    #% set _ ""  ;# test #1
    #> ""
    #% set _ ""  ;# test #2
    #>

The absence of resulting *#>* means that a result isn't important (e.g. for GUI tests) and no messages are displayed in non-verbose doctest.

***

If a doctest body is large, it can be moved to a separate file to be sourced with a comment:

    #% doctest source testedfile.test

where *doctest source* may be of any case (Doctest Source, DOCTEST SOURCE etc.), *testedfile.test* contains the doctest body. Thus a code isn't cluttered with a doctest body. See e.g. *obbit.tcl* and its sourced *tests/obbit_1.test* in [pave](https://github.com/aplsimple/pave).

***

When you have a doctest to run an external application, e.g.

    #% doctest
    #% exec tclsh my-module1.tcl "this-data-file" arg11 arg12 arg13
    #> doctest

do not forget about the <code>#% doctest</code> and <code>#> doctest</code> lines. They are important in order to detach an external call from the rest of text. Otherwise you could get an error message (if you set <code>#> result</code> to see what's wrong with the call), like this:

    GOT:
    "can't read "::argv0": no such variable"

    WAITED:
    "result"

This one means that the rest of text (being evaluated as a Tcl snippet, not a module run in tclsh) can't get to the Tcl argument list.

***

Run the doctest on this README.md

    tclsh doctest.tcl ./README.md

to see how it works.

This thing might be helpful, namely: the doctest's usage isn't restricted with a code. Any data file, that permits '#' or multi-line comments, may include the doctest strings for testing its contents, e.g. through something like:

    #% doctest
    #% exec tclsh my-module1.tcl "this-data-file" arg11 arg12 arg13
    #% exec tclsh my-module2.tcl "this-data-file" arg21 arg22 arg23
    ...
    #> doctest

...or

    #% doctest
    #% exec my-application1 "this-data-file" arg11 arg12 arg13
    #% exec my-application2 "this-data-file" arg21 arg22 arg23
    ...
    #> doctest

... so that, while editing this data file, you can periodically run the doctest on it to check if the data are OK.

<b>Note again:</b> If there is a *quoted* doctest block, any outside #% and #> lines are ignored. And vice versa, if there is no *quoted* doctest block, all of text is considered as a giant doctest block.

<b>As a result:</b> The <code>#% doctest</code> and <code>#> doctest</code> lines are important! You must detach the doctest blocks from the rest of text. Otherwise, all of the text would be evaluated as Tcl code, which won't highly likely be that you want.

You can define a whole set of testing application(s) in block(s) to run all of them at modifying the data.


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


The doctest pages:

>  [code](https://chiselapp.com/user/aplsimple/repository/doctest/download)

>  [description](https://aplsimple.github.io/en/tcl/doctest)

>  [github repo](https://github.com/aplsimple/doctest)

>  [chiselapp repo](https://chiselapp.com/user/aplsimple/repository/doctest)

See also:

> [alited editor](https://aplsimple.github.io/en/tcl/alited)

> [TKE editor](https://github.com/phase1geo/tke)

> [Geany IDE](https://www.geany.org)

> [e_menu](https://aplsimple.github.io/en/tcl/e_menu)
