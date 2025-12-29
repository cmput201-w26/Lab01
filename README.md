# Lab 1 - Welcome to CMPUT 201

This lab exercise is due before __11:59pm, January 18, 2026__. This lab is essential to work on the future lab exercises.
<hr>

Welcome to CMPUT201! We're happy to have you here! 
In this course we'll dive deep into standard C programming in Unix/Linux environment.

This first lab has a good amount of content to go through so please take your time.
Future labs will not require so much setup. 
It is very important that you read all steps, to ensure you know how to submit and test your code in this course. 
The tools we introduce to you here will be ones you will use in any programming-related career you may be interested in doing in the future. 
We hope that you can get comfortable using these tools throughout this year. 
For now though, this lab serves as a simple introduction.

If you need any help with the lab, please don't hesitate to email the TAs, attend *any* lab section (not just your own), or come to one of the TA office hours (Tuesdays and Thursdays, 11:00am - 1:30pm, usually @UCOMM 2-001, see Canvas for exceptions).

## Important Distinctions between Unix and Windows

Our course is aimed to teach you programming in Unix/Linux environment (some Unix based operating systems include MacOS, any Linux distribution, etc), not in Windows. 
We recognize that many students aren't ready to switch to Linux at this stage, so you can still use Windows machines for this course. 
Nevertheless, all the code you write __MUST be run on the lab machines__ (which are ucomm-2086-wXX.cs.ualberta.ca, where XX ranges from 00 to 33; for security reasons, you are not able to directly ssh into these machines, but through ohaton or UofA VPN, details see below and will be demoed in class).
__NEVER use the "play" button__ on VS Code (especially any extensions that claim to run C code for you), that's a fast-track to __getting a 0__ in the labs. 
There will be no exceptions for this, your code must run perfectly on the lab machines (which use Linux), since that's where all the marking will be done.

Notable differences for Windows include:
1. Using `\` instead of `/` for paths. Don't mix this up, since on Unix `\` is usually for escape characters like `\n`.
For example, the path `directory/file.txt` becomes `directory\file.txt` on Windows.
2. `~` also fails to expand to the home directory in many cases, so we use `$USERPROFILE` (a PowerShell exclusive), but in paths that sometimes requires the `$env:` prefix to expand, so `~/.ssh/config` on Unix is `$env:USERPROFILE\.ssh\config` on Windows. 
Note that whenever we ask to navigate to your home directory `$env:USERPROFILE`, it will most likely be in `C:\Users\[your username]`.

To save yourself any headaches, after following the set-up tutorial, make sure to SSH into the lab machines and do all your programming and compiling directly there.

# Part 1 - Setting up SSH
The SSH (Secure Shell) protocol is a tool to remotely connect to other computers (such as our lab machines) and it's indispensable in software development.
This allows for a standardized environment to develop and test your code, and avoids any cross-platform differences.
Even if you use a Unix operating system on your personal computer, you **must** ensure your code works as expected on the lab machines.

On MacOS and Linux, SSH is installed by default.
On any Windows 10 or 11 version after 2018, ssh *should* be installed by default as well.
You can check to make sure by opening up **PowerShell (not cmd)** and running `ssh -V`. 
If that produces an error, install [OpenSSH](https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse?tabs=gui#install-openssh-for-windows).

## Generating an SSH Key
Start by creating an ssh key, which will allow you to connect to the lab machines without constantly re-typing your password.

Run the following in your terminal:

*If you already have an ssh key made, you do not need to make a new one and can skip this command.*

**Windows (PowerShell):**
```
ssh-keygen -t ed25519 -f $env:USERPROFILE\.ssh\id_ed25519
```
Hit enter twice for no passphrase.

**MacOS/Linux:**
```
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519 -P ""
```

## Setting up the SSH Configuration

Now use a text editor to open your ssh `config` file, for example `code $env:USERPROFILE\.ssh\config` for VS Code on Windows.
Here you'll need to copy/paste the below text. 
Replace `<ccid>` with your ccid (the thing in front of @ualberta.ca for your email), and replace `<num>` with a number between `00` and `33` (two digits; we recommend `(student id) mod 34` to balance out among all students).

```sshconfig
Host lab
    Hostname ucomm-2086-w<num>.cs.ualberta.ca
    Port 22
    User <ccid>
    IdentityFile=~/.ssh/id_ed25519
    IdentitiesOnly=yes
    ProxyJump <ccid>@ohaton.cs.ualberta.ca
```

**In this and all following steps, make sure to replace `<ccid>` and `<num>` __including__ the characters `<` and `>`.**
For example, if your CCID is `johndoe` and your desired number is `00`, your config should look like:

```sshconfig
Host lab
    Hostname ucomm-2086-w00.cs.ualberta.ca
    Port 22
    User johndoe
    IdentityFile=~/.ssh/id_ed25519
    IdentitiesOnly=yes
    ProxyJump johndoe@ohaton.cs.ualberta.ca
```

Note that if you're experiencing issues connecting, try changing the `<num>` in this file. 
Your files are the same across all lab machines, so you can switch between them without any trouble. 
The above suggestion tries to even out the students using a machine to avoid potential issues.

Some text editors, such as Notepad on Windows, decide they know better than you and will call the saved text file `config.txt` instead of `config`.
If using the command `ssh lab` says `ssh: Could not resolve hostname lab: No such host is known.` or something simillar, this may be the cause.
To fix this, you can use the command:

**Windows:**
```
Rename-Item -Path "$env:USERPROFILE\.ssh\config.txt" -NewName "config"
```

**MacOS/Linux:**
```
mv ~/.ssh/config.txt ~/.ssh/config
```

## Copying the SSH Key

Now we need to run the following command, where you once again need to replace `<ccid>` and `<num>`

**Windows (PowerShell):**
```
cd ~
cat .\.ssh\id_ed25519.pub | ssh -J <ccid>@ohaton.cs.ualberta.ca <ccid>@ucomm-2086-w<num>.cs.ualberta.ca "cat >> ~/.ssh/authorized_keys"
```
**MacOS/Linux:**
```bash
ssh-copy-id -o ProxyJump=<ccid>@ohaton.cs.ualberta.ca -i ~/.ssh/id_ed25519.pub <ccid>@ucomm-2086-w<num>.cs.ualberta.ca
```

**Continue for both Windows AND MacOS/Linux**

You might see something like the below. In this case type `yes` and hit enter. If it appears more than once, type `yes` each time. When you type `yes`, it may not appear as you type. It will still work however.

```
The authenticity of host 'ohaton.cs.ualberta.ca (129.128.243.70)' can't be established.
ED25519 key fingerprint is SHA256:SeHT9JqaLhpEpec6WXLYs9P1L3P75AWNav4e7b8GmpU.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Next it'll ask for your password. Type in the same password you use for Canvas, but notice that the cursor won't move while typing (it's a security measure). You may be prompted to type your password several times.

Some students may encounter an error "no such file or directory". 
IF this is the case, execute the following (replacing `<ccid>` and `<num>` again):
```
ssh -J <ccid>@ohaton.cs.ualberta.ca <ccid>@ucomm-2086-w<num>.cs.ualberta.ca "mkdir .ssh"
```
Then repeat the steps above starting from the "Copying the SSH Key" heading.

Now try `ssh lab`, which should log you in instantly, no password required! 
This is all you need for accessing lab machines from now on. 
If you're interested in learning more, consider [this tutorial](https://noway.moe/unix/ssh) by TAs in a previous year.

# Part 2 - Working on a Lab Machine through SSH

## Connecting
In this part we'll learn how to connect and use the lab machines. 
These are provided by the university's department of Computing Science for our course and you all have access to them.

Start by opening up a terminal and run the following command:

```bash
ssh lab
```

Now you should be on the lab machine. 
If you did Part 1 correctly, you should not be prompted for a password.

### The File System
In this part, we'll start using the Unix file system commands.
First, we're going to learn 3 commands, which we __heavily encourage__ you to search up to learn more about.

 - `ls`: This one lists the content of the current directory.
 - `cd`: Means "change directory"; this is how you move between directories.
 - `mkdir`: Means "make directory"; it creates a new directory.

Now follow these steps:

 1. Type `cd` to switch to your home directory (You can always type the command `pwd` to `p`rint your current `w`orking `d`irectory, at this point it should be `/cshome/<ccid>`).
 2. Make a directory for your CMPUT 201 work, using `mkdir CMPUT201`.
 3. Check to make sure you successfully made your directory with `ls`.
 4. Go into you CMPUT 201 directory with `cd CMPUT201`.
 5. List all the files in this directory (there won't be any).

**Important:** Any commands you run will execute in the current working directory. You will see this before the typing cursor. Ensure the file
you intend to work with is in the given folder with `ls` if you have issues.

## Moving Files between Machines Using `scp`

Although we encourage you to write all code on the lab machines, you may find it easier to work with on your computer.
It is incredibly important to copy the code to the lab machines and run it there.
**There is 0 tolerance for code that runs perfectly on your computer but not on the lab machines. 
We will mark exclusively on the lab machines; so if it fails there, you will get a zero.**

To make things easy, in this part we'll setup a way to quickly copy over your code from your machine to the lab machines!

We've already setup the SSH connection, so let's try copying over a text file into the home directory of the lab machines. 
We will do this by using the `scp` (secure copy) command.
Start by creating a text file `test.txt` in any folder on your PC, then navigate to that folder in your terminal, and use the following command:

```bash
scp test.txt lab:~/mymovedfile.txt
```

Notice that `lab:` prepending the destination path? 
That indicates we should be copying to the host `lab`, which we previously set up.

If you want to copy over an entire folder, instead of just individual files, all we need to do is add the `-r` option to `scp` to make it "recursive". 
For example,

```bash
scp -r lab01 lab:~/CMPUT201
```

If you want to copy files inside the lab machine, you can use the `cp` command.

If you want to delete a file, you can use the `rm` command. Delete `mymovedfile.txt` with
```
rm mymovedfile.txt
```
And confirm with `y`.

# Part 3 - GitHub setup on Lab Machine

Now it's time to setup GitHub. Think of GitHub like cloud storage for code, but
it's exclusively controlled through `git`. As you may know, this is a vital tool for programmers used for version control.

Firstly, make a GitHub account at [https://github.com](https://github.com) if you don't already have one. 
Now join our GitHub Classroom by accepting lab 1 (if you're here, then you've probably done this already) through this ___.
Choose your CCID from the list. 
If your CCID does not show up, skip this step and contact Henry Tang (hktang@ualberta.ca) so he can add you (especially if you transfer in late).

You can connect to GitHub from your local machine, but we recommend that you use the lab machine to perform all your coding and commits as that environment is consistent for all students.

## Steps

1. Click your profile icon in the top right on
    [https://github.com](https://github.com)
2. Click Settings
3. Click SSH and GPG Keys
4. Click New SSH Key. This should bring up an "Add new SSH Key" Menu
5. Set whatever "Title" is most memorable to you. For example "U of A Lab machine"
6. Set "Key Type" to Authentication
7. Open a terminal and ssh into the lab machine (`ssh lab`).
8. Create a ssh key for your GitHub account. (This will be a new key, not the one you created for sshing to the lab machine)
`ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519 -P ""`

9. The lab terminal, run `cat ~/.ssh/id_ed25519.pub`
10. Copy the output of that command. It should look something like `ssh-ed25519 jdi31aclAkaafnA81uweAnABSj ccid@ucomm-2086-wXX` (except the middle random characters portion will be longer and `ccid@ucomm-2086-wXX` will be your username and device name)
11. Then paste it into the "Key" section back in the "Add new SSH Key" Menu
12. Finally, click "Add SSH Key"
13. Open up `~/.ssh/config` in a text editor and paste the following at the bottom, then save and close the file:

```sshconfig
Host github.com
    Hostname github.com
    IdentityFile ~/.ssh/id_ed25519
```
14. Now in a terminal, run `ssh -T git@github.com` (type `yes` if prompted about the "authenticity of the host" as before). You should then get a message like the following, but with your username of course:
```
Hi <username>! You've successfully authenticated, but GitHub does not provide shell access.
```
15. Clone whatever lab assignment repository you are working on using the following command: 
    `git clone git@github.com:cmput201-<term>/<lab number>-<username>.git`
You can find this link on the GitHub page of the repository you want to clone. (Its in the green "Code" dropdown button).
**Make sure to click on the SSH tab. The link should begin with:**
```
git@github.com:cmput201-<term>/
```
NOT
```
https://github.com/cmput201-<term>/
```

# Part 4 - Introduction to C Programming

We've done a lot to get here, so the C programming part of this lab is very minimal.

Your task for this lab is to create a file named `ex1q1.c`, compile it, run the program, and redirect the output of the program to a file.

Here is the starting code (copy-paste into a new file with name mentioned above):

```c
#include <stdio.h>

int main(void) {

}
```

Copy and paste the following text and make your program print it out exactly as it is:
```
Hello World!
This  is a simple file 
with some text.
Let's make a change.
```
You can do this with the `printf` function. 
Check out the textbook, lecture notes, or look up online (and cite) how to use `printf` to achieve this.

Unlike a Python file, computers cannot run C source code directly. 
To turn a C source file into a real program, we have to _compile_ it using a _compiler_ like GCC (GNU Compiler Collection). 
The compilation process turns the code into machine readable instructions (this will be a separate file, often called an executable), which you can then run. 
A simple compilation using GCC looks like this

```bash
gcc ex1q1.c
```

This creates an executable file named `a.out`, which you can then run. You can use the `-o` flag to rename the executable.
This is done in the following manner:

```bash
gcc ex1q1.c -o ex1q1.out
```

`-o ex1q1.out` tells the compiler to name the resulting executable file `ex1q1.out` instead of the default `a.out`. 
You are marked on submitting correct source code, not an executable. 
This means you can name your executable whatever you wish (including not ending in `.out`).
We will use this naming convention for clarity and recommend you do as well.

We will, however, **enforce** three crucial flags: `-Wall`, `-Werror`, and `-std=c99`.
The first two flags tell the compiler to display any errors or warnings that it may encounter while compiling your code. 
This is very important since it will display useful messages for your debugging purposes should your program have any issues.
Your code will also **__fail to compile__** if **__any__** warnings or errors are detected, meaning you will get a **__zero__** if you submit it in that state.
The third flag, `-std=c99`, tells the compiler to use the C99 standard. 
This is just as important because both the course lectures, and the textbook teach the C99 standard.

Overall, you will now compile and run this (on the **lab machines**) using the following command:

```bash
gcc -Wall -Werror -std=c99 ex1q1.c -o ex1q1.out
```

If you are working locally, you first need to copy over your project with `scp` in order to compile on the lab machines. 
**Do not compile on your own computer** and then transfer, always compile in the lab machines.

If your code has any issues it will not compile, check any error messages in the console provided by `-Wall` and `-Werror`, resolve them, and try again.

If your code is written properly and the compilation is successful, you should be able to see the executable file `ex1q1.out` in the current directory, run it by using

```bash
./ex1q1.out
```

You should see your program's output in the console.

To save this to a text file, you can redirect the output using the redirection operator `>` into a file named `ex1q1_out.txt`.

```bash
./ex1q1.out > ex1q1_out.txt
```

Note if you try running this again, it will fail and refuse to overwrite the old file. You can bypass this by adding the character `|` like so:

```bash
./ex1q1.out >| ex1q1_out.txt
```

**Be VERY careful doing this because it will override (replace) the file you're redirecting into.**

# Part 5 - Checking Your Output

## The `diff` Command
The `diff` command is used to compare the content of two files. When working on labs, you will be given text files containing expected output. Using the `diff` command is how you will determine whether your program produces the correct output or not, this is done by comparing your output with the given expected output. 
The purpose of this section of the lab is for you to get familiar with reading the output produced by `diff`, so you can comfortably determine where your program went wrong in future labs.

The way to use this command is:

`diff -u file1 file2`

Note: For this course, `diff` should always be run with `-u`

## Reading `diff` Output with an Example

`a.txt` contents:
```
This is a file 
with text.
some changes may happen
```

`b.txt` contents:
```
this is a file
with text.
some changes might happen
```

`diff -u a.txt b.txt` output:
```
--- a.txt       2024-09-09 12:00:00.000000000 -0600
+++ b.txt       2024-09-09 12:00:00.000000000 -0600
@@ -1,3 +1,3 @@
-This is a file 
+this is a file
 with text.
-some changes may happen
+some changes might happen
```
**Output breakdown:**

The first two lines say that the `-` character corresponds to `a.txt`, and the `+` character corresponds to `b.txt`. Any time one of `-` or `+` appears on the output after this point, it will refer to its corresponding file. Lastly, `2024-09-09 ...` is the file's timestamp.

`@@ -1,3 +1,3 @@`: This line tells us which lines will be shown on the output given on the lines after this one:
- `-1,3` means that `diff` will show lines `1` through `3` from `a.txt`.
- `+1,3` means that `diff` will show lines `1` through `3` from `b.txt`.

The rest of the lines show the differences between both files on each line:

If at any point, there is a difference between both files on a particular line, then `diff` will show both versions of that line, with one of the two lines beginning with `-`, and the other with `+`, telling us which file these lines belong to. Example from above:

```
-This is a file 
+this is a file
```
and

```
-some changes may happen
+some changes might happen
```

If there are no differences on a line, `diff` will simply show the line common between both files with a space in the beginning (instead of `-` or `+`). Example from above:

```
 with text.
```

## Task

Your task is to compare the output of your program from Part 4 that you wrote to `ex1q1_out.txt`, to `ex1q1_different.txt`, which is provided to you.
1. Compare `ex1q1_out.txt` and `ex1q1_different.txt` using `diff -u` and redirect the output into a file named `diff.txt`.
2. Open `diff.txt`, and view the differences between both files with your new knowledge on how to read `diff -u` output.
3. You may notice that `diff -u` marks one line as different while to you they look the same. 
If this is the case, then `diff -u` spotted a difference that has to do with invisible characters (like space). 
Go back to both files and try to spot the difference yourself.
4. Go back to `ex1q1.c`, and update your code so that it matches `ex1q1_different.txt`, recompile, redirect your output once more, and check the differences using `diff -u` once again, do this until `diff -u` reports no differences between both files.

`diff -u` reporting differences in invisible characters may seem unfair since in future labs, if `diff -u` says that your output does not match the expected output, but you can't see any differences, then you will fail that test case.

You must be aware of `diff -u`'s handling of invisible characters, and how to spot these invisible characters and handle them appropriately. 
Note that this also applies for any empty lines.

Expected outputs for future labs will never contain any spaces after lines to mess you up, any hidden invisible characters for you to go on a quest to discover, or anything similar. 
However, your own output may have extra spaces in places that are not inteded, this will influence the `diff -u` report and potentially result in failing test cases.

# Part 6 - Submitting Your Work

`git` provides a way to incrementally track changes in your code. 
Here we'll learn how to "upload" your changes, which is what you'll need to do for submitting each lab.

SSH into a lab machine (following the earlier instructions) and head over to your `Lab01` folder in your terminal with `cd`.
There we can check the state of git using `git status`. 
This shows all the changes that have happen in this directory since git was last updated.

Your output should look like this:
```
On branch <your-branch-name>
Your branch is up to date with 'origin/<your-branch-name>'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   ex1q1.c

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	diff.txt
	ex1q1.out
	ex1q1_out.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

You should see that `ex1q1.c` has changed, and new files `ex1q1.out`, `ex1q1_out.txt`, and `diff.txt` were created but are "untracked".

First, let's save our changes to `ex1q1.c`:

```bash
git add ex1q1.c
git commit -m "Implemented ex1q1.c"
```

You made your first commit! A commit is a set of changes to our files, with a helpful message describing the changes.
It's good practice to commit often, as it'll let you go back to older versions of your code. 
We personally recommend committing every time you make a notable change.

What about `ex1q1.out`, `ex1q1_out.txt`, and `diff.txt`? We don't really care about saving changes to these files, as they will be the same if
we compiled and ran the program again from our saved source code. 
We can tell git to ignore these files by creating a new text file `.gitignore` (if it does not already exist) and adding
```
ex1q1.out
ex1q1_out.txt
diff.txt
```
You can also add wildcard characters, for example
```
*.out
*.txt
```
Will ignore any files ending in `.out` or `.txt`. 
Use this carefully to not ignore an important file!

Since `.gitignore` is just another file in the repository, you have to commit changes to it as well.
```bash
git add .gitignore
git commit -m "Add ex1q1.out, ex1q1_out.txt, and diff.txt to .gitignore"
```

If you run `git status` now, you should see

```
On branch <your-branch-name>
Your branch is up to date with 'origin/<your-branch-name>'.

nothing to commit, working tree clean
```

Committing saves changes ONLY to the device you are working on.
Now we want to "upload" our changes to GitHub. You can do this with:

```bash
git push
```

Remember to do this before the deadline! 
Just like Canvas, anything submitted after the deadline is late and won't be marked.
You should `git push` after every commit, to make sure you don't forget to do so before the deadline.

That's it! Thank you for bearing with us through this lengthy starter lab. 
Make sure you push everything to your GitHub repository by the deadline.




