# Macros

Macros let you give a name to a sequence of assembly instructions and reuse it
throughout your code. You can also define parameters to provide different
values each time the macro is used.

## Defining and using a macro

Start a definition with either of these forms, followed by any parameter names
separated by commas:

```
name macro [params]
macro name [params]
```

Put the instructions to assemble inside the definition, then finish it with
`endm`. Macro names and parameter names are not case sensitive.

Use a macro by writing its name where an instruction is expected, followed by
any arguments separated by commas. A label can be placed on the line before the
macro name.

This example defines a small delay loop. The `count` parameter supplies the
loop count, and the local `.loop` label lets the macro be used more than once
without its labels conflicting:

```asm
wait_frames macro count
        ld b,count
.loop
        halt
        djnz .loop
        endm

        wait_frames 3
        wait_frames 5
        ret
```

Each use of `wait_frames` assembles its instructions using the supplied count.
Macros can also be defined without parameters when the same instruction
sequence is always needed.

## Parameters and arguments

* In the macro body, refer to each parameter by its name.
* Arguments are inserted into the body as text, so an argument can be a
  constant, a register, a label, or an expression such as `2+1`.
* An argument can contain commas if they are inside parentheses or quotes, for
  example `ld hl,(table+2)` or `print_str "Hello, World"`.
* Each parameter name can be up to 15 characters and an argument can be up to
  31 characters. A macro can have up to 8 parameters.

## Local and global labels

Labels that begin with `.` in the first column of the macro body are local to
each use of the macro. Each expansion gets its own copy of the label, so a macro
can contain loops and branches and be used many times without label clashes.

Labels that do not begin with `.` are not changed. They are shared with the rest
of your program, so a macro that defines a plain label such as `done` must not be
used more than once.

A `.` label defined inside a macro is scoped to the macro's name, not to the label
placed before the macro call. After the expansion, `.` labels that follow are
again relative to the label before the macro call.

## Nesting macros

Macros can use other macros, which makes it possible to build larger reusable
sequences from smaller ones. Up to 4 levels of macro nesting are supported.

## Limits

|Item|Limit|
|----|-----|
|Macro name|24 characters|
|Parameters per macro|8|
|Parameter name|15 characters|
|Argument value|31 characters|
|Local labels per macro|16, each up to 24 characters|
|Nesting depth|4 levels|

The macro definitions share roughly 12K of memory, which is enough for around 80
macros of 15 lines each.

## Rules and behavior

* A macro invocation must be the first statement on its line.
* A macro body cannot contain `INCLUDE`.
* Comments in the macro body are not kept in the assembled output.
* Macro definitions are read even inside a false `IF` block, so a macro defined
  there is still available. Definitions are discarded at the end of each
  assembly pass.

## Errors

If an error occurs in a line produced by a macro, the message names the macro and
the line within the macro body. Line numbers are counted from the line after the
`macro` line. For example:

```
Undefined symbol: macro print, line 4
```

The BASIC line reported is the one that used the macro.
