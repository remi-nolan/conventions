# Coding Conventions

This repository acts as a defacto explanation of my coding conventions I use all of my personal c99 projects, including [ctools](https://github.com/remi-nolan/ctools/) and [nyx](https://github.com/remi-nolan/nyx).

This is not necessarily as robust as some corporate coding convention documents, as it functions mainly as a point of reference for contributing to these projects, as well as an explanation of why I follow the standards I do.

A quick side note: this document should not be taken as universal best practices, it is my personal style that I would rather my repositories follow. Take everything said in it as my opinion, rather than objective truth.

Additionally, all declarations about functions of the c language are based off of c99, and do not take into account c11, c17, or any future version of the language. This is because c99 is the version that I use, for better or worse.

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
bool cstream_read_file(cstream_t* stream, char* filename, int file_flags, int stream_flags);
```

###### §1.4
This prevents naming conflicts except for duplicate operations on the same operative argument (i.e. `cfile_read` and `cstream_read` are both functions that perform similar operations, but on different operative arguments), however this is something that should be avoided as best as possible, and if signficant similarities exist, try to break out the similarities into a separate function which the two operations can share.

###### §1.5
However, a function that that does not operate on a specific object, or operates on a primitive, should just describe the operation in question in as much detail as feasible. An example from nyx would be:
```c
void zero_memory(void* handle, uint8_t num_bytes);
```

###### §1.6
The thinking behind this is that if two functions do the same thing, the overlap would necessitate further detail as to how the functions differ, and if no such distinction is possible, then at least one (if not both) of the functions is unnecessary, as is outlined in [§1.4](#§14).

### §2 Types
###### §2.1
Types, similarly to functions, use `snake_case`, however types should always have a postfix that identifies what kind of type it is. For example, a struct is postfixed with a `_s`.

###### §2.2
This is primarily done for two reasons: the first being to distinguish types from functions & variables, the second being that (since we are in c99) you refer to structs differently than in c++, etc. This lets the user know how the type is declared without looking back at the definition, from which they can parse some information about its use. For example, if a type needs to be tagged `union`, `struct`, or can be used without worrying about a tag.

###### §2.3
The type tags are, generally, the first letter of the tag that the type is declared with. However, in the event of overlap, the more common tag gets the first letter, and the lesser used tag is given the next most obvious letter.

###### §2.4
In practice, most types will be tagged with `_t` (for `typedef`) to alleviate the need for tags altogether. That said this system is used because it is preferable than compiler errors being filled with "unnamed struct" or prefixed with `_` which tell the user nothing in the event that they have to predeclare the type at declaration-time.

###### §2.5
When defining a `struct` or a `union` the `typedef`'d alternative should be defined inline. Like `typedef struct <name>_s {...} <name_t>`.

###### §2.6
This, in practice (and in accordance with [§4](#§4)) would indicate that typedefinitions would look like the definition of nyx2D's `input_event` structure:

```c
typedef struct input_event_s
{
    uint8_t id;
    uint8_t state;
} input_event_t;
```

### §3 Variables
###### §3.1
Variables are declared in `snake_case` with no decoration. This is done because variables are (typically) the section of code that are read and written most of any other. Thus, they should require the least extraneous details. However, that does not mean that they should be shortened to the point of unintelligibility. Unless it is a common maths shorthand (i.e. x, y, z), a variable name should be in longform (i.e. epsilon, display).

###### §3.2
In the event that there is a naming conflict, the smallest deciding factor should be postfixed to the name of the variable (i.e. epsilon32, epsilon64).

###### §3.3
The convention for operative arguments is to be given the same name as the type it refers to, without the declaration tag.

###### §3.4
Each variable always has it's own type specifier. This means that if you have two types, both of which are defined as `float`s, they should be declared in separate statements (separated by a semicolon).

###### §3.5
Example:

| Do | Don't Do |
| -- | -------- |
| `float x;`<br/>`float y;` | `float x, y;` |

###### §3.6
This is mostly done for readability's sake, however this practice is also reinforced because pointer declarations belong on the type not the variable. Despite this, c99 —and future versions of c as far as I know— requires a unique pointer declaration for each variable defined when declaring multiple variables with one type specifier (the "Don't Do" style).

###### §3.7
For example, `float* x, y;`, will declare `x` as a pointer to a `float`, and `y` as a float. Therefore you should declare it as `float* x; float* y;`. This reads better anyway.

### §4 Braces
