To write a new script named $name:

Write a function named $name, in a library file at lib/$name.  Declare
dependencies on other libraries under lib/.  Create a symlink to
executor at bin/$name.  Write documentation at doc/$name.

  cat > lib/"${name}" <<< EOF
  #!/bin/bash

  include dependency1
  include dependency2


  function ${name}() {
    # do stuff
    return 0
  }
  EOF

  ln -s executor "bin/${name}"

  cat > doc/"${name}" <<< EOF
  ${name} <mandatory arg> [optional arg]
    
    Each synopsis should be similar to a man page synopsis and followed
    by an indented section (like this one) describing what that
    particular invocation will do and how arguments and flags affect
    execution.  Two space indentation is conventional.

    In this case, no flags are expected, one mandatory argument is
    expected, and at most one optional argument may be specified.

  ${name} -f <flag parameter> -<x|y|z> [optional arg] [...]
    
    This section describes an alternative invocation of ${name}.  In
    this case, a flag -f is expected and must be followed by a non-flag
    argument.  One flag out of -x, -y, or -z is also required.  Any
    number of arguments are accepted.


Bugs:

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
