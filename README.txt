To write a new script named $name:

Write a function named $name, in a library file under lib/ named $name.
Declare dependencies on other libraries under lib/.  Create a symlink to
executor named $name.

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


To do:

Per function usage messages might be nice.
