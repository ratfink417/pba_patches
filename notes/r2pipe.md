# Radare2 python interface
[Radare2](Radare2.md) includes a single function called ``` r_core_cmd_string ``` in it's source that accepts a string and performs debugger/radare type action and returns the shit you see on the screen when you are in an r2 session and interacting with it. It's a pipe and thus the namesake **r2pipe**. So this being a nice, simple interface into some of the inner workings of radare2 that most users care about encouraged other users to _do the needful_ and create bindings from their favorite languages to this function.

Since python has the most support amongst scripting languages, that's gonna be our horse.

# Basic scripting
r2pipe usage will closely mirror what you would do in an r2 session which makes this really easy for us. The first thing you're going to need to do is import modules for r2pipe and any modules used for getting folder paths and opening processes so... _sys_ and _os_ will need to be in here too.

This example demonstrates what you'll most likely end up doing to open up a binary ```/bin/ls``` in our case and just get some plain text output. That you can do other cool python stuff with.

```python
import r2pipe
import sys
import os

r2 = r2pipe.open("/bin/ls")

r2.cmd('aaa')
r2.cmd('s main')
print(r2.cmd(pdf))
```
There you have it, all we did was open a process, run the ```aaa``` command, moved the seek to the _main_ function and printed the disassembly for that function.

## Sending Input
During an interactive session with the debugger, there are lots of times that you'll end up needing to supply user input to the program you're observing. r2pipe on it's own has no way of letting you do that but similar to gdb, you can front load some of the user input.

To do this, you're gonna need to use the **rarun2** utility that comes with radare2. rarun2 reads a profile you put together which will be used to tell radare about how the session should be set up. Things like arguments to the binary being debugged or some system stuff you might want done like changing an environment variable or turning off ASLR for the session.

To demonstrate this, compile a program that takes user input.

**example.c**
```c
#include <stdio.h>
int main(){
	char name[50];
	
	printf("supply a name: ");
	scanf("%s", &name);
	printf("Hello %s", name);
	
	return 0;
}
```
To use this in your r2pipe script you'll want to first create a profile for this binary.

**example_profile.rr2**
```rr2_profile
#!/usr/local/bin/rarun2
program=example
stdin="John"
stdout=./example_output
```

For your script to make use of it, you'll need to make use of the ``` e ``` command for making changes to the session environment. You'll need to specify your rarun profile.

What we have specified here is the name of the program, the string input we would like sent to ```stdin``` and a file we would like ```stdout``` redirected to. _needed because the output isn't processed by your python shell on it's own_ 

**input_example_script.py**
```python
import os
import sys
import r2pipe

r2 = r2pipe.open('example')
r2.cmd('e dbg.profile=example_profle.rr2')

r2.cmd('ood')
r2.cmd('dc')
```

After setting the debug profile to the profile we just wrote, all this does is open the binary in debug mode via the ```ood``` command and tells the debugger to continue till the end with the ```dc``` command. To learn more about which commands you can use, take a look at the note on [Radare2](Radare2.md)

## Getting JSON data
Besides just the string output resulting from running a command, r2pipe can gather a bunch of other details about your session related to the command you just ran and give you some extra output from it's analyses. The python bindings provide this anyways, and it outputs it as JSON data so that you get it converted over to a dictionary without too much effort on your part.

Getting the JSON output just requires you to add a _'j'_ at the end of **cmd** and the radare command in your argument.

opening a binary in r2pipe and printing the disassembly for the first line in the file with these statements
```python
json_output = r2.cmdj('pdj')
print(json_output)
```
Gave me this and a bunch more output just to give you an example of what kind of stuff r2pipe gives you in the JSON

```output
{
  "offset": 4213152,
  "esil": "ebp,rbp,^,0xffffffff,&,rbp,=,$z,zf,:=,$p,pf,:=,31,$s,sf,:=,0,cf,:=,0,of,:=",
  "refptr": false,
  "fcn_addr": 0,
  "fcn_last": 0,
  "size": 2,
  "opcode": "xor ebp, ebp",
  "disasm": "xor ebp, ebp",
  "bytes": "31ed",
  "family": "cpu",
  "type": "xor",
  "reloc": false,
  "type_num": 28,
  "type2_num": 0,
  "flags": [
    "entry0",
    "rip"
  ]
}
```
# Metadata
**TAGS:** #Binary_Analysis #Radare2 #Binary_Analysis_Recipes #Radare2_tools 