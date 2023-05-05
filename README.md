
# Coding Conventions

This repository acts as a defacto explanation of my coding conventions I use all of my personal c99 projects, including [ctools](https://github.com/remi-nolan/ctools/) and [nyx](https://github.com/remi-nolan/nyx).

This is not necessarily as robust as some corporate coding convention documents, as it functions mainly as a point of reference for contributing to these projects, as well as an explanation of why I follow the standards I do.

A quick side note: this document should not be taken as universal best practices, it is my personal style that I would rather my repositories follow.
Take everything said in it as my opinion, rather than objective truth.

Additionally, all declarations about functions of the c language are based off of c99, and do not take into account c11, c17, or any future version of the language.
This is because c99 is the version that I use, for better or worse.

Throughout this document, we will be referring to example code, the complete version of which is at the end of this document.

There will be a frequently asked questions section at the end.

### §1 Functions

###### §1.1
Functions are plain `snake_case` however they should always be prefixed by the type that they are operating on, where applicable.

###### §1.2
For example, in [ctalk](https://github.com/remi-nolan/ctools/blob/main/ctalk/ctalk.h), all of the functions that operate on a `cstream_t` are prefixed with `cstream_`.
Additionally, these functions all take a `cstream_t` (or a pointer to one) as the first argument; I commonly refer to this as the operative argument.

###### §1.3
For example:
```c
int quadratic_calculate(quadratic_t* quadratic, float a, float b, float c)
```

###### §1.4
This prevents naming conflicts except for duplicate operations on the same operative argument (i.e.
`cfile_read` and `cstream_read` are both functions that perform similar operations, but on different operative arguments), however this is something that should be avoided as best as possible, and if signficant similarities exist, try to break out the similarities into a separate function which the two operations can share.

###### §1.5
However, a function that that does not operate on a specific object, or operates on a primitive, should just describe the operation in question in as much detail as feasible.
An example from nyx would be:
```c
void zero_memory(void* handle, uint8_t num_bytes);
```

###### §1.6
The thinking behind this is that if two functions do the same thing, the overlap would necessitate further detail as to how the functions differ, and if no such distinction is possible, then at least one (if not both) of the functions is unnecessary, as is outlined in [§1.4](#§14).

### §2 Types
###### §2.1
Types, similarly to functions, use `snake_case`, however types should always have a postfix that identifies what kind of type it is.
For example, a struct is postfixed with a `_s`.

###### §2.2
This is primarily done for two reasons: the first being to distinguish types from functions & variables, the second being that (since we are in c99) you refer to structs differently than in c++, etc.
This lets the user know how the type is declared without looking back at the definition, from which they can parse some information about its use.
For example, if a type needs to be tagged `union`, `struct`, or can be used without worrying about a tag.

###### §2.3
The type tags are, generally, the first letter of the tag that the type is declared with.
However, in the event of overlap, the more common tag gets the first letter, and the lesser used tag is given the next most obvious letter.

###### §2.4
In practice, most types will be tagged with `_t` (for `typedef`) to alleviate the need for tags altogether.
That said this system is used because it is preferable than compiler errors being filled with "unnamed struct" or prefixed with `_` which tell the user nothing in the event that they have to predeclare the type at declaration-time.

###### §2.5
When defining a `struct` or a `union` the `typedef`'d alternative should be defined inline.
The shorthand name should be defined on the same line as the closing bracket on the type definition.

###### §2.6
Example (in accordance with [§4](#4-brackets--parentheses) and [§5](#5-indentation)):

```c
typdef struct quadratic_s
{
   float discriminant;
   float roots[2];
} quadratic_t;
```

###### §2.7
Never hide a pointer behind a type declaration (don't `typedef void* opaque_t`).
If you need an opaque type (i.e. nyx `display_t::internal`), make it a `void*`.

### §3 Variables
###### §3.1
Variables are declared in `snake_case` with no decoration.
This is done because variables are (typically) the section of code that are read and written most of any other.
Thus, they should require the least extraneous details.
However, that does not mean that they should be shortened to the point of unintelligibility.
a variable name should be in longform unless it is a common maths shorthand.

###### §3.2
Example:

| Do | Don't Do |
| -- | -------- |
| <pre>float epsilon;</pre> | <pre>float e;<br>float eps;</pre> |
| <pre>display_t display;</pre> | <pre>display_t d;</pre>

###### §3.3
In a position where given context one can discern a meaning, a shorthand is acceptable.
For example, in nyx's display code, where both `display_t` (user-level code) and `win32_display_t` (internal representation) variables coexist, `win32_display_t` is oft named `win32_disp` as opposed to `win32_display`.
This is only considered acceptable because of the coexistance of 'display' and 'disp', and the lack of any other name to conflate 'disp' with.

###### §3.4
In the event that there is a naming conflict, the smallest deciding factor should be postfixed to the name of the variable (i.e. epsilon32, epsilon64).

###### §3.5
The convention for operative arguments is to be given the same name as the type it refers to, without the declaration tag.

###### §3.6
Each variable always has it's own type specifier.
This means that if you have two types, both of which are defined as `float`s, they should be declared in separate statements (separated by a semicolon).

###### §3.7
Example:

| Do | Don't Do |
| -- | -------- |
| <pre>float x;<br/>float y;</pre> | <pre>float x, y;</pre> |

###### §3.8
This is mostly done for readability's sake, however this practice is also reinforced because pointer declarations belong on the type not the variable.
Despite this, c99 —and future versions of c as far as I know— requires a unique pointer declaration for each variable defined when declaring multiple variables with one type specifier (the "Don't Do" style).

###### §3.9
For example, `float* x, y;`, will declare `x` as a pointer to a `float`, and `y` as a float.
Therefore you should declare it as `float* x; float* y;`.
This reads better anyway.

###### §3.10
Any time you need to refer to an integer type, use the standard int version, where the standard library is unavailable, duplicates should be defined manually.
The same goes for stdbool's `bool`, `true`, and `false`.
In contexts where polluting the global namespace is undesirable this should be done using the preprocessor's `define` and `undef`.
 (this is an acceptable exception to §10)

### §4 Brackets & Parentheses
###### §4.1
Brackets are always on the next line after the parent statement.
This true of type definitions, function bodies, and control statements.
The exception to this rule is initializer lists that span multiple lines, in which case the opening bracket sticks to same line as the 

###### §4.2
Parentheses should never be preceded or followed by a space.
They should sit flush with the statement they belong to.
They should be followed by either a semicolon or a newline.

###### §4.3
Example:

| Do | Don't Do |
| -- | -------- |
| `if(<condition>)`<br>`{`<br>`   ...`<br>`}` | `if (<condition>) {`<br>`   ...`<br>`}` |

### §5 Whitespace
####### §5.1
Indentation should always be three discrete space characters, not tab characters.

####### §5.2
This is due to Vim & it's derivatives mishandling of tab characters.

####### §5.3
Blank lines are considered significant in terms of readability, and should be used to separate code into "logic groups" within blocks
Where a 'logic group' consists of code that together performs one part of a larger operation, i.e. calculating the discriminant of a quadratic function.
Logic groups can be prefixed with a one line comment explaining what part of the operation they do, as is visible in the complete example code at the end of this document.

### §6 Control Statements (if/else/for/return)
###### §6.1
Control statements always own (read: sit flush with, [§4](#4-brackets--parentheses)) the respective bracketed conditional statements.

###### §6.2
An `if` statement that has one textual line in it's nested block, should not have brackets.
Instead the nested statement should be dropped onto the line below it, indented once in accordance with [§5](#§5).
An important distinction herein is the use of 'textual line' that is to say that it evaluates to *one line of text*, meaning that a `for` loop with a nested `if` statement should use brackets, irregardless of whether or not the nested `if` statement contains brackets.

###### §6.3
`else` statements should be made on the line below the `if` statement's nested block/statement.
If necessary, an `if` statement should follow on the same line, as if they were the same statement.
Chains of `if`/`else if`/`else` statements should be consistent throughout.
Meaning that if one statement necessitates brackets, all statements in the chain should use them for the sake of consistency.

###### §6.4
Example:

| Do | Don't Do |
| -- | -------- |
| <pre>if(\<condition\>)<br>{<br>   ...<br>}</pre> | <pre>if (\<condition\>) {<br>   ...<br>}</pre> |
| <pre>if(\<condition\>)<br>   \<one line statement\></pre> | <pre>if(\<condition\>)<br>{<br>   \<one line statement\><br>}</pre> |
| <pre>if(\<condition\>)<br>{<br>   if(\<condition\>)<br>      \<one line statement\><br>}</pre> | <pre>if(\<condition\>)<br>   if(\<condition\>)<br>      \<one line statement\></pre> |
| <pre>if(\<condition\>)<br>{<br>   \<one line statement\><br>}<br>else if(\<condition\>)<br>{<br>   ...<br>}<br>else<br>{<br>   \<one line statement\><br>}</pre> | <pre>if(\<condition\>)<br>   \<one line statement\><br>else if(\<condition\>)<br>{<br>   ...<br>}<br>else<br>   \<one line statement\></pre> |

###### §6.5
`while` loops and `do`/`while` loops are usable where necessary, however you should bias towards `for` loops where applicable, for readability's sake.
`while` loops should follow the same conventions as an `if` statement (see [§6.3](#63)).
`do`/`while` loops should separate the `do` loop from it's 

###### §6.6
`for` loops should only ever be one line, and keep the statements used within them limited to one or two variable statement(s), one conditional statement, and one increment statement.
This is to avoid over-engineering and to maintain readability.

###### §6.7
`for` loops should generally follow one of three conventions: either they iterate over a range of integers, or they iterate over a range of arbitrary memory, for they iterate over a collection of values.
These three types of use different naming conventions in order to dinstinguish them inside the block itself.
When iterating over a range of integers, there should be one variable named `index` which contains a copy of the current position in the range.
When iterating over a block of memory, there should be one variable named `iter` or `iterator` which points to the current position in the block of memory.
When iterating over a collection of values, there should be one variable named either `iter`, `iterator`, or the name of type, minus any tags (as described in (§3.4)[#34]).
Otherwise a different loop should be used.

###### §6.8
Example (in accordance with [§7](#7-operators)):

| Do | Don't Do |
| -- | -------- |
| <pre>for(int index = 0; index < 10; ++index) | <pre>for(int i = 0; i < 10; ++i)</pre> |
| <pre>for(char* iter = cstring; iter != 0; ++iter) | <pre>for(char* ch = cstring; *ch; ++ch)</pre> |
| <pre>for(int index = 0, int* iter = array.handle; index < array.count; ++index, ++iter) | <pre>for(int i = 0, int* value = array.handle; i < array.count; ++i, ++value)</pre> |

###### §6.9
`return` statements only be followed by one of two things: a semicolon or a return value, which should be parenthesized and following by a semicolon.
Logic can be contained in a return statement.
Return statements are considered a "logic group" (see [§5.3](#53)) unto themself, however they rarely require a comment explaination.
If more work than a comparison check is required to calculate the return value (or the return value is calculated before the `return` statement), it should stored in a variable called `result`.

### §7 Operators
###### §7.1


COMPLETED EXAMPLE CODE:
```c
typdef struct quadratic_s
{
   float discriminant;
   float roots[2];
} quadratic_t;

//predeclare, for example's sake
float square_root(float x)
{
   //stubbed, this is just here for example's sake
}

int quadratic_calculate(quadratic_t* quadratic, float a, float b, float c)
{
   if(!quadratic || a == 0)
      return(-1);

   int result = 0;

   //calculate discriminant (one logic group)
   result.discriminant = b * b - (4.0f * a * c);

   //determine the number of outputs (one logic group)
   if(discriminant > 0)
   {
      descriminant = square_root(discriminant);

      result = 2;
      result.roots = (int[2]){
         (-b - discriminant) / (2 * a),
         (-b + descriminant) / (2 * a)
      };
   }
   else if(discriminant == 0)
   {
      descriminant = square_root(discriminant);

      result = 1;
      result.roots[0] = (-b - discriminant) / (2 * a);
   }

   return(result);
}
```

