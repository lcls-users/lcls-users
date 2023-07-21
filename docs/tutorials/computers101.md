# How to Computers
!!! Overview 
    This page will provide guidance and key points for navigating and operating with the Unix Terminal, Git, Python, and SSH. 
    Concepts, useful command charts, and demonstration of function are below. See <a href="Software-Carpentry.org">Software Carpentry</a>
    for in depth instruction. 
## How to Terminal
!!! note "Summary" 
    See <a href="Software-Carpentry.org">Software Carpentry</a> for instruction. Below are key take aways and what to look for in the link. 
    We will be using the Unix Shell to:
    <blockquote> 
    -navigate files and directories
    <br>-work with files, directories, loops, and scripts
    <br>-utilize pipes and filters 
    <br>-find strings, files, and directories that match a pattern
    <br>-learn how to combine commands to carry out and achieve tasks 
    <br>-learn shortcuts for ease of working with a programming language
    </blockquote>
 
<br>**The Essentials**
<br><br>**The Shell** 
<br>The **shell,** also known as the terminal or command line, is a program that enables users to direct commands to the computer to do . 
    The computer reads those commands and then runs programs to achieve your specified task. 
    The most common way people do this is through a graphical user interface (GUI), where one can click icons and use menus to interact. 
    Here however, you will use a command line interface (CLI), where one can type commands in a scripting language. 
<br><br>**Shell Prompt**
<br>The shell's prompt `$` is where a command/executable is entered. 
    This may look different depending on your device and operating system, but the shell waits for your input here:
    <br> `$ [your command here]`
<br><br> This is a **command/executable**: `ls` 
<br><br>**Basic Structure of a Command**
<br>At the prompt:
<br>$`CommandName [option(s)] [argument(s)]`
<br>&ensp;&ensp;-an **option** changes the behavior of a command
<br>&ensp;&ensp;-an **argument** states what the command should operate on
<br>&ensp;&ensp;-**parameters** are options and arguments
<br>&ensp;&ensp;-commands can have none or more than one option and argument
<br><br>**Files and Directories**
<br>A **file** is an entity that stores information. 
    A **directory** is a folder, holding other files and directories within itself. 
    There is a root directory from which all files, directories, and **programs** are stored on your computer.  
<br>**Key Commands**
<br>*Note: each command can have several options with combinations of arguments, not listed here. Explore them to fully access the shell's capabilities.*

|<div style="width:140px">Command</div> | Description    |
|----------------|----------------|
|$               |**Command Line Prompt**<br>  - the shell waits for your inputted command here|
|`pwd`           |**Print Working Directory**<br>-at any point in time in the shell we are in a directory<br>-this will print the current working directory (and hence the path to it)
|`cd`            |**Change Directory**<br> Do:<br>`$ cd [path]`<br>to change from the current working directory to specified path
|`ls`            |**List**<br>- lists contents of a directory |
|`ls --help`     |**List Help Option**<br>- the `--help` option lists all options that can be used for `ls`<br>- useful to reference|
|`cat`           |**Catalogue**<br>- prints contents of a file | 
|`clear`         |**Clear**<br>- clears all text on terminal|
|`mkdir`         |**Make Directory**<br>Do:<br> $ `mkdir [path]`<br>to name and create a directory|
|`rmdir`         |**Remove Directory**<br>Do:<br> $ `rmdir [path]`<br>to delete a directory|
|`touch [path]`  |**Create File**<br>- creates a file |
|`rm [path]`     |**Remove**<br>-deletes specified file<br>-does not ask to confirm deletion; once deleted, deleted forever|
|`mv [old] [new]`|**Move**<br>-moves `[old]` contents to `[new]` contents<br>- expects last option to be a directory<br>- can be used to rename files via same structure | 
|`cp [old] [new]`|**Copy**<br>- copies files from `[old]` to `[new]` where `[new]` is the location you are copying to<br>- you can change the name of the new file at the step as well|
|`wc`            |**Word Count**<br>- counts the number of lines, words, and characters in files, and prints those values in that order |
| `|`            |**Pipe**<br>- feeds the output of one command as the input for another command<br>Ex:<br>$ `echo MyNovel.txt | head -n 15`<br>-first command would print the text file `MyNovel.txt` in its entirety but redirects that as the input for the command on the right of `|`<br>- `head -n 15` prints the first 15 lines of its input (`MyNovel.txt`) so in the end only the first 15 lines of `MyNovel.txt` will be printed in the shell 
|`>`             |**Redirect**<br>- redirects output or input of a command to a file<br>- overwrites what's in the destination file |
|`>>`            |**Redirect**<br>- redirects output or input of a command to a file<br>- appends output to what is already in the destination file |
|`head`          |**Head**<br>- prints by default the first 10 lines of one or more files | 
|`tail`          |**Tail**<br>- prints by default the last 10 lines of one or more files |
|`sort`          |**Sort**<br>- sorts lines of text in a file as specified by options<br>- by default sorts by reverse alphanumeric order so that:<br>- a line starting with a number is listed first, then ones with letters |
|`cut`           |**Cut**<br>- removes or cuts out sections of a specified file or data  |
|`echo`          |**Echo**<br>- prints text or strings |
|`nano`          |**Text Editor Program**<br>Do:<br>$`nano [file]`<br>to edit a text file via `nano`<br><br>- save and exit the `nano` program to return to the shell screen |
|`uniq`          |**Uniq**<br>- removes adjacent duplicate lines in a file | 
|`history`       |**History**<br>- lists all commands, numbered, from the start of the session in the shell<br>- history command is printed last<br>- useful to repeat previous work |
|`grep [pattern] [path]`|**Grep**<br>- searches for a continuous string in one or more files that match a pattern<br>- case insensitive unless specified |
|`find [path]`   |**Find**<br>- finds and prints every file and directory under the specified path and parameters given in the command | 

**Keyboard Shortcuts**

|<div style="width:150px">Shortcut</div> |<div style="width:300px">Description</div> | 
|---------------------------------------|------------------------------------------|
|<table><tr><td><kbd>**&uarr;**</kbd><kbd>**&darr;**</kbd></td></tr></table>| Use the arrow keys on your keyboard at the prompt to scroll through previous commands, one line at a time, instead of scrolling through the terminal with your mouse|
|<kbd>Ctrl + A</kbd>| Use to navigate to the beginning of a line|
|<kbd>Ctrl + E</kbd>| Use to navigate to the end of a line|
|<table><tr><td><kbd>Tab</kbd></td></tr></table>|Use **Tab Completion** to automatically fill in the name of a file or directory instead of manually doing so <br> *Note: if mulitple file or directory names are named similarly in the beginning of their title, the tab completion key won't work until the name is unique*<br>Ex: If there are text files `MyNovelBeg.txt` `MyNovelMid.txt` and `MyNovelEnd.txt` and we want to type `MyNovelBegEnd.txt` tab completion will work until `MyNovelE` is typed and the **<kbd> Tab </kbd>**key is pressed
|~|-the tilde represents the user's home directory<br>-can be used to navigate directories without typing out the user's home directory|
|* or ?|**Wildcards**<br>-a character that can be used to represent a set of none or more characters<br>-can be used for ease of navigating files and directories in the shell|

## Other Notes
###Directories
**Directory names** are written as: `DirectoryName/` with the slash mark to indicate it is a directory
<br><br>**Recursive** - a directory can contain multiple files and/or multiple directories that each have multiple files and/or directories. If a command or program is "recursive," it will act on a directory and all of the contents of that directory
###Scripts
**Script** - a list of a particular sequence of commands, usually stored in a file or saved for future reference
<br><br>**Leaving Comments**
<br>&ensp;&ensp;-the # symbol followed by a string can be used to leave a comment in a script that will be skipped over by the shell, so that it won't run as a command
<br>&ensp;&ensp;-this can be useful for leaving notes on what your command does for others or for future reference 

## How to `git and github`- Using a Version Control System

!!! note "Summary"
    See <a href="https://swcarpentry.github.io/git-novice/">Software Carpentry</a> for instruction. Below are key take aways and what to look for in the link.
    This guide will provide key concepts needed to navigate and work with `git` in order to make git push and pull requests. Defining what a version control system is, understanding 
    basic git commands, initiating repositories, and utilizing `git add` and `git commit` are the crucial to successfully utilize `git` and `github`. 
    <br>*Note: A basic understanding of using Terminal is not mandatory but is useful for navigating and working with the version control Git. 
    See the "How to Terminal" section for reference. A basic understanding of navigating a Unix-like terminal will be assumed for working with this version control system.*
     
<br>**Version Control Systems**
<br> A version control system is a tool for managing changes to a file, so that multiple users can edit and contribute code to a shared file.
Instead of copying the file multiple times for each user to then edit, there is one file, with its edit history recorded. Who contributed what, and when, is recorded 
as well. Independent users can record their contributions without committing those changes for review. The system will notify when there's a 
confliction between code. Note that the changes are separate from the original file. In this way, users can changes to the files can be recorded 
and reviewed before they are taken as a real edit to the original file, allowing them to work in parallel without confliction. 
<br><br>**Git Configuration**
<br>Usually, this will only need to be done once. This step sets up your settings for using Git, such as forming your username and email that marks you as the one who is making your edits. 
Your preferred text editor, and whether or not you want to use these settings globally, is determined. Globally means these settings will be applied to all of your repositories.  
<br>**Repository** 
<br>A repository is the complete history of the changes made to a file that are specified to be reviewed. The repository can be kept in sync across 
multiple different computers. The history will be stored in your repository, usually ending in `.git`
<br><br>**Creating a Repository**
<br>Once you have configured Git, create the repository in which all versions of your file will be stored. This will be a directory. 
<br>First navigate to the root project folder, then utilize `git init` to initate the directory as a repository. 
<br>$`cd [path to your root project folder]`
<br>$`git init` 
<br>Avoid creating nested repositories. These are redundant, because when a repository is made, `Git` tracks that directory, and all files and 
subdirectories in that repository. In fact, if a nested repository is made, the outer repository may attempt to act on the inner repository. 
<br><br>**Basic structure of a Git Command**
<br>`git verb options`
<br> A git **verb** is a what we want `Git` to do for us, and an **option** is more information that may need to be provided for the `[verb]` to complete the task we want it to do. 
<br><br>**Accessing Git Help Manual**
<br>`Git` has a built in feature to access the `Git` help manual, to view subcommands and to see options. 
          This can be accessed by entering any of the following on the command line: 
<br><br>&ensp;&ensp;$ `git config -h`
<br>&ensp;&ensp;$ `git config --help`
<br>&ensp;&ensp;$ `git help` 
<br><br>`git revert` vs. `git checkout`
<br><br>**A Good Commit Message**
<br>Leaving a commit message is useful for both yourself and others. Try to keep it less than 50 characters, and convey: "if applied, this commit 
will..." to accurately describe your changes. 
<br><br>**Text Editors**
<br>Vim is the default text editor on many Git programs. There are numerous text editor programs you can use with Git that may need to be 
    downloaded. You will use a form of `git config` that is specific to that text editor to install it.  
<br>**Example Walkthrough of Committing to `Git`**
<br>Suppose you have made an edit to a text file `git_commit_example` and wish to commmit it. 
<br>First add the new change you made to the staging area. 
<br>`git add git_commit_example` 
<br>This will ensure Git is tracking your change. 
<br>Then commit this change by: 
<br>$ `git commit -m [your commit message here]` 
<br>This means any changes made that were added will be committed. Ensure to leave a `commit message` to describe the changes you have made. 
<br><br>**Useful Commands** 

|<div style="width:90px">Command</div>| Description| 
|-------|------------|
| `git log`| this will list a log of all commits in the terminal, including the commit's unique ID identifier, date and time, and your commit message|
|`git show`|-similar to `git log`, this will show a log of commits and tags, but will also where HEAD is at currently|
|`git status`|displays the state of the staging area and your working directory, including if there's modifications or not|
|`rm -rf`| the `rm` command will remove files or directories, while the `-rf` parameter will remove a repository<br>*use with caution, as this can delete a project!|
|`git diff`|-this show changes in your project<br>-you can use this to compare commits or changes in your working directory for instance|

<br>**Remote vs. Local Repositories and Git Push and Pull**
<br>A local repository is the versions of your file that you have on your computer. A remote repository is the versions of the file that is hosted on a server that all persons contributing to the project can access.
    A `git push` is when you transfer your local repository to the remote repository. A `git pull` is done when you copy the remote repository to your local repository.   
<br>**SSH - Creating a Secure Key Pair**
<br>You will need to have a secure key pair between your local repository and the remote repository. There is a public key, and your unique individual key. If a public key has not been made,
    that must be done. 
<br><br>**Conflicts**
<br>Merging is the process of potentially combining your edits to the original file you were editing. If there are conflicts that compete or interfere with each other, GitHub will highlight this 
and not allow your code to merge.  
<br><br>See this [link to Github tutorial for beginners](https://product.hubspot.com/blog/git-and-github-tutorial-for-beginners) for additional information. 

##How to `python`

!!! note "Summary"
    See <a href="Software-Carpentry.org">Software Carpentry</a> for instruction. Below are key take aways and what to look for in the link.
    This guide will provide an overview of the necessary concepts and functions needed to navigate and utilize Python, including what basic 
    variable types are, how to utilize functions, import libraries, Python's unique counting system, for loops, and conceptually how data visualization works with Python.  

**The Basics**
<br><br>**Variables**
<br>You can assign a value as a variable to later work with. When you call the name of the variable, Python
    will substitute the value of that variable. 
<br>To make a variable use the `=` sign. 
<br>For instance: `novel_wordcount = 35432` makes `novel_wordcount` a variable that has a value assigned to it. 
<br><br>**Data types**
<br>Python works with data. Three common types are:
<br>&ensp;&ensp;**integer** - an integer value, such as `71`, `2`, or `965`
<br>&ensp;&ensp;**string** - single or double quotes around text, such as 'this_is_a_string' or `34567` 
<br>&ensp;&ensp;**float** - fractional numbers expressed as decimals, such as `45.67` or `8976.11111` 
<br><br>**Functions**
<br>Python has a set of named functions that have instructions to perform a certain action when its name is typed in the command line. These are commands. One of the best uses of Python is for processing data. There are many different Python functions that can be
used to interact with your data. This will depend on the program you are using.
<br><br>**Importing Libraries**
<br>Python has libraries which are units of code that perform certain tasks. These will need to be imported, providing us with more tools than basic python would. For instance, `numpy` reads and interprets data. 
<br><br>**Counting + Arrays**
<br>Python starts counting from 0. This is important when referencing indices for arrays: ordered systems of values.
<br>An array will often depict data. In Python, an indice in an array is given by `[row, column]`. 
<br><br>**Commenting**
<br>The # symbol preceding a message is used for commenting, or lines that the shell will not interpret as a command.  
<br>**Making Lists**
<br>When making a list of values, use a `,` to separate values, and enclose in `[]`
<br>`[1, 2, 3, 4, 5]` 
<br><br>**Loops** 
<br>Loops are a process that will repeat the same action over and over for unique values in a set or range. 
<br>For loops are a type of loop, in a general pattern of: "for x variable, enact y on x" where x is a variable and y is a command. 
<br>The loop will process each element in the list one at a time. 
<br><br>**If Statements and Conditionals**
<br>Enabling an **if statement** means Python will only execute its given command if the **conditions** of the if statement are met.
<br>"and" and "or" can be used in the if statement. 
<br>`if x and y` means both x and y have to be true for the loop to execute. 
<br>`if x or y` means if x or y or both are true, the loop will execute its command. 
<br><br>**Data Visualization**: `matplotlib`
<br>This is one of Python's most commonly used data visualization libraries. More uses than can be listed here are available, such as basic graphing and statistical measures, or grouping multiple plots together. This program is useful for processing large amounts of data. Explore it to access the library's full capabilities.  
<br>**Functions**
<br>A function is a set of commands that will only be executed when called upon, following this format:
<br>You define the name of the function and parameters, and use `return` to state what you want done. 

## How to SSH
!!! Summary
    SSH or secure shell is a way to securely connect your computer to a remote server. This is done by using the shell. SSH needs to be installed on both your computer 
    and the server you are connecting to. This is commonly referred to as a client and server connection. 
**Important Notes**
<br>Depending upon your operating system, installation of SSH will vary. 
<br>Once an SSH server is installed, you can start your connection. 
<br>At the terminal line, type: 
<br>`ssh your_username@your_ip_address`
<br>Enter your password.
<br>If it is your first time connecting, it will ask if you wish to continue connecting. Type 'yes' and press the enter key. 
<br>Now, you should be connected. 
