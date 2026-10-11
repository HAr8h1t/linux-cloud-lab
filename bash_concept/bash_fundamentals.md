01 — Bash Fundamentals
1. Introduction to Bash Scripting
What is Bash?

Bash (Bourne Again SHell) is a command-line shell and scripting language commonly used on Linux and other Unix-like operating systems.

It allows users to interact with the operating system through commands and combine those commands into scripts to automate tasks.

Instead of manually executing the same commands repeatedly, we can write a Bash script to perform them automatically.

What can Bash scripting do?
Automate repetitive tasks.
Create, copy, move, and manage files.
Collect system information.
Execute commands in a particular sequence.
Process text and command output.
Work with variables and user input.
Make decisions using conditions.
Repeat operations using loops.
Organize reusable code using functions.
Automate Linux administration and infrastructure workflows.
2. Shell vs Terminal vs Bash Script

These terms are related but have different meanings.

Term	Meaning
Terminal	An application or interface through which we interact with a shell.
Shell	A program that interprets commands and arranges their execution.
Bash	A particular shell that also provides a scripting language.
Bash script	A text file containing commands and Bash programming constructs.
Linux command	A command or utility used to perform a task on the system.

For example, when we type ls into a terminal, Bash interprets the command and runs the appropriate utility.

Other shells include sh, zsh, and fish. Their syntax and features differ, so Bash-specific scripts may not run correctly in other shells.

3. What Is a Bash Script?

A Bash script is a plain-text file containing commands that Bash can execute.

Example:

#!/bin/bash

echo "Hello, World!"
date
pwd


Save the file as hello.sh.

When executed, it displays a greeting, prints the current date and time, and displays the current working directory.

A Bash script can contain simple commands as well as variables, conditions, loops, functions, and other programming constructs.

4. Understanding the Shebang

The first line of a Bash script commonly looks like this:

#!/bin/bash


This line is called the shebang. It identifies the interpreter used when the script is executed directly.

Breakdown:

#! identifies an interpreter directive.
/bin/bash specifies the path to the Bash executable on many Linux systems.

An alternative is:

#!/usr/bin/env bash


This uses env to locate Bash through the environment's PATH.

Use the form appropriate for your system and portability requirements.

Important: The shebang is used for direct execution, such as ./hello.sh. When you run bash hello.sh, you explicitly choose Bash as the interpreter.

5. Creating and Executing a Script
Step 1: Create a file
nano hello.sh


Enter:

#!/bin/bash

USER_NAME="Alex"

echo "Hello, $USER_NAME!"


Save and exit the editor.

Step 2: Execute using Bash
bash hello.sh


This executes the script through Bash without requiring executable permission on the file.

Step 3: Add executable permission
chmod +x hello.sh


The chmod command changes file permissions. The +x option adds executable permission for the applicable user, group, and other permission classes.

Step 4: Execute directly
./hello.sh


The ./ prefix tells the shell to execute the file from the current directory.

Remember: chmod +x changes permissions; it does not execute the script.

6. Basic Script Structure

Consider this example:

#!/bin/bash

# Store a message in a variable.
MESSAGE="Welcome to Linux!"

# Display the message.
echo "$MESSAGE"


Let's examine the components.

Comments
# This is a comment.


Comments explain the purpose of code and are generally ignored by Bash.

Variables
MESSAGE="Welcome to Linux!"


Variables store values that can be reused.

Do not put spaces around the assignment operator.

Correct:

MESSAGE="Hello"


Incorrect:

MESSAGE = "Hello"

Command execution
echo "$MESSAGE"


This displays the value stored in MESSAGE.

Output:

Welcome to Linux!

7. Commands, Options, and Arguments

Many commands follow this general pattern:

command [options] [arguments]


The square brackets indicate optional parts; they are not typed literally.

Example:

ls -l /home

ls is the command.
-l is an option requesting a detailed listing.
/home is the argument specifying the directory.

More examples:

pwd
ls -a
mkdir projects
touch notes.txt
cp notes.txt backup.txt
mv backup.txt old-notes.txt


Be careful with destructive commands such as rm, especially when using recursive or force options.

8. Quoting Basics

Quoting controls how Bash interprets text and variable expansions.

Double quotes

Double quotes allow variable expansion and command substitution.

USER_NAME="Alex"

echo "Hello, $USER_NAME"
echo "Current directory: $(pwd)"

Single quotes

Single quotes preserve the literal characters inside them.

USER_NAME="Alex"

echo 'Hello, $USER_NAME'


Output:

Hello, $USER_NAME


The variable is not expanded.

Why quote variable expansions?

Consider a filename containing spaces:

FILE_NAME="my notes.txt"


Prefer:

cat "$FILE_NAME"


Without quotes, Bash can split the value into separate words.

Beginner rule: Quote variable expansions unless you deliberately need word splitting or wildcard expansion.

9. Command Substitution

Command substitution captures the standard output of a command and substitutes it into another expression.

Syntax:

VARIABLE="$(command)"


Example:

CURRENT_DATE="$(date '+%A, %B %d, %Y')"

echo "$CURRENT_DATE"


Another example:

CURRENT_DIR="$(pwd)"


Here, the output of pwd is stored in CURRENT_DIR.

Prefer $(...) over legacy backtick syntax because it is easier to read and nest.

10. Exit Status

Commands return an exit status when they finish.

By convention:

0 means success.
A nonzero value indicates failure or another condition.

The special parameter $? contains the exit status of the most recently executed command.

Example:

mkdir practice_directory

echo "$?"


If the directory is created successfully, the status is normally 0. If it already exists, the command will ordinarily fail and return a nonzero status.

A clearer approach is to test the command directly:

if mkdir practice_directory; then
    echo "Directory created successfully."
else
    echo "Directory creation failed."
fi


Important: $? changes after subsequent commands, so inspect it immediately if you need its value.

11. Basic Debugging

Bash provides useful options for troubleshooting scripts.

Print commands as they execute
bash -x hello.sh


This helps reveal how Bash expands variables and executes commands.

Check syntax without executing the script
bash -n hello.sh


This checks for many syntax errors, but it does not guarantee that the script will behave correctly.

Check the exit status
bash hello.sh
echo "$?"


Use these techniques when a script does not behave as expected.

12. Common Beginner Mistakes
Mistake 1: Spaces around assignment

Incorrect:

NAME = "Alex"


Correct:

NAME="Alex"

Mistake 2: Forgetting quotes

Risky:

echo $FILE_NAME


Usually preferable:

echo "$FILE_NAME"

Mistake 3: Using the wrong interpreter

A script containing Bash-specific syntax may not work in another shell.

For example, Bash supports arithmetic for loops:

for ((i = 0; i < 5; i++)); do
    echo "$i"
done


This syntax is not portable POSIX shell syntax.

Mistake 4: Confusing permissions with execution
chmod +x hello.sh


This makes the file executable where permitted. It does not run the script.

13. Practice Exercises

Try these exercises before moving to the next chapter.

Create a script that displays Hello, Linux!.
Display the current date, username, and working directory.
Store a person's name in a variable and display a greeting.
Capture the output of pwd using command substitution.
Create a directory and report whether the operation succeeded.
Run a script using both bash script.sh and ./script.sh.
Introduce a syntax error and use bash -n to investigate it.
Run a script with bash -x and observe the execution trace.
14. Chapter Summary

In this chapter, we learned:

What Bash is and how it differs from a terminal.
How Bash scripts are structured.
What the shebang does.
How to create and execute scripts.
How variables, comments, commands, and quoting work.
How to capture command output.
How to inspect exit statuses.
How to debug basic scripts.

Next chapter: Commands, variables, user input, output formatting, printf, and terminal colors.
