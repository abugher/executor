# Description

This project is a framework for breaking shell scripts into multiple
files.  Currently the only shell in focus is bash.

The star of the show is the include function.  It will source a target
file by name exactly once.  If called again with the same target, it
will skip that target.  Thus each function (or file defining a function)
can include libraries defining other functions, without worrying much
about redundancy, or about accidental recursive loops.  (Even if a file
includes itself, no loop will occur.)

Functionally, `executor` becomes the executable file for any project
using this framework.  Symlinks expose that executable to the user with
different names, and the name of the symlink determines the flow of
execution.  All of the code for a project using this framework is
written in library files.


# Assumptions

* Your project consists of the following directories:
    * `bin`
    * `lib`
    * `doc`
* Each function (`foo`) is defined under `lib/` in a file with the same name as the function (`lib/foo`).
* Each function (`foo`) is documented under `doc/` in a file with the same name as the function (`doc/foo`).
* The name of each executable you wish to expose to the user is the name of a function.
* The name of each executable you wish to expose to the user is the name of a symlink under `bin/`, pointing to `executor`.
* `bin/executor` is a symlink to the real `executor`, in its own project repo.

# Implementation Details

The location of your `executor` symlink is how your project directory,
containing your function definitions and documentation files, is found
by the real `executor`.  The name of the symlink called for execution by
the user determines the flow of execution.


# Usage

## Framework

To use this framework in another project:

* Create a git repo for your project.
* Include this project using `git submodule`.
* Symlink from the `executor` executable in the executor repo to `bin/executor` in your project repo.
* Write your code as functions, defined one-per-file, under `lib/`.
* Document each function with a file of the same name under `doc/`.
* Declare dependencies among functions by writing `include` statements in each library definition file.
* Expose a function (`foo`) to the user by creating a symlink under `bin/` (`bin/foo`) pointing to `executor`.
* Repeat for each function you wish to expose.

## Include

Write as many of these as you need to declare all dependencies of each
library:

```
include <library>
```

`library` must be defined at `lib/library` and documented at
`doc/library`.  (Substitute the name of the library for the literal
string "library".)

For efficiency, the `include` lines should probably be outside the
function definition.  I think they look good at the top of the file.


# Example

To write a new script named $name:

Write a function named `$name`, in a library file at `lib/$name`.  Declare
dependencies on other libraries to be found under `lib/`.  Write documentation
at `doc/$name`.  Create a symlink to `executor` at `bin/$name`.

```
cat > lib/"${name}" <<< EOF
#!/bin/bash

include dependency1
include dependency2


function ${name}() {
  # do stuff
  return 0
}
EOF

cat > doc/"${name}" <<< EOF
NAME

  ${name} - A tool to do a thing.


SYNOPSIS

  ${name} <mandatory arg> [optional arg]
  ${name} -f <flag parameter> -<x|y|z> [optional arg] [...]


DESCRIPTION

  This is where you describe what ${name} is and does, and how arguments affect
  execution.  I like to emulate the format of a man page, but these files are
  plain text.  Line-wrapping is encouraged.  80 character line-width is
  recommended.
EOF

ln -s executor "bin/${name}"
```


# Bugs

A few functions are defined in executor:

  include
  output
  warn
  error
  fail

It would feel more organized to put them into libraries, but that
presents bootstrap problems.  To include a library defining include, it
would be necessary to duplicate the contents of the include function.
To provide failure messages during that operation, it would be necessary
to duplicate the contents of the fail function at least once, since the
fail library couldn't be included before include is defined.

A usage file is expected for every library name, which is assumed to
correspond to exactly one function name.  The usage function itself, for
example, has an empty usage file to suppress the warning, but does not
contain conditions to output its own usage.
