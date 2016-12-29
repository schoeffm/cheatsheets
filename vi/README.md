## Vim Cheatsheet/

### Moving the cursor around
- __`w`__ / __`W`__: Forward by word 
- __`b`__ / __`B`__: Backward by word 
- __`f{char}`__ /__`,`__ /  __`;`__: Moves the cursor to the first occurence of the given character. If there are more than one matching character in the current line use `,` and `;` to move back and forth
- __`$`__: To end of line
- __`0`__: To beginning of line 
- __`n|`__: Column n of current line
- __`%`__: Finds the matching parathesis and jump to it (no matter if back or forth)
- __`gj`__ / __`gk`__: One display line down/up (even when the real line spans several display lines)
- __`g0`__ / __`g$`__: To start/end of display line 

### Paging / Scrolling
- __`^F`__ / __`^D`__: Scroll forward one screen / half screen.
- __`^B`__ / __`^U`__: Scroll backward one screen / half screen.
- __`z ENTER`__: Move current line to top of screen and scroll.
- __`zz`__: Move current line to center of screen and scroll.
- __`200z ENTER`__: moves line 200 to the top of the screen.

### Editing / Content modification
- __`i`__ / __`I`__: Insert text at current position / at beginning of line
- __`a`__ / __`A`__: Append text at current position / at end of line
- __`o`__ / __`O`__: Open new line below/above cursor for new text
- __`J`__: Join current and next line
- __`.`__: Repeat last action (__`!`__)
- __`u`__: Undo last change
- __`U`__: Restore line to original state
- __`p`__ / __`P`__: Put deleted text after / before cursor
- __`yw`__: Yank (copy) word
- __`yy`__: Yank current line
- __`D`__ / __`dd`__: clear line (from cursor-pos 'till the end) / delete line completely
- __`x`__ / __`X`__: Delete character under cursor / before cursor
- __`dw`__: Delete word
- __`cw`__: Change word (same as `dw` but after deletion switches to insert-mode)
- __`r`__ / __`R`__: replace the character under the cursor / replace everything from here until <esc>
- __`s`__: substitude the character under the cursor with the char-sequence which is entered (so, a combination of `r` and `R`)

### Macro handling
_Use macros when you have to apply repetitive things to several lines!_

- __`qa`__: start recording a macro and assign it to register __a__
    - the status-line states _recording_
    - __`q`__: stops recording
- __`@a`__: applies the macro saved in register __a__
    - __`:'<,'>normal @a`__: applies the macro to the current selection (see visual mode)

### ex Commands
They're __always__ triggered via a `:`-sign and are much more powerful than simple vi-commands.
You should know at least some of them ...  <br/>
To leverage the full power of __`ex`__-Commands you'll have to know adressing:

#### Adressing lines/areas
- __`1,$`__: All lines in the file
- __`x,y`__: Lines x through y
- __`x;y`__: Lines x through y, with current line reset to x
- __`0`__ / __`.`__ / __`$`__: Top of file / Current line / Last line in file
- __`%`__: All lines; same as 1,$
- __`n`__: Absolute line number n

#### Commands (in combination with adressing)
- __`:copy`__ / __`:co`__ / __`:t`__: Copy lines (these commands are equivalent)
    - i.e. __`:2,5copy.`__ copies line 2-5 to the current position
    - i.e. __`:5t10`__ copies line 5 to line 10
    - i.e. __`:.,/pattern/co$`__: Copy from current line through line containing _pattern_ and puts this result to end of file
- __`:e FILE_2`__: Open FILE_2 for editing without leaving vi
- __`:delete`__ / __`:d`__: Delete the given lines
    - i.e. __`:.,$d`__: Delete from current line to end of file
- __`:move`__ / __`:m`__: Move (several contiguous) lines
    - i.e. __`:5,10m.`__ Move line 5-10 to the current position
    - i.e. __`:30,60m0`__ Move lines 30 through 60 to top of file
    - __`226,$m.-2`__: Move lines 226 through the end of the file to two lines above the current line.
- __`:write`__ / __`:w`__: Write to a new/given File
    - i.e. __`:30,60w NEWFILE`__: Write from line 30 through line 60 as newfile
    - i.e. __`:30,60w>> FILE`__: Write from line 30 through line 60 and append to file

#### Folding/Unfolding Areas
_Actually also an ex-command there are some explicit mnemonics for folding/unfolding_

- __`zf`__: Folds the selected lines and collapses 'em (you'll have to select a bunch of lines via visual mode) 
- __`zo`__ / __`zO`__: Opens the current folding / does so recusively
- __`zc`__ / __`zC`__: Closes the current folding (if one exists) 
- __`zd`__: Deletes the current folding-definition (if there is one)
- __`[z`__ / __`]z`__: Jump to the start / end of the current folding-definition

#### Substitutions
The general form of this command is `:[addr1[,addr2]]s/old/new/[flags]` whereas the flags `g` (global), `c` (confirm - every substition) and `p` (print) are possible _(The g option affects each pattern on a line; don't confuse it with the :g command, which affects each line of a file.)_

- __`:s/old/new/g`__: changes every occurrence of old to new on the __current line__, 
     not just the first occurrence (the `g` option)
- __`:1,$s/old/new/g`__: This command will change every occurrence of old to new within the entire 
     file (use `n,m` instead to narrow down the affected area). ___You can also use % instead of 1,$___
- __`:g/pattern/s/old/new/g`__: The first `g` tells the command to operate on all lines of a file. 
      _pattern_ identifies the lines on which a substitution is to take place. <br/>

Syntax-Foo: `:g/editer/s//editor/g` <=> __`:%s/editer/editor/g`__ (___this will change all occurences in the file___)

#### call external programms 
- __`:!ls -lahtr`__: executes the given command and then returns back to your vi session
- __`:96,99!sort`__: will pass lines 96 through 99 through the sort filter and replace those lines with the output of sort

### Tabbing und Windows 
- __`:tabnew FILENAME`__: Open a new tab and edit a file (optional). If no file is specified Vim opens a new tab with an empty buffer. 
- __`:tabclose`__: Close the current tab (or simply use __`:q!`__). 
- __`:tabonly`__: Close all other tabs. 
- __`gt`__ / __`2gt`__: go to next tab / go to 2nd tab
- __`CTRL+w +s`__ / __`CTRL+w +v`__: Splits the screen horizontally / vertically
- __`CTRL+w +w`__: Cycle between open windows
- __`CTRL+w +c`__: Closes the active window
- __`CTRL+w +o`__: Keep only the active window (close all others).


### Mixed stuff
- __`vi scp://user:password@host:/path`__: opens a remote file for editing in the locale installation of vi
- __`:g/search_term/#`__: display each line containing __search_term__ with line numbers
- __`:r !date`__: will insert the current date/time stamp (more generally the output from the command)
- __`:set shiftwidth=4 softtabstop=4 expandtab`__:

_If we want to run a Normal mode command on a series of consecutive lines, we can do so using the :normal command._

- __`:%normal A;`__: Appends a semicolone to the end of every line
- __`:4,20normal i//`__: Inserts comment-signs at the beginning of line 4-20


