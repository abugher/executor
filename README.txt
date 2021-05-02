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
  NAME

    ${name} - A tool to do a thing.

  SYNOPSIS

    ${name} <mandatory arg> [optional arg]
    ${name} -f <flag parameter> -<x|y|z> [optional arg] [...]

  DESCRIPTION

    This is where you describe what ${name} is and does, and how
    arguments affect execution.  I like to emulate the format of a man
    page, but these files are plain text.  Line-wrapping is encouraged;
    80 character line-width is recommended.
  EOF


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

Integration with other projects seems difficult.  It would be nice for
other projects to be able to include this as a subproject to make use of
the framework.
