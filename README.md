        Object Oriented Bash Library
=============================================
Download: https://github.com/ewindisch/bashoo


Installation
============

    sudo cp lib/oo /usr/local/bin/oo


Basic Usage
===========

    #!/bin/bash
    . $(which oo)

    # Classes are created implicitly through functions
    function ClsName::new {
      this=$1; shift
      args=$@
      return $this
    }
    function ClsName::hi {
      echo "hello world"
    }

    new objname is ClsName
    objname.hi  # this will echo "hello world"

Returning $this is only necessary to support
method chaining.


Instance Variables
==================

Instance variables are set by calling:

    $self.set varname value

Instance variables are accessed by calling:

    $self.varname


Method Chaining
===============

Return the method's $1 argument (self / this)
This returns a *string* representing the
name of the object variable.

Call methods in chain:

    $($(new fido is Dog).sit).rollover


Implementation
==============

Objects are defined by dynamically creating global
functions based on the name of the objects being
initalized. This new function curries the name
of the object as an argument to the class functions.

If you're familiar with Perl, this actually isn't so
different from blessing.

For example:

    new fido is Dog
    fido.sit
    fido.rollover

This unrolls to:

    Dog::new fido
    Dog::sit fido
    Dog::rollover fido


Instance variables
------------------

Instance variables are defined via instance_var,
which is curried to $objname.set

    instance_var objname varname value

Practically, from a ::new method, you could use:

    instance_var $self varname value

Normally, one would simply use:

    $self.set varname value


Inheritance (experimental)
==========================

This is pretty dirty and incomplete...

    function Animal::eat {
     self=$1; shift
     food_name=$1
     echo "Eats ${food_name}."
    }
    function Dog::new {
     self=$1; shift
     name=$1
     instance_var $self name $name
    }
    function Dog::sit {
     self=$1
     echo "${self.name} sits down."
    }
    function Dog::rollover {
     self=$1
     echo "${self.name} rolls over."
    }

    class Dog inherits from Animal
    new fido from Dog
    fido.eat grass
