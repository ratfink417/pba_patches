# Metadata
**TAGS:** #Programming #Reference  
# Content
**execve**() 

<pre>
<b>#include &lt;unistd.h&gt;</b>
<b>int execve(const char *<i>pathname</i><b>, char *const </b><i>argv</i><b>[],</b><b>char *const </b><i>envp</i><b>[]);</b>
</pre>
		    
executes the program referred to by _pathname_.  This
causes the program that is currently being run by the calling
process to be replaced with a new program, with newly initialized
stack, heap, and (initialized and uninitialized) data segments.

_pathname_ must be either a binary executable, or a script starting
with a line of the form:

    **#!**_interpreter_ [optional-arg]

For details of the latter case, see "Interpreter scripts" below.

_argv_ is an array of pointers to strings passed to the new program
as its command-line arguments.  By convention, the first of these
strings (i.e., _argv[0]_) should contain the filename associated
with the file being executed.  The _argv_ array must be terminated
by a NULL pointer.  (Thus, in the new program, _argv[argc]_ will be
NULL.)

_envp_ is an array of pointers to strings, conventionally of the
form **key=value**, which are passed as the environment of the new
program.  The _envp_ array must be terminated by a NULL pointer.

The argument vector and environment can be accessed by the new
program's main function, when it is defined as:

    int main(int argc, char *argv[], char *envp[])

Note, however, that the use of a third argument to the main
function is not specified in POSIX.1; according to POSIX.1, the
environment should be accessed via the external variable
[environ(7)](https://man7.org/linux/man-pages/man7/environ.7.html).

**execve**() does not return on success, and the text, initialized
data, uninitialized data (bss), and stack of the calling process
are overwritten according to the contents of the newly loaded
program.

If the current program is being ptraced, a **SIGTRAP** signal is sent
to it after a successful **execve**().

If the set-user-ID bit is set on the program file referred to by
_pathname_, then the effective user ID of the calling process is
changed to that of the owner of the program file.  Similarly, if
the set-group-ID bit is set on the program file, then the
effective group ID of the calling process is set to the group of
the program file.

The aforementioned transformations of the effective IDs are _not_
performed (i.e., the set-user-ID and set-group-ID bits are
ignored) if any of the following is true:

*  the _no_new_privs_ attribute is set for the calling thread (see
   [prctl(2)](https://man7.org/linux/man-pages/man2/prctl.2.html));

*  the underlying filesystem is mounted _nosuid_ (the **MS_NOSUID**
   flag for [mount(2)](https://man7.org/linux/man-pages/man2/mount.2.html)); or

*  the calling process is being ptraced.

The capabilities of the program file (see [capabilities(7)](https://man7.org/linux/man-pages/man7/capabilities.7.html)) are
also ignored if any of the above are true.

The effective user ID of the process is copied to the saved set-
user-ID; similarly, the effective group ID is copied to the saved
set-group-ID.  This copying takes place after any effective ID
changes that occur because of the set-user-ID and set-group-ID
mode bits.

The process's real UID and real GID, as well as its supplementary
group IDs, are unchanged by a call to **execve**().

If the executable is an a.out dynamically linked binary
executable containing shared-library stubs, the Linux dynamic
linker [ld.so(8)](https://man7.org/linux/man-pages/man8/ld.so.8.html) is called at the start of execution to bring
needed shared objects into memory and link the executable with
them.

If the executable is a dynamically linked ELF executable, the
interpreter named in the PT_INTERP segment is used to load the
needed shared objects.  This interpreter is typically
_/lib/ld-linux.so.2_ for binaries linked with glibc (see
       [ld-linux.so(8)](https://man7.org/linux/man-pages/man8/ld-linux.so.8.html)).
	
