# Extended Brainfuck
Extended Brainfuck (eBF for short) is, as the name suggests, an extended variant of Brainfuck, the infamous esolang.\
eBF was built for development inside of the [ePU](https://github.com/Nadelio/ePU).\
Kind of a crazy idea that it was built for use inside of a computer architecture.\
I (Nadelio) have actually been using it for just that, development inside of the ePU computer architecture.\
I started working on a simple [OS](https://github.com/Nadelio/ePU/tree/main/eBF%20External%20Programming/bin/OS), but (expectedly) haven't gotten very far.\
As a remedy, I've built a second language, [Hades](https://github.com/Nadelio/Hades-Programming-Language), to be a more approachable version of eBF, and in turn be a easier language to program the OS in.

## eBF Documentation
### Examples
Hello World in eBF:
```cpp
/* these are Dependency statements, use them to bring in other eBF programs */
DPND .\..\bin\STD\Lowercase_A_Char.ebf a /* declare "Lowercase_A_Char.ebf" as "a" */
DPND .\..\bin\STD\Uppercase_A_Char.ebf A /* declare "Uppercase_A_Char.ebf" as "A" */
DPND .\..\bin\STD\clear.ebf clear /* declare "clear.ebf" as "clear" */

/* "A" sets the value connected to the pointer to the character code for 'A' */
% A + + + + + + + , = /* H */
% a + + + + , = /* e */
+ + + + + + + , = = /* ll */
+ + + , = /* o */
% clear  /* this clears the value connected to the pointer and the current cell */
, = /* white space */
% A + + + + + + + + + + + + + + + + + + + + + + , = /* W */
% a + + + + + + + + + + + + + + , = /* o */
+ + + , = /* r */
% a + + + + + + + + + + + , = /* l */
% a + + + , = /* d */
% clear
+ , = /* ! */
/* this marks the end of the program */
END
```
Declare "Hello World!" as a string and print it
```cpp
DPND `.\..\writeHelloWorld`.ebf writeHelloWorld /* assume this does as advertised */
> # origin /* create label called origin on cell 1 */
> # size + + + + + + + + + + + + , /* set cell 2 to 12 and create label called size */
< < # sizeCopy , > > /* move to cell 0 and create a label called sizeCopy and write 12 to cell, then move back */
> # stringBegin # temp /* label the beginning of the string character data and make temporary label */
[ > # temp @ sizeCopy ' - , @ temp ] # stringEnd /* move to cell 15 and create a label called stringEnd */
!# temp /* delete "temp" label */
@ stringBegin /* move to label "stringBegin" (cell 3) */
% writeHelloWorld /* write "Hello World!" to the string character data section of the string */
@ size ' @ sizeCopy , /* copy size to sizeCopy */
@ stringBegin # temp /* jump to the beginning of the string character data and create label called "temp" */
[ @ temp ' = > # temp @ sizeCopy ' - , ] /* print all the characters in the string character data, decrement from sizeCopy until 0 */
!# temp /* delete "temp" label */
/* end program */
END
```
Adding two numbers using the `% add [ a, b ]` function
```cpp
DPND `.\..\STD\add`.ebf add /* declare add function dependency */
+ + + >> /* push the number 3 to the stack */
+ + >> /* push the number 2 to the stack */
% add , = /* add 2 and 3 together and write to terminal */
END
```
### Symbol Behaviors
- `+`: increment pointer value
- `-`: decrement pointer value
- `>`: increment pointer position
- `<`: decrement pointer position
- `,`: write to tape
- `.`: ask for character, store character in the current value of a cell being pointed at as `word`
- `=`: write to terminal the current value of the cell being pointed at as a character
- `[`: begin conditional loop, if the current value of the cell being pointed at  is `0` when `[` is met, skip, otherwise, loop until the current value of the cell being pointed at is `0`
- `]`: end conditional loop
- `>>`: push current pointer value to stack, set pointer value to `0`
- `<<`: pop top value from stack and store in pointer value
- `'`: read from the current value of the cell being pointed at
- `"`: read the position of the current cell into the pointer value
- `$`: system call for ePUx16, always needs 5 arguments following it &rarr; `$ <syscall id> <arg1> <arg2> <arg3> <arg4>`
- `DPND`: create a dependency using the next two tokens &rarr; ``DPND `<.ebf/.ebin file path>` <alias>``
- `%`: call a dependency using its alias &rarr; `% <alias>`
- `#`: create a label associated with the current position of the pointer &rarr; `# <alias>`
- `!#`: delete a label &rarr; `!# <alias>`
- `@`: jump to the position associated with the label &rarr; `@ <alias>`
- `END`: declare the end of a eBF program
- `/*`: begin a comment block, needs a matching `*/`, compiler automatically removes comments during compilation
- `*/`: end a comment block, needs a matching `/*`, compiler automatically removes comments during compilation
- `!E`: *Compiler only token*, hints to the compiler that the user wants to compile to the embedded format of eBin, place at the *very beginning* of a program

- *printing to terminal follows the Java Character Code conventions

### Unimplemented eBin operations
- *Interpreter still can interpret these operations, but there are no relevant symbols inside eBF, meant for use with Nullify's Sphere to eBin Compiler*
- `0000000000010100`: set pointer value to the following two bytes
- `0000000000010101`: move pointer position up (decrement Y address)
- `0000000000010110`: move pointer position down (increment Y address)
- `0000000000010111`: set the pointer position to the following two bytes
- `0000000000011000`: interrupts/conditionals
  - `0000000000000001`: Not equal
  - `0000000000000010`: Equal
  - `0000000000000011`: Greater than
  - `0000000000000100`: Less than
  - `0000000000000101`: Greater than or equal to
  - `0000000000000110`: Less than or equal to
- `0000000000011001`: NOP, waits 10ms
- `0000000000011010`: Syscall label syntax : `{`
- `0000000000011011`: Syscall label syntax : `}`
- `0000000000011100`: Write value to Tape

### Documentation Terminology
- Tape: Main memory unit, interacted with via the read and write instructions (`'`/`,`)
  - `[0][0][0][0]`
  - each piece of the Tape is called a "cell"
  - the size of the Tape is `((2^8)^2)-2` cells (`65534` cells)
  - the size of a cell is a `word`
- Stack: secondary memory unit, interacted with via the push and pop instructions (`>>`/`<<`)
  - `[ 0, 0 ]`
  - each member of the Stack is called a "paper"
  - the size of the Stack is `256`
  - the size of a paper is a `word`
- Pointer value: The value stored with the pointer, interacted with via the increment and decrement instructions (`+`/`-`)
  - `PV = 0`
- Pointer position: The position in the Tape that the pointer is pointing at, interacted with via the increment and decrement pointer instructions (`>`/`<`)
  - represented as `{ }` around a cell in the state diagram
- `word`: 16-bit unsigned integer
- Label: the name of a saved Tape position
  - `foo[0]: 0`

A *state diagram* is a diagram that shows a representation of the current state of an eBF program:
```
// Beginning state of all eBF programs
{0}[0][0][0]
[ 0, 0 ]
PV = 0
Labels: {  }
```
A *function notation* is a quick representation of the beginning state of a function:
```
 Top of stack  Bottom of stack
     \              /
% foo [ a, b, c... ]
   ^     \ | /
   |  Stack parameters in stack order
Function name
```
A *label notation* is a quick representation of the state of a label at any given point in the program:
```
Pointer Position
      |
@ foo[0]: 0
   ^      |
   | Pointer value
  Label name
```

# Getting Started
- Run the `eBFInterpreter.jar` file with the first argument being your file's name
  - `java -jar eBFInterpreter.jar filename.ebf`
  - Make sure that the `eBFInterpreter.jar` file is in the same folder as the one you are in, or write out its full path, same goes for the input file