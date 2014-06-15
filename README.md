Phalcon-HHVM
============

Repo for playing with the idea of getting PhalconPHP extension to work with HHVM

##Idea
It was [@Swader](https://github.com/Swader)(Bruno Škvorc) who came up with this [idea](http://forum.phalconphp.com/discussion/2429/build-phalcon-for-hhvm-) to build a version of Phalcon that would work in an environment running [HHVM](https://github.com/facebook/hhvm). This is a brilliant idea and since the Phalcon Team is not going to have the time to perform the required [changes](https://github.com/facebook/hhvm/tree/master/hphp/runtime/ext_zend_compat) to the code-base, I figured I would take a look into it myself and see what I could do.

##Why
Before learning of this idea, I looked at Phalcon and HHVM as being two totally different approaches to solve the same problem. And they are. PHP can be slow and does not scale all that well in very large environments. Both projects address these problems, but in two total different ways. And because of these differences, not once did the thought occur to me to join them together.

Phalcon is a PHP framework written as a PHP extension. In is current version, 1.3.2, it is completely written in C. It runs on your server, just like *mcrypt* or *mongodb*. And since it is a PHP extension, it is super fast and scales very very well with your needs and environment. The framework itself is fairly mature, for the most part. It is still in version 1 of it's life cycle, so there is plenty of room to grow. Biggest hurdle for the framework, thus far, is not enough contributors. Face it, there are not a whole lot of C developers out there interested in writing something for PHP. Actually, I think they all work at Facebook, if I had to guess. Hence, Zephir was born about a year or so ago. To learn more about Zephir, check-out their [github repo](https://github.com/phalcon/zephir) and the [official site](http://zephir-lang.com/). There is talk of adding a flag to Zephir that will automagically build HHVM compatible code for you. Work on that will not begin until Phalcon 2.0 is released from Alpha and Beta builds. Hence, this project. I want to see if I can make it work now! This is purely experimental.

HHVM is, essentially, a replacement for PHP5 that works with a JIT compiler. The team has seen tremendous performance improvements over at Facebook by using HHVM in their environment. The cool thing about HHVM, unlike Phalcon, is that you can run and use just about any pre-existing PHP code or frameworks on top of it without many or any modifications. Plus get the same performance increases instantly, for free! Sure, after installing Phalcon on your server, you can also create a Laravel app. But it will not take any benefits of Phalcon being on the server.

So, why would someone want to run Phalcon in an HHVM environment? Why not! Phalcon is already fast. Anyone that uses Phalcon knows that. But so is HHVM. HHVM has support for existing PHP extensions(rewritten of course), so why not take advantage of the amazing steps made by the HHVM team and put the fastest framework inside the fastest environment? Makes perfect sense to me!

##To-Do-List
This list is a simplified version of the README over on HHVM's repo. We will strike off/add as needed.

- [x] Move all .c to .cpp (`for i in phalcon-hhvm/**/**/*.c; do mv $i "$i"pp; done`)
- [ ] Create a system library at phalcon-hhvm/**/ext_*.php This system library contains definitions for any functions and classes that have C implementations in your extension. All functions should have the __Native("ZendCompat") attribute. Internal classes should have the __NativeData("ZendCompat") attribute.
- [x] Use Z_RESVAL instead of Z_LVAL for resource access
- [ ] Don't use PHP_MALIAS. Define the other function.
- [ ] Change any ZVAL_STRING(foo, "literal string", 0) to ZVAL_STRING(foo, "literal string", 2)
- [x] Allocate hashtables with ALLOC_HASHTABLE() and zvals with ALLOC_ZVAL() or one of the macros that calls ALLOC_ZVAL(), don't allocate them directly. Don't use malloc(sizeof(zval)) or create them on the stack. *(there are still 4 instances malloc(sizeof()) in place)*
- [ ] After doing some [reading](http://en.wikipedia.org/wiki/Compatibility_of_C_and_C%2B%2B) and learning, we will need to traverse through the code-base and convert everything over to proper C++ code(proper use of structs, arrays, libraries that might need to be called, etc...). I am sure the above linked reference is just the tip of the ice burg. But one cube of ice at a time, right?
- [ ] Fix any C++ compile errors

## File structure

The `php-src` directory *([repo](https://github.com/facebook/hhvm/tree/master/hphp/runtime/ext_zend_compat/php-src))* should exactly mirror Zend's directory layout.
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
As for now, I would love to see contributions of any type. I am not a C Developer, nor did I stay at a Holiday Inn Express last night. But, I figure that if I am willing to take this on, others might want to join the party as well. Bear in mind, that this is going to be more for throwing around ideas and working with the code-base more than anything. If we get something compiled and working, Awesome! If we don't, well, we gave it a shot and a chalk it up as a learning experience. So... what do you say to doing something big and out of your comfort zone?
