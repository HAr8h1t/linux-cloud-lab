Bash Scripting — Error Handling, Debugging, Arrays, and Automation

Section: Bash Scripting
Part of: cloud-archi
Level: Beginner to Intermediate

1. Introduction

A useful Bash script should do more than execute commands. It should also handle failures, validate inputs, process files safely, and make it easy to identify problems.

This chapter covers:

Exit statuses and error handling
Debugging techniques
Arrays and string processing
File and directory operations
Pipes, redirection, and text-processing tools
ANSI colors and terminal animation
Scheduling and automation
Practical Linux administration projects
Script security and best practices
2. Exit Statuses

Every command returns an exit status when it finishes.

By convention:

0 means success.
A nonzero status indicates failure or a condition that was not successful.

Check the status using $?.

ls /etc
echo "$?"


The output is normally 0 if the directory listing succeeds.

Try a nonexistent directory:

ls /directory_that_does_not_exist
echo "$?"


The command normally returns a nonzero status.

Important: $? must be checked immediately after the command whose status you need. Running another command changes it.

2.1 Use if to Check Success
if mkdir new_folder; then
    echo "Directory created successfully"
else
    echo "Failed to create directory"
fi


This is generally clearer than checking $? separately.

2.2 The && and || Operators

&& runs the next command only if the previous command succeeds.

mkdir backup && echo "Backup directory created"


|| runs the next command only if the previous command fails.

mkdir backup || echo "Could not create backup directory"


These operators are useful for short command chains. Use if when the logic becomes more complicated.

3. Handling Errors
3.1 The exit Command

Use exit to terminate a script with a specific status.

#!/bin/bash

if [[ ! -f "$1" ]]; then
    echo "Error: file not found." >&2
    exit 1
fi

echo "File exists."
exit 0


Usage:

bash check_file.sh example.txt


Here:

exit 0 signals success.
exit 1 signals a general failure.
>&2 sends the error message to standard error.
3.2 Standard Output and Standard Error

Linux commands commonly use three standard streams.

Stream	File descriptor	Purpose
Standard input	0	Input to a command
Standard output	1	Normal output
Standard error	2	Error messages

Examples:

echo "Normal message"
echo "Error message" >&2


Redirect output:

command > output.txt


Redirect errors:

command 2> errors.txt


Redirect both output streams to one file in Bash:

command > output.txt 2>&1


Or use:

command &> output.txt


Append output instead of overwriting:

command >> output.txt 2>&1


Be careful: > overwrites an existing file.

3.3 The /dev/null Device

/dev/null discards data written to it.

command > /dev/null 2>&1


This discards both standard output and standard error.

Use it only when you intentionally do not need those messages. During development, retaining error output is often more helpful.

4. Safer Script Defaults

A common starting point for Bash scripts is:

#!/usr/bin/env bash

set -o nounset
set -o pipefail


These settings improve error detection.

nounset makes many references to unset variables produce errors.
pipefail makes a pipeline report failure if a command within it fails, rather than considering only the last command.

You may also see:

set -e


This requests that Bash exit when certain commands fail. However, set -e has exceptions and behaves differently in contexts such as conditionals, loops, and parts of pipelines. It is not a complete error-handling strategy.

A more complete example:

#!/usr/bin/env bash

set -o nounset
set -o pipefail

main() {
    printf '%s\n' "Starting script..."

    if ! mkdir -p logs; then
        printf '%s\n' "Error: unable to create logs directory." >&2
        return 1
    fi

    printf '%s\n' "Directory ready."
}

main "$@"


Best practice: Use explicit checks for important operations such as deleting files, changing permissions, writing backups, and modifying system settings.

5. Debugging Bash Scripts

Debugging means finding and correcting problems in a script.

5.1 Syntax Checking

Use:

bash -n script.sh


This checks Bash syntax without executing the script.

5.2 Execution Tracing

Use:

bash -x script.sh


Bash prints commands as they are executed, which helps reveal unexpected variable values or control flow.

You can also enable tracing inside a script:

set -x
echo "Debugging enabled"

NAME="Alex"
echo "$NAME"

set +x


Tracing can expose passwords, tokens, and other sensitive values. Avoid enabling it around secret-handling code.

5.3 Display Variables While Debugging
USERNAME="Alex"
COUNT=5

printf 'USERNAME=%q\n' "$USERNAME"
printf 'COUNT=%q\n' "$COUNT"


The %q format specifier displays a value in a form that can help reveal whitespace and special characters.

5.4 Use ShellCheck

ShellCheck is a static-analysis tool that identifies common Bash and shell scripting problems.

Install it using your Linux distribution's package manager when available.

Run:

shellcheck script.sh


ShellCheck can identify issues involving quoting, variable usage, portability, and common shell pitfalls.

6. Arrays

Arrays store multiple values in a single variable.

6.1 Creating an Indexed Array
FRUITS=("Apple" "Banana" "Mango")

echo "${FRUITS[0]}"
echo "${FRUITS[1]}"
echo "${FRUITS[2]}"


Bash arrays use zero-based indexing.

6.2 Accessing All Elements
FRUITS=("Apple" "Banana" "Mango")

printf '%s\n' "${FRUITS[@]}"


Use "${ARRAY[@]}" to preserve each array element as a separate word.

6.3 Array Length
FRUITS=("Apple" "Banana" "Mango")

echo "${#FRUITS[@]}"


Output:

3

6.4 Loop Through an Array
SERVICES=("nginx" "ssh" "cron")

for SERVICE in "${SERVICES[@]}"; do
    printf 'Service: %s\n' "$SERVICE"
done

6.5 Adding Elements
FRUITS=("Apple" "Banana")

FRUITS+=("Mango")
FRUITS+=("Orange")

printf '%s\n' "${FRUITS[@]}"

6.6 Associative Arrays

Associative arrays store key-value pairs. They require Bash 4 or later.

declare -A SERVER_INFO

SERVER_INFO[OS]="Linux"
SERVER_INFO[ENVIRONMENT]="Production"
SERVER_INFO[REGION]="Asia"

echo "${SERVER_INFO[OS]}"
echo "${SERVER_INFO[ENVIRONMENT]}"


To iterate through the keys:

for KEY in "${!SERVER_INFO[@]}"; do
    printf '%s: %s\n' "$KEY" "${SERVER_INFO[$KEY]}"
done


Associative-array iteration order is not guaranteed.

7. String Processing

Bash supports useful operations for manipulating strings.

7.1 String Length
TEXT="Hello Bash"

echo "${#TEXT}"

7.2 Extract a Substring
TEXT="LinuxScripting"

echo "${TEXT:0:5}"


Output:

Linux


The first number is the starting position, and the second is the number of characters to extract.

7.3 Replace Text
TEXT="I like Python"

echo "${TEXT/Python/Bash}"


Output:

I like Bash


This replaces the first matching occurrence.

To replace all matching occurrences:

TEXT="cat cat cat"
echo "${TEXT//cat/dog}"

7.4 Remove Prefixes and Suffixes
FILE="report.txt"

echo "${FILE%.txt}"


Output:

report


${VARIABLE%PATTERN} removes the shortest matching suffix.

${VARIABLE#PATTERN} removes the shortest matching prefix.

These operations use shell pattern matching, not general regular expressions.

8. File and Directory Operations
8.1 Create Directories
mkdir reports
mkdir -p reports/2026/october


The -p option creates parent directories when needed and avoids an error when the target directory already exists.

8.2 Create and Read Files
touch notes.txt
printf '%s\n' "Bash scripting notes" > notes.txt
cat notes.txt


> replaces the file's existing content.

Use >> to append:

printf '%s\n' "New note" >> notes.txt

8.3 Copy, Move, and Delete
cp source.txt backup.txt
mv backup.txt archived.txt
rm archived.txt


For directories:

cp -r source_dir backup_dir
mv backup_dir archived_dir


Warning: rm can permanently delete files. Always verify paths before using deletion commands, especially when variables or wildcards are involved.

8.4 Check Before Processing a File
FILE="data.txt"

if [[ -f "$FILE" && -r "$FILE" ]]; then
    echo "File exists and is readable"
else
    echo "File is missing or unreadable" >&2
fi


Checking a path and then using it is not a guarantee that the file will remain unchanged. For critical operations, handle command failures as well.

9. Finding and Processing Text
9.1 grep

Search for matching text:

grep "error" application.log


Case-insensitive search:

grep -i "error" application.log


Search recursively:

grep -r "TODO" ./src


Display matching line numbers:

grep -n "error" application.log

9.2 find

Find files by name:

find . -type f -name "*.log"


Find directories:

find . -type d -name "backup"


Find files modified within the last day:

find . -type f -mtime -1


Use -exec to process matches:

find . -type f -name "*.log" -exec wc -l {} +


This counts lines in the matching files. Always understand a find expression before combining it with destructive commands.

9.3 cut

Extract fields separated by a delimiter:

printf 'Alex:25:Linux\n' | cut -d ':' -f 1


Output:

Alex

9.4 sort and uniq
sort names.txt
sort names.txt | uniq


uniq removes adjacent duplicate lines, so sorting first is useful when you want to remove duplicates from an entire file.

Count occurrences:

sort names.txt | uniq -c

9.5 wc

Count lines, words, or bytes:

wc -l application.log
wc -w notes.txt
wc -c notes.txt

9.6 awk

awk is useful for extracting columns and performing simple calculations.

printf 'Alex 25\nSam 30\n' | awk '{print $1}'


Output:

Alex
Sam


Calculate a sum:

printf '10\n20\n30\n' | awk '{sum += $1} END {print sum}'


Output:

60

9.7 sed

Replace text in a stream:

printf '%s\n' "I like Python" | sed 's/Python/Bash/'


Output:

I like Bash


These utilities are useful for log analysis, configuration processing, and report generation.

10. Pipes and Redirection

A pipe passes one command's standard output to another command's standard input.

ps aux | grep nginx


A practical pipeline:

grep -i "error" application.log | wc -l


This counts lines containing the text error, ignoring case.

Save pipeline output:

grep -i "error" application.log > errors.txt


Append output to a log:

date >> activity.log

Important Pipeline Behavior

By default, the exit status of a pipeline is usually the status of its last command.

For example, an earlier command may fail while the last command succeeds.

Use:

set -o pipefail


when the script should detect failures anywhere in a pipeline.

Also remember that commands in a pipeline may execute in subshell environments. Variables changed inside a pipeline loop may not be available afterward in the calling shell.

11. ANSI Colors in the Terminal

ANSI escape sequences allow compatible terminals to display colored text and text effects.

11.1 Basic Colors
#!/usr/bin/env bash

RED=$'\033[31m'
GREEN=$'\033[32m'
YELLOW=$'\033[33m'
BLUE=$'\033[34m'
RESET=$'\033[0m'

printf '%sError message%s\n' "$RED" "$RESET"
printf '%sSuccess message%s\n' "$GREEN" "$RESET"
printf '%sWarning message%s\n' "$YELLOW" "$RESET"
printf '%sInformation%s\n' "$BLUE" "$RESET"


The RESET sequence restores the default text styling.

11.2 Common ANSI Codes
Code	Effect
\033[0m	Reset formatting
\033[1m	Bold
\033[31m	Red foreground
\033[32m	Green foreground
\033[33m	Yellow foreground
\033[34m	Blue foreground
\033[35m	Magenta foreground
\033[36m	Cyan foreground
\033[41m	Red background
\033[42m	Green background

Use printf for predictable escape-sequence handling. Terminal support and color settings may vary, so important information should not rely on color alone.

12. Terminal Animation

Simple animations can make command-line scripts more interactive.

12.1 Loading Animation
#!/usr/bin/env bash

printf 'Loading'

for (( i = 0; i < 3; i++ )); do
    printf '.'
    sleep 1
done

printf '\nDone!\n'

12.2 Progress Counter
#!/usr/bin/env bash

for (( i = 1; i <= 5; i++ )); do
    printf '\rProgress: %d/5' "$i"
    sleep 1
done

printf '\nCompleted!\n'


\r moves the cursor to the beginning of the current line.

12.3 Animated Welcome Message
#!/usr/bin/env bash

TEXT="Welcome to Bash!"

for (( i = 0; i < ${#TEXT}; i++ )); do
    printf '%s' "${TEXT:i:1}"
    sleep 0.05
done

printf '\n'


This prints the message one character at a time.

Use animations sparingly in automation scripts because they can make logs harder to read and may not work well in non-interactive environments.

13. Environment Variables

Environment variables provide information to programs and can configure their behavior.

Examples:

echo "$HOME"
echo "$PATH"
echo "$USER"
echo "$PWD"

HOME: home directory.
PATH: directories searched for executable commands.
USER: commonly the current username.
PWD: current working directory.

Set a shell variable:

APP_ENV="development"


Export it to child processes:

export APP_ENV="production"


A child process receives exported variables through its environment. Changes made by the child do not normally change the parent shell's environment.

14. Temporary Files and Cleanup

Temporary files can be useful when a script needs to store intermediate results.

Use mktemp instead of inventing a predictable temporary filename.

#!/usr/bin/env bash

set -o nounset

TEMP_FILE=$(mktemp) || {
    printf '%s\n' "Unable to create temporary file." >&2
    exit 1
}

cleanup() {
    rm -f -- "$TEMP_FILE"
}

trap cleanup EXIT

printf '%s\n' "Temporary data" > "$TEMP_FILE"
cat "$TEMP_FILE"


Key concepts:

mktemp creates a temporary file with a unique name.
trap registers a handler for a shell event.
EXIT runs the cleanup handler when the shell exits.
-- marks the end of command options for commands that support it.

For temporary directories, use mktemp -d.

Cleanup logic is particularly important when scripts fail before completing their normal work.

15. Scheduling Scripts With Cron

Cron is commonly used to schedule recurring tasks on Linux systems.

Open the current user's crontab:

crontab -e


Example:

0 2 * * * /home/alex/scripts/backup.sh


This requests execution every day at 02:00 according to the cron service's scheduling and timezone configuration.

The five time fields are:

minute hour day-of-month month day-of-week


Another example:

*/15 * * * * /home/alex/scripts/check_status.sh


This requests execution every 15 minutes.

Important Cron Tips
Use absolute paths for scripts and important files.
Make sure the script has suitable permissions.
Set required environment variables explicitly.
Do not assume cron has the same PATH as an interactive terminal.
Log output when useful.

Example:

0 2 * * * /home/alex/scripts/backup.sh >> /home/alex/logs/backup.log 2>&1


The script's output and error messages are appended to the log.

Cron availability, configuration, and behavior can differ between Linux distributions and environments.

16. Practical Project: System Information Report

Create system_report.sh:

#!/usr/bin/env bash

set -o nounset
set -o pipefail

printf '%s\n' "===== SYSTEM REPORT ====="

printf 'Date: %s\n' "$(date)"
printf 'Hostname: %s\n' "$(hostname)"
printf 'User: %s\n' "$(id -un)"
printf 'Working directory: %s\n' "$PWD"

printf '\n%s\n' "Disk usage:"
df -h .

printf '\n%s\n' "Memory information:"
if command -v free >/dev/null 2>&1; then
    free -h
else
    printf '%s\n' "The free command is unavailable."
fi

printf '\n%s\n' "Uptime:"
uptime

printf '\n%s\n' "===== REPORT COMPLETE ====="


Run:

chmod +x system_report.sh
./system_report.sh


This project introduces command substitution, environment variables, command detection, output formatting, and basic system administration.

17. Practical Project: Log Error Counter

Create error_count.sh:

#!/usr/bin/env bash

set -o nounset
set -o pipefail

if (( $# != 1 )); then
    printf 'Usage: %s <log-file>\n' "$0" >&2
    exit 1
fi

LOG_FILE="$1"

if [[ ! -f "$LOG_FILE" || ! -r "$LOG_FILE" ]]; then
    printf 'Error: file is missing or unreadable: %s\n' "$LOG_FILE" >&2
    exit 1
fi

if ! ERROR_COUNT=$(grep -ic 'error' "$LOG_FILE"); then
    STATUS=$?

    # This branch is entered when grep fails, but a direct assignment
    # in an if-condition requires care when retrieving the original status.
    printf '%s\n' "Unable to search the log file." >&2
    exit 1
fi

printf 'File: %s\n' "$LOG_FILE"
printf 'Lines containing "error": %s\n' "$ERROR_COUNT"


Note: grep returns status 1 when it finds no matches, which is not necessarily an operational error. A robust script should distinguish "no matching lines" from an actual read or execution failure.

Here is a simpler implementation that handles both cases:

#!/usr/bin/env bash

set -o nounset
set -o pipefail

if (( $# != 1 )); then
    printf 'Usage: %s <log-file>\n' "$0" >&2
    exit 1
fi

LOG_FILE="$1"

if [[ ! -f "$LOG_FILE" || ! -r "$LOG_FILE" ]]; then
    printf 'Error: file is missing or unreadable: %s\n' "$LOG_FILE" >&2
    exit 1
fi

ERROR_COUNT=$(grep -ic 'error' "$LOG_FILE")
STATUS=$?

if (( STATUS > 1 )); then
    printf '%s\n' "Unable to search the log file." >&2
    exit "$STATUS"
fi

printf 'File: %s\n' "$LOG_FILE"
printf 'Lines containing "error": %s\n' "$ERROR_COUNT"


This example demonstrates why understanding command exit statuses matters.

18. Script Security and Best Practices
18.1 Quote Variable Expansions

Prefer:

cp -- "$SOURCE" "$DESTINATION"


over:

cp $SOURCE $DESTINATION


Quoting protects spaces and many special characters from unintended word splitting and filename expansion.

18.2 Validate User Input

Do not blindly trust arguments, environment variables, filenames, or data read from external commands.

Validate expected formats and permitted values before using them.

18.3 Avoid Unnecessary Root Privileges

Run scripts as a normal user whenever possible.

Use sudo only for operations that genuinely require elevated privileges.

18.4 Be Careful With Destructive Commands

Never execute destructive commands using unchecked variables.

Risky pattern:

rm -rf "$TARGET"


Before deleting anything, verify that the variable is set, contains the expected location, and cannot resolve to a critical directory.

18.5 Use Meaningful Names

Prefer:

BACKUP_DIRECTORY="/var/backups/myapp"


over:

X="/var/backups/myapp"


Meaningful names make scripts easier for other people to understand.

18.6 Keep Scripts Modular

Break complex work into functions:

check_dependencies() {
    :
}

collect_information() {
    :
}

write_report() {
    :
}

main() {
    check_dependencies
    collect_information
    write_report
}

main "$@"


The : command is a placeholder that does nothing successfully. Replace it with the actual implementation.

18.7 Test Before Automating

Before scheduling a script or running it on production systems:

Test it with harmless sample data.
Validate arguments and error paths.
Review file permissions.
Run bash -n.
Run ShellCheck.
Inspect its output and exit status.
Confirm that failure does not leave unwanted files or partial changes.
19. Recommended Development Workflow

A practical workflow for creating Bash scripts is:

Define the script's purpose.
Identify the required commands and inputs.
Write the smallest working version.
Add input validation.
Handle important failures.
Test normal and abnormal conditions.
Run syntax checks and ShellCheck.
Add documentation and useful messages.
Automate execution only after testing.
20. Practice Exercises
Create a script that checks disk usage and prints a warning when a chosen threshold is exceeded.
Write a script that counts .txt files in a directory.
Create an array of server names and print each one.
Read a log file and count occurrences of ERROR, WARNING, and INFO.
Write a script that creates a dated backup directory.
Create a temporary file and remove it automatically on script exit.
Build a colored terminal menu with options to show the date, hostname, and disk usage.
Schedule a harmless test script using cron.
Write a script that accepts a filename and prints its size and line count.
Use ShellCheck to find and correct at least three problems in a deliberately flawed script.
21. Quick Reference
Task	Command or construct
Check previous exit status	$?
Exit with a status	exit 1
Send errors to stderr	>&2
Enable unset-variable checks	set -o nounset
Check pipeline failures	set -o pipefail
Trace commands	bash -x script.sh
Check syntax	bash -n script.sh
Analyze scripts	shellcheck script.sh
Create an indexed array	ARRAY=(one two three)
Get array length	${#ARRAY[@]}
Loop through an array	"${ARRAY[@]}"
Find files	find
Search text	grep
Extract columns	cut, awk
Replace text	sed
Create a temporary file	mktemp
Register cleanup	trap cleanup EXIT
Schedule recurring tasks	crontab -e
Final Takeaways

After completing this chapter, you should understand how to:

Detect command failures and use exit statuses.
Redirect output and error messages.
Debug scripts with tracing and syntax checks.
Analyze code with ShellCheck.
Store and process collections using arrays.
Manipulate strings and process text files.
Work with files, directories, and command pipelines.
Add terminal colors and simple animations.
Automate recurring tasks using cron.
Write scripts that are safer, clearer, and easier to maintain.

Bash learning path completed: You now have a foundation for writing practical Linux automation scripts. Continue by building small tools, testing them carefully, and gradually combining these techniques into larger projects.
