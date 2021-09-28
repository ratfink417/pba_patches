# rarun2
rarun2 launcher for running programs within different environments, with different arguments, permissions, directories, and overridden default file descriptors.

Environments are configured by rarun2 via profiles that follow a simple _key=value_ syntax like this.

**example.rr2**
```rarun2
#!/usr/bin/rarun2
program=./pp400
arg0=10
stdin=foo.txt
chdir=/tmp
#chroot=.
./foo.rr2` 
```

rarun2 can be called directly from the command line like this ``` rarun2 progarm =/bin/ls stdout=output.txt``` or as an argument to radare2's **-r** flag like this ```r2 -r example.rr2 -d /bin/ls``` 
Below are some simple recipes for different rarun2 functions you might want to use for solving problems in CTF's or whatever.

## Connecting a Program with a Socket
Set up a [[netcat]] listener on some port and use the _connect_ setting in rarun to connect the i/o of that running process to the listening port.

```output
$ nc -l 9999
$ rarun2 program=/bin/ls connect=localhost:9999` 
```

## Debugging a Program Redirecting the stdio into Another Terminal

- open a new terminal and type 'tty' to get a terminal name

```commandline
$ tty ;
/dev/ttyS010 
```

- Create a new file containing the following rarun2 profile named foo.rr2:

**foo.rr2**
```rarun
#!/usr/bin/rarun2
program=/bin/ls
stdio=/dev/ttys010` 
```

- Launch the following radare2 command:
```
$ r2 -r foo.rr2 -d /bin/ls
```
# Metadata
**TAGS:** #Binary_Analysis #Radare2 #Recipies #Radare2_tools