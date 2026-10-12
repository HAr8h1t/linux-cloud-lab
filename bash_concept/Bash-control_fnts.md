Bash Scripting — Control Flow, Loops, Functions, and Arguments

Section: Bash Scripting
Part of: cloud-archi
Level: Beginner to Intermediate

1. Introduction

Control flow determines which commands a script executes, how many times they execute, and when execution stops.

Bash provides several constructs for controlling program flow:

Conditional statements: if, elif, else
Pattern matching: case
Loops: for, while, until
Arithmetic expressions
Functions
Script arguments and return statuses

These features allow us to create scripts that make decisions, repeat tasks, and reuse code.

2. Conditional Statements

Conditional statements execute commands depending on whether a condition is true or false.

2.1 The if Statement

Syntax:

if [[ condition ]]; then
    # Commands executed when condition is true
fi


Example:

#!/bin/bash

AGE=20

if [[ "$AGE" -ge 18 ]]; then
    echo "You are an adult."
fi


Output:

You are an adult.


Important points:

if starts the condition.
[[ ... ]] evaluates the condition.
then starts the commands to execute.
fi ends the conditional block.
2.2 The if-else Statement
#!/bin/bash

NUMBER=7

if (( NUMBER % 2 == 0 )); then
    echo "Even number"
else
    echo "Odd number"
fi


Output:

Odd number

2.3 The if-elif-else Statement

Use elif when multiple conditions must be checked.

#!/bin/bash

MARKS=82

if (( MARKS >= 90 )); then
    echo "Grade A"
elif (( MARKS >= 75 )); then
    echo "Grade B"
elif (( MARKS >= 60 )); then
    echo "Grade C"
else
    echo "Needs improvement"
fi


Output:

Grade B


Conditions are evaluated from top to bottom. Once a condition matches, the remaining branches are skipped.

3. Comparison Operators
3.1 Numeric Comparisons

Use these operators inside [[ ... ]] or another appropriate conditional expression.

Operator	Meaning
-eq	Equal to
-ne	Not equal to
-gt	Greater than
-ge	Greater than or equal to
-lt	Less than
-le	Less than or equal to

Example:

A=10
B=20

if [[ "$A" -lt "$B" ]]; then
    echo "A is smaller than B"
fi

3.2 String Comparisons
Operator	Meaning
==	Strings are equal
!=	Strings are different
-z "$VAR"	String is empty
-n "$VAR"	String is not empty

Example:

USERNAME="admin"

if [[ "$USERNAME" == "admin" ]]; then
    echo "Administrator account"
fi


Check whether a variable is empty:

if [[ -z "$USERNAME" ]]; then
    echo "Username is required"
fi


Important: Quote variable expansions in tests when appropriate, especially when using [ ... ] or passing values to commands.

3.3 File Test Operators

Bash can check whether files and directories exist and whether files have specific properties.

Operator	Meaning
-e FILE	Path exists
-f FILE	Regular file exists
-d DIR	Directory exists
-r FILE	File is readable
-w FILE	File is writable
-x FILE	File is executable
-s FILE	File exists and is not empty

Example:

FILE="/etc/hosts"

if [[ -f "$FILE" ]]; then
    echo "File exists"
else
    echo "File not found"
fi

3.4 Combining Conditions

Use && for AND and || for OR.

AGE=25
HAS_ID=true

if (( AGE >= 18 )) && [[ "$HAS_ID" == "true" ]]; then
    echo "Access allowed"
fi


Example using OR:

if [[ "$1" == "help" || "$1" == "--help" ]]; then
    echo "Display help information"
fi


For complex conditions, use parentheses where needed to make the intended logic clear.

4. The case Statement

The case statement is useful when a variable can have several possible values.

Syntax:

case "$VARIABLE" in
    pattern1)
        # Commands
        ;;
    pattern2)
        # Commands
        ;;
    *)
        # Default commands
        ;;
esac


Example:

#!/bin/bash

read -r -p "Enter an option (start/stop/restart): " ACTION

case "$ACTION" in
    start)
        echo "Starting service..."
        ;;
    stop)
        echo "Stopping service..."
        ;;
    restart)
        echo "Restarting service..."
        ;;
    *)
        echo "Invalid option"
        ;;
esac


Important syntax:

case begins the statement.
) ends a pattern.
;; ends a case branch.
* matches any value.
esac closes the statement.

Patterns can contain wildcards:

case "$1" in
    *.txt)
        echo "Text file"
        ;;
    *.log)
        echo "Log file"
        ;;
    *)
        echo "Unknown file type"
        ;;
esac

5. The for Loop

A for loop repeats commands for each item in a list.

5.1 Loop Through Values
for NAME in Alice Bob Charlie; do
    echo "Hello, $NAME"
done


Output:

Hello, Alice
Hello, Bob
Hello, Charlie

5.2 Loop Through Numbers
for NUMBER in {1..5}; do
    echo "$NUMBER"
done


This prints numbers from 1 to 5.

5.3 Use a C-Style Loop
for (( i = 1; i <= 5; i++ )); do
    echo "Count: $i"
done


The three parts are:

Initialization: i = 1
Condition: i <= 5
Update: i++
5.4 Loop Through Files
for FILE in ./*.txt; do
    [[ -f "$FILE" ]] || continue
    echo "Found: $FILE"
done


This processes regular files matching the pattern in the current directory. The guard also prevents an unmatched literal ./*.txt from being processed.

Best practice: Quote "$FILE" so spaces in filenames do not split the filename into separate words.

6. The while Loop

A while loop continues as long as its condition is true.

#!/bin/bash

COUNT=1

while (( COUNT <= 5 )); do
    echo "Count: $COUNT"
    ((COUNT++))
done


Output:

Count: 1
Count: 2
Count: 3
Count: 4
Count: 5


If the condition never becomes false, the loop may run forever.

Example: Read User Input Repeatedly
#!/bin/bash

while true; do
    read -r -p "Enter a command (quit to exit): " COMMAND

    if [[ "$COMMAND" == "quit" ]]; then
        break
    fi

    echo "You entered: $COMMAND"
done


break exits the loop.

continue skips the remainder of the current iteration and proceeds to the next one.

7. The until Loop

An until loop continues while its condition is false. It stops when the condition becomes true.

#!/bin/bash

COUNT=1

until (( COUNT > 5 )); do
    echo "$COUNT"
    ((COUNT++))
done


This prints numbers from 1 to 5.

Remember:

while: repeat while the condition is true.
until: repeat until the condition becomes true.
8. Arithmetic in Bash

Bash supports integer arithmetic using arithmetic expansion and arithmetic commands.

8.1 Arithmetic Expansion

Use $(( ... )) when you want the result as a value.

A=10
B=5

SUM=$(( A + B ))
DIFFERENCE=$(( A - B ))
PRODUCT=$(( A * B ))
QUOTIENT=$(( A / B ))
REMAINDER=$(( A % B ))

printf 'Sum: %d\n' "$SUM"
printf 'Difference: %d\n' "$DIFFERENCE"
printf 'Product: %d\n' "$PRODUCT"
printf 'Quotient: %d\n' "$QUOTIENT"
printf 'Remainder: %d\n' "$REMAINDER"


Bash arithmetic uses integers by default. For floating-point calculations, use a suitable tool such as awk or another language.

8.2 Increment and Decrement
COUNT=5

((COUNT++))
echo "$COUNT"

((COUNT--))
echo "$COUNT"


Output:

6
5


Be careful when using arithmetic commands as conditions or under set -e. An arithmetic command can return a nonzero status when its evaluated expression is zero. For predictable increments, assignment arithmetic such as COUNT=$((COUNT + 1)) is often easier to understand.

8.3 Shorthand Assignment
NUMBER=10

((NUMBER += 5))
((NUMBER -= 2))
((NUMBER *= 3))

echo "$NUMBER"


Output:

39

9. Functions

Functions allow you to group commands into reusable blocks.

9.1 Creating a Function
#!/bin/bash

greet() {
    echo "Welcome to Bash scripting!"
}

greet


Output:

Welcome to Bash scripting!


The function is defined first and called afterward.

9.2 Functions With Parameters
#!/bin/bash

greet() {
    local NAME="$1"
    echo "Hello, $NAME!"
}

greet "Alex"
greet "Sam"


Output:

Hello, Alex!
Hello, Sam!


Inside a function, positional parameters work similarly to script arguments:

$1: first argument
$2: second argument
$@: all arguments
$#: number of arguments

Use local for variables that should remain local to a function.

9.3 Returning Values

Bash functions do not use return to return arbitrary strings or objects. Their return status is an integer from 0 to 255.

Use standard output to return text.

get_greeting() {
    printf 'Hello from Bash'
}

MESSAGE=$(get_greeting)
echo "$MESSAGE"


Output:

Hello from Bash

9.4 Returning a Status
check_number() {
    if (( $1 > 0 )); then
        return 0
    else
        return 1
    fi
}

if check_number 5; then
    echo "Positive number"
else
    echo "Not a positive number"
fi


By convention:

Exit status 0 means success.
A nonzero exit status indicates failure or another condition.
9.5 Reusing Functions
print_line() {
    printf '%s\n' "------------------------"
}

show_title() {
    print_line
    printf '%s\n' "$1"
    print_line
}

show_title "SYSTEM INFORMATION"


Functions help keep scripts organized, readable, and easier to maintain.

10. Script Arguments

Arguments allow users to provide information when running a script.

Create greet.sh:

#!/bin/bash

echo "Script name: $0"
echo "First argument: $1"
echo "Second argument: $2"
echo "Argument count: $#"


Run it:

bash greet.sh Alex 25


Output:

Script name: greet.sh
First argument: Alex
Second argument: 25
Argument count: 2

Important Special Parameters
Parameter	Meaning
$0	Script name or invocation path
$1, $2	Positional arguments
$#	Number of positional arguments
"$@"	All positional arguments, preserved individually when quoted
"$*"	All positional arguments combined into one string when quoted
$?	Exit status of the previous command
$$	PID of the current shell process
$!	PID of the most recently started background job
10.1 Loop Through Arguments
#!/bin/bash

for ARGUMENT in "$@"; do
    printf 'Argument: %s\n' "$ARGUMENT"
done


Run:

bash args.sh "Hello World" Linux Bash


The first argument remains one item even though it contains a space.

10.2 Validate Required Arguments
#!/bin/bash

if (( $# < 1 )); then
    echo "Usage: $0 <username>" >&2
    exit 1
fi

USERNAME="$1"
printf 'Hello, %s!\n' "$USERNAME"


The script stops with an error if no username is supplied.

11. Reading User Input Safely

Use read -r to read input without interpreting backslashes as escapes.

read -r -p "Enter your name: " NAME
printf 'Hello, %s!\n' "$NAME"


Read a secret without displaying it:

read -r -s -p "Enter password: " PASSWORD
printf '\nPassword received.\n'


Avoid printing or logging passwords and other secrets.

12. Practical Mini-Project: Simple Calculator

Create calculator.sh:

#!/bin/bash

read -r -p "Enter first number: " A
read -r -p "Enter second number: " B
read -r -p "Choose operation (+, -, *, /): " OP

if [[ ! "$A" =~ ^-?[0-9]+$ || ! "$B" =~ ^-?[0-9]+$ ]]; then
    echo "Please enter valid integers." >&2
    exit 1
fi

case "$OP" in
    +)
        RESULT=$((A + B))
        ;;
    -)
        RESULT=$((A - B))
        ;;
    '*')
        RESULT=$((A * B))
        ;;
    /)
        if (( B == 0 )); then
            echo "Division by zero is not allowed." >&2
            exit 1
        fi
        RESULT=$((A / B))
        ;;
    *)
        echo "Invalid operation." >&2
        exit 1
        ;;
esac

printf 'Result: %d\n' "$RESULT"


This project demonstrates input, validation, conditionals, pattern matching, arithmetic, and error handling.

The calculator uses integer division, so decimal results are not preserved.

13. Common Mistakes
Missing Spaces Around Test Brackets

Incorrect:

if [ "$A"=="$B" ]; then
    echo "Equal"
fi


Correct:

if [[ "$A" == "$B" ]]; then
    echo "Equal"
fi

Forgetting to Increment a Loop Counter

Incorrect:

COUNT=1
while (( COUNT <= 5 )); do
    echo "$COUNT"
done


The counter never changes, so the loop does not terminate normally.

Correct:

COUNT=1
while (( COUNT <= 5 )); do
    echo "$COUNT"
    ((COUNT++))
done

Forgetting to Quote Arguments

Incorrect:

process_file $FILE


Correct:

process_file "$FILE"

Confusing Output With Return Status

Incorrect:

calculate() {
    return 100
}


This returns a status code, not the numeric result 100 as ordinary output.

Use printf and command substitution when you need to capture a value.

14. Practice Exercises
Write a script that checks whether a number is positive, negative, or zero.
Write a script that prints numbers from 1 to 20.
Create a multiplication table for a number provided by the user.
Write a menu using case with options to display the date, current directory, and logged-in username.
Create a function that accepts two numbers and prints their sum.
Write a script that accepts a filename as an argument and checks whether it exists.
Create a script that accepts multiple names and greets each one.
Extend the calculator to support exponentiation or additional operations.
15. Quick Reference
Requirement	Construct
Make a decision	if, elif, else
Match several options	case
Repeat over a list	for
Repeat while true	while
Repeat until true	until
Exit a loop	break
Skip an iteration	continue
Perform integer arithmetic	$((...))
Reuse commands	Functions
Access script inputs	$1, $2, "$@"
Count arguments	$#
Stop with a failure status	exit 1
Key Takeaways
Conditional statements let scripts make decisions.
Loops automate repetitive operations.
Arithmetic expressions perform integer calculations.
Functions organize reusable logic.
Script arguments make scripts flexible.
Input validation prevents many common errors.
Quoting variables and filenames helps avoid unexpected behavior.

Next chapter: Bash Error Handling, Debugging, Arrays, File Operations, and Automation.
