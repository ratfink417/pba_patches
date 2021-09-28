# GDB commands by function - simple guide
More important commands have a (\*) by them.

## Startup

% gdb -help print startup help, show switches

**% gdb object normal debug**

**% gdb object core core debug (must specify core file)**

% gdb object pid attach to running process

% gdb use file command to load object

## Help

**(gdb) help list command classes**

_(gdb)_ help running list commands in one command class

_(gdb)_ help run bottom-level help for a command "run"

_(gdb)_ help info list info commands (running program state)

_(gdb)_ help info line help for a particular info command

_(gdb)_ help show list show commands (gdb state)

_(gdb)_ help show commands specific help for a show command

## Breakpoints

**(gdb) break main set a breakpoint on a function**

**(gdb) break 101 set a breakpoint on a line number**

**(gdb) break basic.c:101 set breakpoint at file and line (or function)**

**(gdb) info breakpoints show breakpoints**

**(gdb) delete 1 delete a breakpoint by number**

_(gdb)_ delete delete all breakpoints (prompted)

_(gdb)_ clear delete breakpoints at current line

_(gdb)_ clear function delete breakpoints at function

_(gdb)_ clear line delete breakpoints at line

_(gdb)_ disable 2 turn a breakpoint off, but don't remove it

_(gdb)_ enable 2 turn disabled breakpoint back on

_(gdb)_ tbreak function|line set a temporary breakpoint

_(gdb)_ commands break-no ... end set gdb commands with breakpoint

_(gdb)_ ignore break-no count ignore bpt N-1 times before activation

_(gdb)_ condition break-no expression break only if condition is true

_(gdb)_ condition 2 i == 20 example: break on breakpoint 2 if i equals 20

_(gdb)_ watch expression set software watchpoint on variable

_(gdb)_ info watchpoints show current watchpoints

## Running the program

**(gdb) run run the program with current arguments**

**(gdb) run args redirection run with args and redirection**

_(gdb)_ set args args... set arguments for run

_(gdb)_ show args show current arguments to run

\*_(gdb)_ cont continue the program

\*_(gdb)_ step single step the program; step into functions

_(gdb)_ step count singlestep \fIcount\fR times

**(gdb) next step but step over functions**

**(gdb) next count next \fIcount\fR times**

**(gdb) CTRL-C actually SIGINT, stop execution of current program**

**(gdb) attach process-id attach to running program**

**(gdb) detach detach from running program**

**(gdb) finish finish current function's execution**

**(gdb) kill kill current executing program**

## Stack backtrace

**(gdb) bt print stack backtrace**

_(gdb)_ frame show current execution position

_(gdb)_ up move up stack trace (towards main)

_(gdb)_ down move down stack trace (away from main)

**(gdb) info locals print automatic variables in frame**

_(gdb)_ info args print function parameters

## Browsing source

**(gdb) list 101 list 10 lines around line 101**

**(gdb) list 1,10 list lines 1 to 10**

**(gdb) list main list lines around function**

**(gdb) list basic.c:main list from another file basic.c**

**(gdb) list - list previous 10 lines**

_(gdb)_ list \*0x22e4 list source at address

_(gdb)_ cd dir change current directory to \fIdir\fR

_(gdb)_ pwd print working directory

_(gdb)_ search regexpr forward current for regular expression

_(gdb)_ reverse-search regexpr backward search for regular expression

_(gdb)_ dir dirname add directory to source path

_(gdb)_ dir reset source path to nothing

_(gdb)_ show directories show source path

## Browsing Data

**(gdb) print expression print expression, added to value history**

**(gdb) print/x expressionR print in hex**

_(gdb)_ print array[i]@count artificial array - print array range

_(gdb)_ print $ print last value

_(gdb)_ print \*$->next print thru list

_(gdb)_ print $1 print value 1 from value history

_(gdb)_ print ::gx force scope to be global

_(gdb)_ print 'basic.c'::gx global scope in named file (>=4.6)

_(gdb)_ print/x &main print address of function

_(gdb)_ x/countFormatSize address low-level examine command

_(gdb)_ x/x &gx print gx in hex

_(gdb)_ x/4wx &main print 4 longs at start of \fImain\fR in hex

_(gdb)_ x/gf &gd1 print double

_(gdb)_ help x show formats for x

**(gdb) info locals print local automatics only**

_(gdb)_ info functions regexp print function names

_(gdb)_ info variables regexp print global variable names

**(gdb) ptype name print type definition**

_(gdb)_ whatis expression print type of expression

**(gdb) set variable = expression assign value**

_(gdb)_ display expression display expression result at stop

_(gdb)_ undisplay delete displays

_(gdb)_ info display show displays

_(gdb)_ show values print value history (>= gdb 4.0)

_(gdb)_ info history print value history (gdb 3.5)

## Object File manipulation

_(gdb)_ file object load new file for debug (sym+exec)

_(gdb)_ file discard sym+exec file info

_(gdb)_ symbol-file object load only symbol table

_(gdb)_ exec-file object specify object to run (not sym-file)

_(gdb)_ core-file core post-mortem debugging

## Signal Control

_(gdb)_ info signals print signal setup

_(gdb)_ handle signo actions set debugger actions for signal

_(gdb)_ handle INT print print message when signal occurs

_(gdb)_ handle INT noprint don't print message

_(gdb)_ handle INT stop stop program when signal occurs

_(gdb)_ handle INT nostop don't stop program

_(gdb)_ handle INT pass allow program to receive signal

_(gdb)_ handle INT nopass debugger catches signal; program doesn't

_(gdb)_ signal signo continue and send signal to program

_(gdb)_ signal 0 continue and send no signal to program

## Machine-level Debug

_(gdb)_ info registers print registers sans floats

_(gdb)_ info all-registers print all registers

_(gdb)_ print/x $pc print one register

_(gdb)_ stepi single step at machine level

_(gdb)_ si single step at machine level

_(gdb)_ nexti single step (over functions) at machine level

_(gdb)_ ni single step (over functions) at machine level

_(gdb)_ display/i $pc print current instruction in display

_(gdb)_ x/x &gx print variable gx in hex

_(gdb)_ info line 22 print addresses for object code for line 22

_(gdb)_ info line \*0x2c4e print line number of object code at address

_(gdb)_ x/10i main disassemble first 10 instructions in \fImain\fR

_(gdb)_ disassemble addr dissassemble code for function around addr

## History Display

_(gdb)_ show commands print command history (>= gdb 4.0)

_(gdb)_ info editing print command history (gdb 3.5)

_(gdb)_ ESC-CTRL-J switch to vi edit mode from emacs edit mode

_(gdb)_ set history expansion on turn on c-shell like history

_(gdb)_ break class::member set breakpoint on class member. may get menu

_(gdb)_ list class::member list member in class

_(gdb)_ ptype class print class members

_(gdb)_ print \*this print contents of this pointer

_(gdb)_ rbreak regexpr useful for breakpoint on overloaded member name

## Miscellaneous

_(gdb)_ define command ... end define user command

**(gdb) RETURN repeat last command**

**(gdb) shell command args execute shell command**

**(gdb) source file load gdb commands from file**

**(gdb) quit quit gdb**

# Metadata
**TAGS:**
#UsefulCommands  #Linux #Programming #Binary_Analysis
