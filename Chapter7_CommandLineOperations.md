## Chapter 7: Command Line Operations

### Viewing Files

`cat` Used for viewing files that are not very long; it does not provide any scroll-back.

`tac` Used to look at a file backwards, starting with the last line.

`less` Used to view larger files because it is a paging program. It pauses at each screen full of text, provides scroll-back capabilities, and lets you search and navigate within the file. Note: Use / to search for a pattern in the forward direction and ? for a pattern in the backward direction. An older program named more is still used, but has fewer capabilities: "less is more".

`tail` Used to print the last 10 lines of a file by default. You can change the number of lines by doing -n 15 or just -15 if you wanted to look at the last 15 lines instead of the default.

`head` The opposite of tail; by default, it prints the first 10 lines of a file.

`touch` is often used to set or update the access, change, and modify times of files. By default, it resets a file's timestamp to match the current time.

However, you can also create an empty file using touch:

```sh
touch <filename>
```

This is normally done to create an empty file as a placeholder for a later purpose.

touch provides several useful options. For example, the -t option allows you to set the date and timestamp of the file to a specific value, as in:

```sh
touch -t 12091600 myfile
```

This sets the myfile file's timestamp to 4 p.m., December 9th (12 09 1600).

`mkdir` is used to create a directory:

```sh
mkdir sampdir 
```

It creates a sample directory named sampdir under the current directory.

```sh
mkdir /usr/sampdir
```

It creates a sample directory called sampdir under /usr.
Removing a directory is done with `rmdir`. The directory must be empty or the command will fail. To remove a directory and all of its contents you have to do `rm -rf`.

Note that `mv` does double duty, in that it can:

- Simply rename a file
- Move a file to another location, while possibly changing its name at the same time.

If you are not certain about removing files that match a pattern you supply, it is always good to run `rm` interactively `(rm –i)` to prompt before every removal.

`mv` Rename a file

`rm` Remove a file

`rm –f` Forcefully remove a file

`rm –i` Interactively remove a file

`rmdir` works only on empty directories; otherwise you get an error.

While typing `rm –rf` is a fast and easy way to remove a whole filesystem tree recursively, it is **extremely dangerous** and should be used with the **utmost care**, especially when used by root (recall that recursive means drilling down through all sub-directories, all the way down a tree).

`mv` Rename a directory

`rmdir` Remove an empty directory

`rm -rf` Forcefully remove a directory recursively

### Modifying the Command Line Prompt

The PS1 variable is the character string that is displayed as the prompt on the command line. Most distributions set PS1 to a known default value, which is suitable in most cases. However, users may want custom information to show on the command line. For example, some system administrators require the user and the host system name to show up on the command line as in:

```sh
student@c8 $
```

This could prove useful if you are working in multiple roles and want to be always reminded of who you are and what machine you are on. The prompt above could be implemented by setting the PS1 variable to: `\u@\h \$`.

For example:

```sh
$ echo $PS1
\$
$ PS1="\u@\h \$ "
student@c8 $ echo $PS1
\u@\h \$
student@c8 $
```

By convention, most systems are set up so that the root user has a pound sign (#) as their prompt.

### Standard File Streams

When commands are executed, by default there are three standard file streams (or descriptors) always open for use: standard input (standard in or stdin), standard output (standard out or stdout) and standard error (or stderr).

|Name|Symbolic Name|Value|Example|
|----|-------------|-----|------|
|standard input|stdin|0|keyboard|
|standard output|stdout|1|terminal|
|standard error|stderr|2|log file|

Usually, stdin is your keyboard, and stdout and stderr are printed on your terminal. stderr is often redirected to an error logging file, while stdin is supplied by directing input to come from a file or from the output of a previous command through a pipe. stdout is also often redirected into a file. Since stderr is where error messages are written, usually nothing will go there.

In Linux, all open files are represented internally by what are called file descriptors. Simply put, these are represented by numbers starting at zero. stdin is file descriptor 0, stdout is file descriptor 1, and stderr is file descriptor 2. Typically, if other files are opened in addition to these three, which are opened by default, they will start at file descriptor 3 and increase from there.

On the next page and in the chapters ahead, you will see examples which alter where a running command gets its input, where it writes its output, or where it prints diagnostic (error) messages.

### I/O Redirection

Through the command shell, we can redirect the three standard file streams so that we can get input from either a file or another command, instead of from our keyboard, and we can write output and errors to files or use them to provide input for subsequent commands.

For example, if we have a program called do_something that reads from stdin and writes to stdout and stderr, we can change its input source by using the less-than sign ( < ) followed by the name of the file to be consumed for input data:

```sh
do_something < input-file
```

If you want to send the output to a file, use the greater-than sign (>) as in:

```sh
do_something > output-file
```

Because stderr is not the same as stdout, error messages will still be seen on the terminal windows in the above example.

If you want to redirect stderr to a separate file, you use stderr’s file descriptor number (2), the greater-than sign (>), followed by the name of the file you want to hold everything the running command writes to stderr:

```sh
do_something 2> error-file
```

Note: By the same logic, do_something 1> output-file is the same as do_something > output-file.

A special shorthand notation can send anything written to file descriptor 2 (stderr) to the same place as file descriptor 1 (stdout): 2>&1.

```sh
do_something > all-output-file 2>&1
```

bash permits an easier syntax for the above:

```sh
do_something >& all-output-file
```

### Pipes

The UNIX/Linux philosophy is to have many simple and short programs (or commands) cooperate together to produce quite complex results, rather than have one complex program with many possible options and modes of operation. In order to accomplish this, extensive use of pipes is made. You can pipe the output of one command or program into another as its input.

In order to do this, we use the vertical-bar, `|`, (pipe symbol) between commands as in:

```sh
command1 | command2 | command3
```

The above represents what we often call a pipeline, and allows Linux to combine the actions of several commands into one. This is extraordinarily efficient because command2 and command3 do not have to wait for the previous pipeline commands to complete before they can begin hacking at the data in their input streams; on multiple CPU or core systems, the available computing power is much better utilized and things get done quicker.

Furthermore, there is no need to save output in (temporary) files between the stages in the pipeline, which saves disk space and reduces reading and writing from disk, which is often the slowest bottleneck in getting something done.

### Searching for Files

Being able to quickly find the files you are looking for will save you time and enhance productivity. You can search for files in both your home directory space, or in any other directory or location on the system.

The main tools for doing this are the `locate` and `find` utilities.

The `locate` utility program performs a search taking advantage of a previously constructed database of files and directories on your system, matching all entries that contain a specified character string. This can sometimes result in a very long list.

To get a shorter (and possibly more relevant) list, we can use the `grep` program as a filter. grep will print only the lines that contain one or more specified strings, as in:

```sh
locate zip | grep bin
```

which will list all the files and directories with both zip and bin in their name. We will cover grep in much more detail later. Notice the use of `|` to pipe the two commands together.

locate utilizes a database created by a related utility, `updatedb`. Most Linux systems run this automatically once a day. However, you can update it at any time by just running `updatedb` from the command line as the root user.

### Wildcards and Matching File Names

You can search for a filename containing specific characters using wildcards.

|Wildcard|Result|
|--------|------|
|?|Matches any single character|
|*|Matches any string of characters|
|[set]|Matches any character in the set of characters, for example [adf] will match any occurrence of a, d, or f|
|[!set]|Matches any character not in the set of characters|

To search for files using the ? wildcard, replace each unknown character with `?`. For example, if you know only the first two letters are 'ba' of a three-letter filename with an extension of .out, type `ls ba?.out` .

To search for files using the *wildcard, replace the unknown string with*. For example, if you remember only that the extension was .out, type `ls *.out`.

### The find Program

`find` is an extremely useful and often-used utility program in the daily life of a Linux system administrator. It recurses down the filesystem tree from any particular directory (or set of directories) and locates files that match specified conditions. The default pathname is always the present working directory.

For example, administrators sometimes scan for potentially large core files (which contain diagnostic information after a program fails) that are more than several weeks old in order to remove them.

It is also common to remove files in inessential or outdated files in /tmp (and other volatile directories, such as those containing cached files) that have not been accessed recently. Many Linux distributions use shell scripts that run periodically (through cron usually) to perform such house cleaning.

When no arguments are given, find lists all files in the current directory and all of its subdirectories. Commonly used options to shorten the list include `-name` (only list files with a certain pattern in their name), `-iname` (also ignore the case of file names), and `-type` (which will restrict the results to files of a certain specified type, such as d for directory, l for symbolic link, or f for a regular file, etc.).

Searching for files and directories named gcc:

```sh
find /usr -name gcc
```

Searching only for directories named gcc:

```sh
find /usr -type d -name gcc
```

Searching only for regular files named gcc:

```sh
find /usr -type f -name gcc
```

Another good use of find is being able to run commands on the files that match your search criteria. The `-exec` option is used for this purpose.

To find and remove all files that end with .swp:

```sh
find -name "*.swp" -exec rm {} ’;’
```

The `{}` (squiggly brackets) is a placeholder that will be filled with all the file names that result from the find expression, and the preceding command will be run on each one individually.

Please note that you have to end the command with either ‘`;`’ (including the single-quotes) or "`\;`". Both forms are fine.

One can also use the `-ok` option, which behaves the same as `-exec`, except that find will prompt you for permission before executing the command. This makes it a good way to test your results before blindly executing any potentially dangerous commands.

It is sometimes the case that you wish to find files according to attributes, such as when they were created, last used, etc., or based on their size. It is easy to perform such searches.

To find files based on time:

```sh
find / -ctime 3
```

Here, `-ctime` is when the inode metadata (i.e. file ownership, permissions, etc.) last changed; it is often, but not necessarily, when the file was first created. You can also search for accessed/last read (-atime) or modified/last written (`-mtime`) times. The number is the number of days and can be expressed as either a number (`n`) that means exactly that value, `+n`, which means greater than that number, or `-n`, which means less than that number. There are similar options for times in minutes (as in `-cmin`, `-amin`, and `-mmin`).

To find files based on sizes:

```sh
find / -size 0
```

Note the size here is in 512-byte blocks, by default; you can also specify bytes (c), kilobytes (k), megabytes (M), gigabytes (G), etc. As with the time numbers above, file sizes can also be exact numbers (n), +n or -n. For details, consult the man page for find.

For example, to find files greater than 10 MB in size and running a command on those files:

```sh
find / -size +10M -exec command {} ’;’
```

### Package Management Systems on Linux

The core parts of a Linux distribution and most of its add-on software are installed via the Package Management System. Each package contains the files and other instructions needed to make one software component work well and cooperate with the other components that comprise the entire system. Packages can depend on each other. For example, a package for a web-based application written in PHP can depend on the PHP package.

There are two broad families of package managers: those based on Debian and those which use RPM as their low-level package manager. The two systems are incompatible, but broadly speaking, provide the same features and satisfy the same needs. There are some other systems used by more specialized Linux distributions.

### Package Managers: Two Levels

Both package management systems operate on two distinct levels: a low-level tool (such as dpkg or rpm) takes care of the details of unpacking individual packages, running scripts, getting the software installed correctly, while a high-level tool (such as apt, yum, dnf or zypper) works with groups of packages, downloads packages from the vendor, and figures out dependencies.

Most of the time users need to work only with the high-level tool, which will take care of calling the low-level tool as needed. Dependency resolution is a particularly important feature of the high-level tool, as it handles the details of finding and installing each dependency for you. Be careful, however, as installing a single package could result in many dozens or even hundreds of dependent packages being installed.

### Working With Different Package Management Systems

The Advanced Packaging Tool (apt) is the underlying package management system that manages software on Debian-based systems. While it forms the backend for graphical package managers, such as the Ubuntu Software Center and synaptic, its native user interface is at the command line, with programs that include apt (or apt-get) and apt-cache.

yum is an open source command-line package-management utility for the RPM-compatible Linux systems that belongs to the Red Hat family. yum has both command line and graphical user interfaces. Fedora and RHEL 8 have replaced yum with dnf, which has less historical baggage, has nice new capabilities and is mostly backwards-compatible with yum for day-to-day commands.

zypper is the package management system for the SUSE/openSUSE family and is also based on RPM. zypper also allows you to manage repositories from the command line. zypper is fairly straightforward to use and resembles yum quite closely.

To learn the basic packaging commands, take a look at these [basic packaging commands.]([https://link](https://prod-edxapp.edx-cdn.org/assets/courseware/v1/f478e1c28b54f5df1d53ef1bc855b2e3/asset-v1:LinuxFoundationX+LFS101x+3T2018+type@asset+block/Basic_Packagaing_Commands.pdf))

#### `dpkg`

```sh
dpkg --list
dpkg --list | grep pattern
dpkg --listfiles package
dpkg --remove package
dpkg --help OR man dpkg
```

#### `rpm`

```sh
rpm -qa | grep pattern
rpm -qil package
ls -lF $(rpm -ql package) | less
rpm -e --test package
rpm -q --whatprovides package
rpm -q --whatrequires package
```

#### `yum`

```sh
yum list pattern
yum info package
yum install package
yum remove package
```

#### High-Level Package Management with zypper ( rpm low level package manager ) on openSUSE

```sh
zypper search package
zypper install package
zypper remove package
```

#### Video: High-Level Package Management with apt (dpkg as low level package manager) on Ubuntu

```sh
apt-cache search package
apt-get install package
apt-get remove package
```
