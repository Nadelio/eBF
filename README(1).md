# Standard Library Documentation

- `% clear [  ]`
  - clears the pointer value and the current tape slot
- `% popRAMValue [  ]`
  - moves the tape value from the tape slot being pointed at to the pointer value and sets the tape slot to `0`
- `% multiply [ c, b, a ]`
  - multiplies `a` * `b` and *adds* to `c`
  - working space is 4 cells long starting from the current pointer location
  - end state is `[ c+(a*b), 0, 0, 0 ]`
- `% add [ a, b ]`
  - adds the top two stack values together and stores them in the current tape slot
  - working space is 2 cells long starting from the current pointer location
  - end state is `[ a+b, 0 ]`
- `% sub [ a, b ]`
  - subtracts the top two stack values together and stores them in the current tape slot
  - end state is `[ a-b, 0 ]`
- `% zero [  ]`
  - sets the pointer value and the current tape slot to `16` (the character code for `0`)
- `% A [  ]`
  - sets the pointer value and the current tape slot to `33` (the character code for `A`)
- `% a [  ]`
  - sets the pointer value and current tape slot to `65` (the character code for `a`)

<!-- function notation: % foo [ parameters in stack order at beginning of function ] --!>
<!-- if a function changes the end state of more than one cell, add an end state note --!>