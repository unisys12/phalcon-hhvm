Phalcon-HHVM
============

Repo for playing with the idea of getting PhalconPHP extension to work with HHVM

##Idea
@Swader came up with the [idea](http://forum.phalconphp.com/discussion/2429/build-phalcon-for-hhvm-) to build a version of Phalcon that would work in an environment running [HHVM](https://github.com/facebook/hhvm). This is a brilliant idea and since the Phalcon Team is not going to have the time to perform the required [changes](https://github.com/facebook/hhvm/tree/master/hphp/runtime/ext_zend_compat) to the code-base, I figured I would take a look into it myself and at least get everything started.

##Why
Before learning of this idea, I looked at Phalcon and HHVM being two totally different approaches to solve the same problem. PHP can be slow and does not scale all that well in very large environments. Both projects address these problems, but in two different ways. Phalcon, of course, is a PHP framework written as a PHP extension. It runs on your server, just like *mcrypt* or *mongodb*. So, why would someone want to run Phalcon in an HHVM environment? Why not! Phalcon is already fast. Anyone that uses Phalcon knows that. But so is HHVM. HHVM has support for existing PHP extensions(rewritten of course), so why not take advantage of the amazing steps made by the HHVM team and put the fastest framework inside the fastest environment.

##To-Do-List
This list is a simplified version of the README over on HHVM's repo. We will strike off/added as needed.

- [x] Move all .c to .cpp (`for i in phalcon-hhvm/**/**/*.c; do mv $i "$i"pp; done`)
- [ ] Create a system library at runtime/ext_zend_compat/ext_.php This system library contains definitions for any functions and classes that have C implementations in your extension. All functions should have the __Native("ZendCompat") attribute. Internal classes should have the __NativeData("ZendCompat") attribute.
- [ ] Fix any C++ compile errors
- [x] Use Z_RESVAL instead of Z_LVAL for resource access
- [ ] Don't use PHP_MALIAS. Define the other function.
- [ ] Change any ZVAL_STRING(foo, "literal string", 0) to ZVAL_STRING(foo, "literal string", 2)
- [ ] Allocate hashtables with ALLOC_HASHTABLE() and zvals with ALLOC_ZVAL() or one of the macros that calls ALLOC_ZVAL(), don't allocate them directly. Don't use malloc(sizeof(zval)) or create them on the stack.
- [ ] After doing some [reading](http://en.wikipedia.org/wiki/Compatibility_of_C_and_C%2B%2B) and learning, we will need to traverse through the code-base and convert everything over to proper C++ code(proper use of structs, arrays, libraries that might need to be called, etc...). I am sure the above linked reference is just the tip of the ice burg. But one chunk at a time, right?

## File structure

The `php-src` directory should exactly mirror Zend's directory layout.
The `.h` files are exact copies from there with minor edits wrapped in
`#define HHVM`. The `.cpp` files are inspired by the `.c` file with the same
name but will devaiate wildly.

The `hhvm` directory contains various glue code that is needed to be written but
doesn't have a Zend function with the same name.

Extensions are stored under their PECL name and are unmodified except for the
changes required above.

Tests go in either `test/zend` if they are bundled extensions or
`test/slow/ext-` for all other PECL ones.

##Contributions
As for now, I would love to see contributions of any type. I am not a C Developer, nor did I stay at a Holiday Inn Express last night. But, I figure that if I am willing to take this on, others might want to join the party as well. Bear in mind, that this is going to be more for throwing around ideas and working with the code-base more than anything. I don't think it would be worth forking the HHVM repo until this is serious and passing tests. So... what do you say to doing something big and out of your comfort zone?
