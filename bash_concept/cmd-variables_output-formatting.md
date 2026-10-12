02 — Commands, Variables & Output Formatting

This chapter covers essential Linux commands and the Bash features used to store data, accept input, display information, and format terminal output.

Table of Contents
Essential Linux Commands
Variables
Quoting Rules
User Input with read
Special Parameters
Command Substitution
Output with echo
Formatted Output with printf
Printf Format Specifiers
Printf Escape Sequences
ANSI Colors and Text Styling
Date and Time Formatting
Redirection and Pipes
Common Mistakes
Practice Exercises
1. Essential Linux Commands

A command is an instruction executed by the shell or a program.

Many commands follow this general structure:

command [options] [arguments]


The square brackets indicate optional parts and are not typed literally.

Navigation and directory commands
Command	Purpose	Example
pwd	Display the current working directory	pwd
ls	List directory contents	ls -l
cd	Change the current directory	cd /home
mkdir	Create a directory	mkdir projects
rmdir	Remove an empty directory	rmdir empty_dir

Useful ls options:

ls -l       # Detailed listing
ls -a       # Include hidden entries
ls -lh      # Human-readable file sizes
ls -la      # Detailed listing including hidden entries

File management commands
Command	Purpose	Example
touch	Create an empty file or update timestamps	touch notes.txt
cp	Copy files or directories	cp a.txt b.txt
mv	Move or rename files	mv old.txt new.txt
rm	Remove files	rm notes.txt
cat	Display or concatenate file contents	cat notes.txt
less	View text interactively	less notes.txt
head	Display the beginning of a file	head -n 5 notes.txt
tail	Display the end of a file	tail -n 10 notes.txt
grep	Search text for matching patterns	grep "error" app.log
find	Search for files and directories	find . -name "*.sh"

Caution: rm can permanently remove files. Be especially careful with recursive deletion and force options.

System information commands
date          # Current date and time
whoami        # Current effective username
uname -a      # System and kernel information
hostname      # System hostname
uptime        # System uptime and load averages
df -h         # Filesystem disk usage
free -h       # Memory usage, if available on the system


Some commands and output details vary between Linux distributions and Unix-like systems.

File permissions
ls -l script.sh
chmod +x script.sh

ls -l displays file permissions.
chmod +x adds executable permission for the applicable permission classes.
./script.sh attempts to execute the script directly.

Remember that changing permissions and executing a script are separate operations.

2. Variables

Variables store values that can be reused throughout a script.

Basic syntax
VARIABLE_NAME="value"


There must be no spaces around = when assigning a variable.

Correct:

USER_NAME="Alex"
AGE=25
OPERATING_SYSTEM="Arch Linux"


Incorrect:

USER_NAME = "Alex"


Bash would interpret this as a command rather than a variable assignment.

Accessing variables

Use $VARIABLE_NAME or ${VARIABLE_NAME} to expand a variable.

USER_NAME="Alex"

echo "$USER_NAME"
echo "${USER_NAME}"


Both display:

Alex
Alex


Braces are especially useful when adjoining text to a variable name.

NAME="Alex"

echo "${NAME}_backup"


Output:

Alex_backup


Without braces, $NAME_backup would refer to a variable named NAME_backup.

Variable naming rules

Common conventions:

Start with a letter or underscore.
Use letters, digits, and underscores in names.
Do not use spaces in variable names.
Variable names are case-sensitive.

For example, USER_NAME, user_name, and User_Name are different variables.

Local variables

Inside a function, local limits a variable's scope to that function.

show_message() {
    local MESSAGE="Hello from a function"
    echo "$MESSAGE"
}

show_message


The local keyword is a Bash feature and is not available in strictly POSIX sh.

Read-only variables
readonly APP_NAME="Cloud Tools"

echo "$APP_NAME"


A read-only variable cannot normally be reassigned.

3. Quoting Rules

Quoting controls how Bash interprets variable expansions, spaces, and special characters.

Double quotes: "..."

Double quotes allow variable expansion and command substitution while preserving most embedded whitespace.

NAME="Alex"

echo "Hello, $NAME"
echo "Current directory: $(pwd)"

Single quotes: '...'

Single quotes preserve the literal characters inside them.

NAME="Alex"

echo 'Hello, $NAME'


Output:

Hello, $NAME


The variable is not expanded.

Unquoted expansions

Unquoted variable expansions may undergo word splitting and pathname expansion.

FILE_NAME="project notes.txt"

cat "$FILE_NAME"


Quoting preserves the filename as one argument.

Best practice: Quote variable expansions by default unless you intentionally require splitting or wildcard expansion.

4. User Input with read

The read builtin collects input from standard input.

Basic syntax
read VARIABLE_NAME


Example:

printf 'Enter your name: '
read USER_NAME

printf 'Hello, %s!\n' "$USER_NAME"


If the user enters Alex, the output is:

Enter your name: Alex
Hello, Alex!

Using read -p

The -p option displays a prompt before reading input.

read -p "Enter your name: " USER_NAME
printf 'Welcome, %s!\n' "$USER_NAME"

Reading a password

Use -s to suppress the display of typed characters.

read -r -s -p "Enter password: " PASSWORD
printf '\n'


This is a basic illustration of silent input, not a complete secure password-management system. Avoid printing or logging passwords.

Why use -r?

The -r option prevents read from treating backslashes as escape characters.

For ordinary text input, this is usually the preferred form:

read -r -p "Enter a filename: " FILE_NAME

Reading multiple values
read -r -p "Enter your first and last name: " FIRST_NAME LAST_NAME

printf 'First: %s\nLast: %s\n' "$FIRST_NAME" "$LAST_NAME"


By default, read splits input according to IFS, the internal field separator.

5. Special Parameters

Bash provides special parameters that expose script arguments and execution information.

Parameter	Meaning
$0	Name or path used to invoke the script
$1, $2, ...	First, second, and subsequent positional parameters
$#	Number of positional parameters
"$@"	All positional parameters, preserving each as a separate word when quoted
"$*"	All positional parameters combined into one word when quoted
$?	Exit status of the most recently executed command
$$	Process ID of the current Bash process
$!	Process ID of the most recently started asynchronous command

Example script:

#!/bin/bash

printf 'Script: %s\n' "$0"
printf 'First argument: %s\n' "${1:-not provided}"
printf 'Argument count: %s\n' "$#"


Run it:

bash example.sh report.txt


The first positional argument is report.txt.

Why "$@" matters
for ARGUMENT in "$@"; do
    printf 'Argument: %s\n' "$ARGUMENT"
done


If the script is invoked as:

bash example.sh "hello world" report.txt


The loop processes two arguments, preserving "hello world" as one argument.

6. Command Substitution

Command substitution captures the standard output of a command.

Syntax
VARIABLE="$(command)"


Example:

CURRENT_DATE="$(date '+%A, %B %d, %Y')"
CURRENT_DIR="$(pwd)"

printf 'Date: %s\n' "$CURRENT_DATE"
printf 'Directory: %s\n' "$CURRENT_DIR"


Bash runs each command and substitutes its standard output into the assignment.

Prefer $(...) over legacy backticks because it is easier to read and nest.

Important: Command substitution removes trailing newline characters from the captured output. It does not automatically capture standard error.

7. Output with echo

echo displays text.

Basic usage
echo "Hello, World!"
echo "Welcome to Linux."


Each invocation normally adds a newline at the end.

Displaying variables
NAME="Alex"
echo "Hello, $NAME"

Suppressing the trailing newline

Some implementations support -n:

echo -n "Loading..."


The next output may continue on the same line.

Escape interpretation

Support for options such as -e and escape sequences varies between implementations. Avoid relying on echo for complex formatting.

For predictable output, prefer printf when formatting, escaping, or portability matters.

8. Formatted Output with printf

printf produces formatted output according to a format string.

Unlike echo, Bash's printf does not automatically append a newline.

Basic syntax
printf FORMAT [ARGUMENTS...]


Example:

printf 'Hello, World!\n'


Output:

Hello, World!


The \n inserts the newline.

Using variables
NAME="Alex"
AGE=25

printf 'Name: %s\nAge: %d\n' "$NAME" "$AGE"


Output:

Name: Alex
Age: 25

Understanding the format string
printf 'Name: %s\n' "$NAME"

printf is the builtin command.
'Name: %s\n' is the format string.
%s is a string conversion specifier.
\n adds a newline.
"$NAME" supplies the value for %s.
Multiple arguments
printf '%s is using %s\n' "Alex" "Linux"


Output:

Alex is using Linux


Each conversion specifier consumes a corresponding argument.

Important rule

The format string is not merely ordinary text: conversion specifiers and recognized escape sequences have special meanings.

For user-controlled text, pass the text as an argument instead of using it as the format string.

Prefer:

printf '%s\n' "$USER_INPUT"


Avoid:

printf "$USER_INPUT"


The second form may interpret characters in the input as formatting instructions.

9. Printf Format Specifiers

Format specifiers tell printf how to represent an argument.

Common conversion specifiers
Specifier	Meaning	Example
%s	String	printf '%s\n' "Linux"
%d	Signed decimal integer	printf '%d\n' 42
%i	Integer conversion	printf '%i\n' 42
%f	Floating-point number	printf '%f\n' 3.14
%c	Character	printf '%c\n' A
%x	Lowercase hexadecimal	printf '%x\n' 255
%X	Uppercase hexadecimal	printf '%X\n' 255
%o	Octal representation	printf '%o\n' 8
%%	Literal percent sign	printf '100%%\n'

Bash's builtin printf supports common conversions including %s, %d, %f, %x, and %o. Conversion behavior and supported extensions can differ from other implementations of printf.

Integer example
COUNT=42
printf 'Count: %d\n' "$COUNT"


Output:

Count: 42

Floating-point precision
printf 'Value: %.2f\n' 3.14159


Output:

Value: 3.14


The .2 specifies two digits after the decimal point.

Width and alignment
printf '|%10s|\n' "Linux"
printf '|%-10s|\n' "Linux"


Output:

|     Linux|
|Linux     |

%10s uses a minimum field width of 10 and right-aligns the string.
%-10s uses a minimum field width of 10 and left-aligns the string.
Zero-padding integers
printf '%05d\n' 42


Output:

00042


The 5 sets the minimum field width, and 0 requests zero-padding for this numeric conversion.

Formatting a table
printf '%-15s %8s\n' "NAME" "SCORE"
printf '%-15s %8d\n' "Alex" 92
printf '%-15s %8d\n' "Sam" 85


Output:

NAME                SCORE
Alex                   92
Sam                    85


This is useful for reports, status displays, and command-line dashboards.

Reusing a format string

Bash's printf reuses a format string when more arguments remain than the format requires.

printf '%s\n' "Alpha" "Beta" "Gamma"


Output:

Alpha
Beta
Gamma

10. Printf Escape Sequences

Escape sequences represent special characters or control actions.

Sequence	Meaning
\n	Newline
\t	Horizontal tab
\r	Carriage return
\\	Backslash
\a	Alert character; may produce a beep
\b	Backspace
\v	Vertical tab
\f	Form feed
Newline
printf 'First line\nSecond line\n'

Tab
printf 'NAME\tAGE\nAlex\t25\n'

Carriage return
printf 'Progress: 50%%\r'


A carriage return moves the cursor to the beginning of the current line. It does not itself erase existing text.

Literal backslash
printf 'Path separator: \\\n'


Output:

Path separator: \

Percent sign

A literal percent sign in a printf format string is represented by %%.

printf 'Completed: 100%%\n'


Output:

Completed: 100%

11. ANSI Colors and Text Styling

ANSI escape sequences allow compatible terminals to display colored text and apply styles.

Basic structure
ESC[STYLE;FOREGROUNDm


The ESC character is ASCII escape, represented in Bash's printf format strings as \033 or \e.

For example:

printf '\033[1;36mHello, Linux!\033[0m\n'

\033[ begins the control sequence.
1 requests bold or increased intensity.
36 selects cyan foreground.
m ends the styling sequence.
\033[0m resets terminal styling.

The appearance depends on terminal support and configuration.

Basic foreground colors
Code	Color
30	Black
31	Red
32	Green
33	Yellow
34	Blue
35	Magenta
36	Cyan
37	White
Basic background colors
Code	Color
40	Black
41	Red
42	Green
43	Yellow
44	Blue
45	Magenta
46	Cyan
47	White
Common text styles
Code	Effect
0	Reset styling
1	Bold or increased intensity
2	Faint
3	Italic, if supported
4	Underline
7	Inverse video

Support for some styles varies between terminals.

Using variables for colors
RESET='\033[0m'
BOLD='\033[1m'
RED='\033[31m'
GREEN='\033[32m'
CYAN='\033[36m'
YELLOW='\033[33m'

printf '%b\n' "${CYAN}Information message${RESET}"
printf '%b\n' "${GREEN}Operation successful${RESET}"
printf '%b\n' "${YELLOW}Warning message${RESET}"
printf '%b\n' "${RED}Error message${RESET}"


Here %b tells Bash's builtin printf to interpret backslash escapes in the corresponding argument. That is useful when the color variables contain literal backslash escape sequences.

Another approach is to put the escape sequences directly in the format string:

printf '\033[1;32mSuccess!\033[0m\n'


Best practice: Reset styling after colored output so subsequent terminal text does not unintentionally inherit it.

12. Date and Time Formatting

The date command can format date and time using conversion specifiers.

Current date and time
date

Common date specifiers
Specifier	Meaning
%A	Full weekday name
%a	Abbreviated weekday name
%B	Full month name
%b	Abbreviated month name
%d	Day of the month, zero-padded
%m	Month number
%Y	Four-digit year
%y	Two-digit year
%H	Hour, 24-hour clock
%I	Hour, 12-hour clock
%M	Minutes
%S	Seconds
%p	AM or PM marker, depending on locale
%r	Locale-specific 12-hour time representation

Example:

date '+%A, %B %d, %Y'


Possible output:

Sunday, October 11, 2026


The exact output depends on the current date and locale.

Capturing formatted date output
CURRENT_DATE="$(date '+%A, %B %d, %Y | %r')"

printf 'Today is: %s\n' "$CURRENT_DATE"


This combines command substitution, variable assignment, date formatting, and printf.

13. Redirection and Pipes

Shell redirection controls where command input and output go.

Standard output redirection
echo "Hello" > output.txt


The > operator redirects standard output to a file, replacing its previous contents.

Append output
echo "Another line" >> output.txt


The >> operator appends output to the file.

Standard error
ls missing_file.txt 2> errors.txt


File descriptor 2 represents standard error.

Redirect both standard output and standard error
command > output.log 2>&1


This sends standard output to output.log, then redirects standard error to the same destination.

Pipes

A pipe sends the standard output of one command to the standard input of another.

ls -l | grep '\.sh$'


This filters a directory listing for lines ending in .sh. For scripts that need robust filename handling, consider using find instead of parsing formatted ls output.

14. Common Mistakes
Mistake 1: Forgetting the newline in printf
printf 'Hello'
printf 'World'


Output:

HelloWorld


Correct:

printf 'Hello\n'
printf 'World\n'

Mistake 2: Using %d for ordinary text

Incorrect:

printf '%d\n' "Linux"


Use %s for strings:

printf '%s\n' "Linux"

Mistake 3: Treating user input as a format string

Risky:

printf "$USER_INPUT"


Safer:

printf '%s\n' "$USER_INPUT"

Mistake 4: Forgetting to reset ANSI styling

Risky:

printf '\033[31mError\n'


Better:

printf '\033[31mError\033[0m\n'

Mistake 5: Forgetting quotes around variable expansions

Risky:

cat $FILE_NAME


Usually preferable:

cat "$FILE_NAME"

Mistake 6: Assuming every echo supports -e

Implementations differ in how echo handles options and escape sequences. Use printf when predictable formatting is required.

15. Practice Exercises
Exercise 1: System information

Write a script that prints the current date, username, and working directory.

Exercise 2: User input

Ask the user for their name and display a greeting using printf.

Exercise 3: Formatted table

Use printf to display a table with columns for a filename, file type, and status.

Exercise 4: Number formatting

Display an integer with leading zeros and a floating-point number rounded to two decimal places.

Exercise 5: Colored messages

Create variables for red, green, yellow, and reset sequences. Print an error, success, and warning message.

Exercise 6: Date formatting

Display the current weekday, full month name, year, and 12-hour time.

Exercise 7: Output redirection

Write a script that saves a message to a file and appends a second message.

Exercise 8: Combine the concepts

Create a terminal welcome script that:

Clears the terminal.
Prints a welcome message.
Displays the current date and time.
Uses cyan and yellow text.
Resets styling after each message.
Chapter Summary

This chapter introduced:

Essential Linux commands and file operations.
Variables, quoting, and user input.
Positional parameters and command substitution.
echo and formatted output with printf.
Conversion specifiers, field widths, precision, and escape sequences.
ANSI foreground colors, background colors, and text styles.
Date and time formatting.
Output redirection, standard error, and pipes.

Next chapter: Control flow, conditional statements, loops, functions, debugging, and practical Bash scripting projects.
