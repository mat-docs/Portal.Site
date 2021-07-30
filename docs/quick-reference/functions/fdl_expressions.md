# Function Definition Language Expressions

## Operator Order of Precedence

* `||` boolean operator _(left associative)_ 
* `&&` boolean operator _(left associative)_
* `!` boolean operator _(non-associative)_
* `&lt;`, `&lt;=`, `&gt;`, `&gt;=`, `==`, `!=` relational operators _(left associative)_
* `=` assignment operator _(right associative)_
* `+` and `-` operators _(left associative)_
* `*`, `/` and `%` operators _(left associative)_ 
* `^` operator _(right associative)_ 
* unary `-` operator _(non-associative)_
* `++` and `--` operators _(non-associative)_

## Mathematical Operators

`-` _expr_
:   The result is the negation of the expression.
 
_expr_ `+` _expr_
:   The result of the expression is the sum of the two expressions.
 
_expr_ `–` _expr_
:   The result of the expression is the difference between the two expressions.
 
_expr_ `*` _expr_
:   The result of the expression is the product of the two expressions.
 
_expr_ `/` _expr_
:   The result of the expression is the quotient of the two expressions.
 
_expr_ `%` _expr_
:   The result of the expression is the remainder of the division.
 
`^` _expr_
:   The result of the expression is the value of the first expression raised to the second expression.  
    The second expression must be an integer.
 
`(` _expr_ `)`
:   Alters the standard precedence to force the evaluation of the expression, e.g. a + b / c is not the same as (a + b) / c.
 
## Variable Operators

`var` refers to either a simple or an array variable.  
A simple variable is just a name; an array variable is specified as `name[expr]` (array subscript may be an expression).

`++`_var_
:   The variable is incremented by one and the new value is the result of the expression.
 
`--`_var_
:   The variable is decremented by one and the new value is the result of the expression.
 
_var_`++`
:   The result of the expression is the value of the variable and the variable is then incremented by one.
 
_var_`--`
:   The result of the expression is the value of the variable and the variable is then decremented by one.
 
_var_ `=` _expr_
:   The variable is assigned the value of the expression.
 
_var_ **_op_**`=` _expr_
:   This is equivalent to _var_ `=` _var_ **_op_** _expr_ with the exception that the _var_ part is evaluated only once.  
    This can make a difference if var is an array.  
    **_op_** is one of the mathematical operators `+`, `-`, `*`, `/`, `%` or `^`.

## Relational Operators

Relational expressions evaluate to either `0` (if the relation is false) or `1` (if the relation is true).  
These may appear in any legal expression.

_expr1_ `<` _expr2_
:   The result is `1` if _expr1_ is strictly less than _expr2_.
 
_expr1_ `<=` _expr2_
:   The result is `1` if _expr1_ is less than or equal to _expr2_.
 
_expr1_ `>` _expr2_
:   The result is `1` if _expr1_ is strictly greater than _expr2_.
 
_expr1_ `>=` _expr2_
:   The result is `1` if _expr1_ is greater than or equal to _expr2_.
 
_expr1_ `==` _expr2_
:   The result is `1` if _expr1_ is equal to _expr2_.
 
_expr1_ `!=` _expr2_
:   The result is `1` if _expr1_ is not equal to _expr2_.
 
## Boolean Operators

The result of all boolean operators is either `0` or `1` (for `false` and `true`), as in Relational expressions.

Any non-zero value is treated as `1`.

`!` _expr_
:   The result is `1` if _expr_ is `0`, otherwise `0`. (`NOT`)
 
_expr_ `&&` _expr_
:   The result is `1` if both expressions are non⌀zero, otherwise `0`. (`AND`)
 
_expr_ `||` _expr_
:   The result is `1` if either expression is non⌀zero otherwise `0`. (`OR`)
 
## Bitwise Operators

`~`_expr_
:   Bitwise `NOT`
 
_expr_ `&` _expr_
:   Bitwise `AND`
 
_expr_ `|` _expr_
:   Bitwise `OR`
 
_expr_ `>>` _expr_
:   Shift right
 
_expr_ `<<` _expr_
:   Shift left
