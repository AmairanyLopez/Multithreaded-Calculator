#Multithreaded Calculator
==============================

Calculator accepts expressions as infix notation text strings
consisting only of non-negative integers and three operators: grouping
"()", addition "+", and multiplication "*".  For example, given the
string "4*(6+3)" as input, the expected output is the string "36".

Multiple expressions are read from stdin, seperated by newlines, and evaluated concurrently.  
A period '.' as the first character of a line indicates end of input.  Then the program can stop once all of the expressions already entered have been processed.

A skeleton implementation of the calculator is provided in calc.c.  
The buffer[] array holds the inputted expressions, seperated by semicolons ';' (these are used internally only, and are never input by the user or output by the program!).

The first expression in the buffer is the one that is actively being reduced by the ungrouping, addition, and multiplication threads.  
When this expression has been reduced to a single number (such that the buffer looks something like "36;2+5;..."), 
a sentinel thread detects this condition and prints that number to stout, followed by a newline, 
and removes the expression from the buffer, so work can start on the next expression, e.g., "36;2+5;" becomes "2+5;".

The calculator implementation encompasses five threads, aside from the
main thread, all working concurrently:

 1. reader_thread.  This thread reads lines from stdin and appends them
to the buffer.  If there is not enough space remaining in the buffer[],
reader blocks until there is.

 2&3. adder_thread and multiplier_thread.  These look for "+" and "*"
signs, respectively, surrounded by two "naked" numbers, e.g., "4+3" or
"2*6", but NOT something "9+(2)" or "(5*(2))*8".  The corresponding
operation on the two numbers is performed, replacing the subexpression
with the result, e.g., "4+3" becomes "7".

 4. degrouper.  It looks for a single number surrounded by parentheses,
e.g. "(8)", and removes said parentheses.

 5. sentinel.  This thread checks the buffer to see if the currently
processed expression has been reduced to a single number; if it has, it
prints the number to stdout and removes it from the buffer as described
above.
