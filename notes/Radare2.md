# Radare2
Radare2 is a suite of tools centered around reverse engineering binary files on the command line. It augments the functionality of [Binutils](Binutils.md) and was designed to make it easy for you to move on from basic analysis tasks like the kind of recon you'd normally do with binutils to automating things, customizing output and doing cool-guy leet reverse engineering shit.

Most of your interaction with radare is likely to be over a session which you can start by running the command ```r2 <FILE_NAME>```. This will load the binary and bring you to a prompt that show the address in the binary your session is sitting and a ```>```character to let you know you can begin typing commands. Should look something like this. 
```output
 -- Most likely your core dump fell into a blackhole, can't see it.
[0x00400500]>
```
# Session Basics
The session is the environment you will edit or observe a binary file from. Various things about the session can be configured to make sense for whatever work your trying to do. You might for example want to load a process from the binary that uses a different library path or turns off the system's ASLR. Here we'll talk about some of the most common things you might configure or at least be aware of.

## The Seek
The seek is the address displayed in the prompt as you work in radare2 and it represents the file that you are currently working from. This is the address that any commands you run will be relative to. For example, in a debugging session you might have your process hit a break point but may wish to print some lines in some other area of memory. For this to happen, you would first need to move the seek with one of the [r2 commands](r2%20commands.md#Seek%20Commands) to that portion and then print the next few lines you want to see.

## Addressing Modes
By default, radare2 will open your session in (VA) _Virtual Addressing_ mode meaning that the offsets you see in your prompt and work with as you analyze the file will be offsets used to determine addresses of the same code when the binary is loaded as a process. If you want to view the offsets in _normal mode_ just use the **-n** flag in starting your session and offsets will start at 0.

## Commands
Commands in radare2 take an approach similar to vim, they are mnemonic abrieviations for the actions you need r2 to do for you. For example, anything that requires printing will start with `p` if it's disassembly you want printed, add `d` and if you need the next 3 lines printed just add 3 to the end. The full command would be `:pd3`.

A list of all commands for reference can be found in [this note](r2%20commands.md)

### Getting Help
Any time you need to get help on a command, you can get a description of the command and it's options by following it with a `?` for example `:p?` will give you all the different commands that start with `p` and `pd?` would give you all the different commands that start with `pd`.

### Pipes and Redirection
The standard UNIX pipe `|` is made available in your r2 session and can send output to any program that accepts _stdin_ as input. You'll often want to send r2 command output to things like grep and awk for instance.

File redirection `>` is also supported, allowing you to push your output to a file for later use.

This for example would be valid.`pd 2000 | grep eax > output.txt` 
if you wanted to grep opcodes that use the 'eax' register and append them to the file _output.txt_

Sort of unrelated but because grep is so useful, on systems that don't have a utility like it, radare has the `~` command to get you similar functionality. You can find out more about it from your session using the `~?` help command.

### Relative Positions
If you do not want to move your seek to execute a command the `@` symbol is used to make any command to the left of it operate relative to the position put to the right of it. IE (`pd 5 @ 0x100000fce` will print 5 lines of disassembly starting from the address _0x00000fce_)

## Expressions
Expressions are often your input or output from commands run in your r2 session and are mathematical operations used in calculating a value that you need.

Before using the wrong expression as you work, you can evaluate it by prepending `?` to your expression. ```?vi 0x8048000``` would give you ```134512640```

You can get help on expression with the `?$?` command and you can dig into each expression command from there. `v` shows up in that help list for example and if you want to learn more about the `v` command, you can get help on it with `?v?`

## Debugging session
To open your binary in an interactive debugging session, you can specify the **-d** option on the command line or if you are already in a static section you can use the command `ood` the mnemonic here being `o` for open `oo` for _Open_ the currently _Open_ file and `d` for _Debug mode_

Once in this mode you can set breakpoints and run the process with the `db` and `dc` and you can step into or over with the `f7` and `f8` keys like you can in most debuggers.

To leave the prompt view and see the debugging view, you can use the `V` command to change the _View_ from here you can use the `p` key to move between various views. The first one you get dropped into won't actually show disassembly because it's going to be the _hex editor view_. Pressing the `p` key a couple times will get you to the disassembly and one more time will get you to a view that show the code and the state of the stack and processor registers/flags. It should look something like this.

![](Pasted%20image%2020210923125705.png)

To get commands run, you need to enter the command prompt by pressing `:` which is also similar to the workflow in vim.

# Metadata
**TAGS:** #Binary_Analysis #Radare2