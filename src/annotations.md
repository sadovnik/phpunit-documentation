Annotations {#appendixes.annotations}
===========

Annotation An annotation is a special form of syntactic metadata that
can be added to the source code of some programming languages. While PHP
has no dedicated language feature for annotating source code, the usage
of tags such as `@annotation arguments` in documentation block has been
established in the PHP community to annotate source code. In PHP
documentation blocks are reflective: they can be accessed through the
Reflection API's `getDocComment()` method on the function, class,
method, and attribute level. Applications such as PHPUnit use this
information at runtime to configure their behaviour.

> **Note**
>
> A doc comment in PHP must start with `/**` and end with `*/`.
> Annotations in any other style of comment will be ignored.

This appendix shows all the varieties of annotations supported by
PHPUnit.

@author {#appendixes.annotations.author}
=======

@author The `@author` annotation is an alias for the `@group` annotation
(see ?) and allows to filter tests based on their authors.

@after {#appendixes.annotations.after}
======

The `@after` annotation can be used to specify methods that should be
called after each test method in a test case class.

    class MyTest extends PHPUnit_Framework_TestCase
    {
        /**
         * @after
         */
        public function tearDownSomeFixtures()
        {
            // ...
        }

        /**
         * @after
         */
        public function tearDownSomeOtherFixtures()
        {
            // ...
        }
    }

@afterClass {#appendixes.annotations.afterClass}
===========

The `@afterClass` annotation can be used to specify static methods that
should be called after all test methods in a test class have been run to
clean up shared fixtures.

    class MyTest extends PHPUnit_Framework_TestCase
    {
        /**
         * @afterClass
         */
        public static function tearDownSomeSharedFixtures()
        {
            // ...
        }

        /**
         * @afterClass
         */
        public static function tearDownSomeOtherSharedFixtures()
        {
            // ...
        }
    }

@backupGlobals {#appendixes.annotations.backupGlobals}
==============

`@backupGlobals` The backup and restore operations for global variables
can be completely disabled for all tests of a test case class like this

    /**
     * @backupGlobals disabled
     */
    class MyTest extends PHPUnit_Framework_TestCase
    {
        // ...
    }

`@backupGlobals` The `@backupGlobals` annotation can also be used on the
test method level. This allows for a fine-grained configuration of the
backup and restore operations:

    /**
     * @backupGlobals disabled
     */
    class MyTest extends PHPUnit_Framework_TestCase
    {
        /**
         * @backupGlobals enabled
         */
        public function testThatInteractsWithGlobalVariables()
        {
            // ...
        }
    }

@backupStaticAttributes {#appendixes.annotations.backupStaticAttributes}
=======================

`@backupStaticAttributes` The `@backupStaticAttributes` annotation can
be used to back up all static property values in all declared classes
before each test and restore them afterwards. It may be used at the test
case class or test method level:

    /**
     * @backupStaticAttributes enabled
     */
    class MyTest extends PHPUnit_Framework_TestCase
    {
        /**
         * @backupStaticAttributes disabled
         */
        public function testThatInteractsWithStaticAttributes()
        {
            // ...
        }
    }

> **Note**
>
> `@backupStaticAttributes` is limited by PHP internals and may cause
> unintended static values to persist and leak into subsequent tests in
> some circumstances.
>
> See ? for details.

@before {#appendixes.annotations.before}
=======

The `@before` annotation can be used to specify methods that should be
called before each test method in a test case class.

    class MyTest extends PHPUnit_Framework_TestCase
    {
        /**
         * @before
         */
        public function setupSomeFixtures()
        {
            // ...
        }

        /**
         * @before
         */
        public function setupSomeOtherFixtures()
        {
            // ...
        }
    }

@beforeClass {#appendixes.annotations.beforeClass}
============

The `@beforeClass` annotation can be used to specify static methods that
should be called before any test methods in a test class are run to set
up shared fixtures.

    class MyTest extends PHPUnit_Framework_TestCase
    {
        /**
         * @beforeClass
         */
        public static function setUpSomeSharedFixtures()
        {
            // ...
        }

        /**
         * @beforeClass
         */
        public static function setUpSomeOtherSharedFixtures()
        {
            // ...
        }
    }

@codeCoverageIgnore\* {#appendixes.annotations.codeCoverageIgnore}
=====================

@codeCoverageIgnore @codeCoverageIgnoreStart @codeCoverageIgnoreEnd The
`@codeCoverageIgnore`, `@codeCoverageIgnoreStart` and
`@codeCoverageIgnoreEnd` annotations can be used to exclude lines of
code from the coverage analysis.

For usage see ?.

@covers {#appendixes.annotations.covers}
=======

Code Coverage @covers The `@covers` annotation can be used in the test
code to specify which method(s) a test method wants to test:

    /**
     * @covers BankAccount::getBalance
     */
    public function testBalanceIsInitiallyZero()
    {
        $this->assertEquals(0, $this->ba->getBalance());
    }

If provided, only the code coverage information for the specified
method(s) will be considered.

? shows the syntax of the `@covers` annotation.

  Annotation                          Description
  ----------------------------------- ---------------------------------------------------------------------------------------------------------------------------
  `@covers ClassName::methodName`     `Specifies that the annotated test method covers the specified method.`
  `@covers ClassName`                 `Specifies that the annotated test method covers all methods of a given class.`
  `@covers ClassName<extended>`       `Specifies that the annotated test method covers all methods of a given class and its parent class(es) and interface(s).`
  `@covers ClassName::<public>`       `Specifies that the annotated test method covers all public methods of a given class.`
  `@covers ClassName::<protected>`    `Specifies that the annotated test method covers all protected methods of a given class.`
  `@covers ClassName::<private>`      `Specifies that the annotated test method covers all private methods of a given class.`
  `@covers ClassName::<!public>`      `Specifies that the annotated test method covers all methods of a given class that are not public.`
  `@covers ClassName::<!protected>`   `Specifies that the annotated test method covers all methods of a given class that are not protected.`
  `@covers ClassName::<!private>`     `Specifies that the annotated test method covers all methods of a given class that are not private.`
  `@covers ::functionName`            `Specifies that the annotated test method covers the specified global function.`

  : Annotations for specifying which methods are covered by a test

@coversDefaultClass {#appendixes.annotations.coversDefaultClass}
===================

@coversDefaultClass The `@coversDefaultClass` annotation can be used to
specify a default namespace or class name. That way long names don't
need to be repeated for every `@covers` annotation. See ?.

    <?php
    /**
     * @coversDefaultClass \Foo\CoveredClass
     */
    class CoversDefaultClassTest extends PHPUnit_Framework_TestCase
    {
        /**
         * @covers ::publicMethod
         */
        public function testSomething()
        {
            $o = new Foo\CoveredClass;
            $o->publicMethod();
        }
    }
    ?>

@coversNothing {#appendixes.annotations.coversNothing}
==============

@coversNothing The `@coversNothing` annotation can be used in the test
code to specify that no code coverage information will be recorded for
the annotated test case.

This can be used for integration testing. See ? for an example.

The annotation can be used on the class and the method level and will
override any `@covers` tags.

@dataProvider {#appendixes.annotations.dataProvider}
=============

@dataProvider A test method can accept arbitrary arguments. These
arguments are to be provided by a data provider method (`provider()` in
?). The data provider method to be used is specified using the
`@dataProvider` annotation.

See ? for more details.

@depends {#appendixes.annotations.depends}
========

@depends PHPUnit supports the declaration of explicit dependencies
between test methods. Such dependencies do not define the order in which
the test methods are to be executed but they allow the returning of an
instance of the test fixture by a producer and passing it to the
dependent consumers. ? shows how to use the `@depends` annotation to
express dependencies between test methods.

See ? for more details.

@expectedException {#appendixes.annotations.expectedException}
==================

@expectedException ? shows how to use the `@expectedException`
annotation to test whether an exception is thrown inside the tested
code.

See ? for more details.

@expectedExceptionCode {#appendixes.annotations.expectedExceptionCode}
======================

@expectedExceptionCode The `@expectedExceptionCode` annotation, in
conjunction with the `@expectedException` allows making assertions on
the error code of a thrown exception thus being able to narrow down a
specific exception.

    class MyTest extends PHPUnit_Framework_TestCase
    {
        /**
         * @expectedException     MyException
         * @expectedExceptionCode 20
         */
        public function testExceptionHasErrorcode20()
        {
            throw new MyException('Some Message', 20);
        }
    }

To ease testing and reduce duplication a shortcut can be used to specify
a class constant as an `@expectedExceptionCode` using the
"`@expectedExceptionCode ClassName::CONST`" syntax.

    class MyTest extends PHPUnit_Framework_TestCase
    {
        /**
          * @expectedException     MyException
          * @expectedExceptionCode MyClass::ERRORCODE
          */
        public function testExceptionHasErrorcode20()
        {
          throw new MyException('Some Message', 20);
        }
    }
    class MyClass
    {
        const ERRORCODE = 20;
    }

@expectedExceptionMessage {#appendixes.annotations.expectedExceptionMessage}
=========================

@expectedExceptionMessage The `@expectedExceptionMessage` annotation
works similar to `@expectedExceptionCode` as it lets you make an
assertion on the error message of an exception.

    class MyTest extends PHPUnit_Framework_TestCase
    {
        /**
         * @expectedException        MyException
         * @expectedExceptionMessage Some Message
         */
        public function testExceptionHasRightMessage()
        {
            throw new MyException('Some Message', 20);
        }
    }

The expected message can be a substring of the exception Message. This
can be useful to only assert that a certain name or parameter that was
passed in shows up in the exception and not fixate the whole exception
message in the test.

    class MyTest extends PHPUnit_Framework_TestCase
    {
         /**
          * @expectedException        MyException
          * @expectedExceptionMessage broken
          */
         public function testExceptionHasRightMessage()
         {
             $param = "broken";
             throw new MyException('Invalid parameter "'.$param.'".', 20);
         }
    }

To ease testing and reduce duplication a shortcut can be used to specify
a class constant as an `@expectedExceptionMessage` using the
"`@expectedExceptionMessage ClassName::CONST`" syntax. A sample can be
found in ?.

@expectedExceptionMessageRegExp {#appendixes.annotations.expectedExceptionMessageRegExp}
===============================

@expectedExceptionMessageRegExp The expected message can also be
specified as a regular expression using the
`@expectedExceptionMessageRegExp` annotation. This is helpful for
situations where a substring is not adequate for matching a given
message.

    class MyTest extends PHPUnit_Framework_TestCase
    {
         /**
          * @expectedException              MyException
          * @expectedExceptionMessageRegExp /Argument \d+ can not be an? \w+/
          */
         public function testExceptionHasRightMessage()
         {
             throw new MyException('Argument 2 can not be an integer');
         }
    }

@group {#appendixes.annotations.group}
======

@group A test can be tagged as belonging to one or more groups using the
`@group` annotation like this

    class MyTest extends PHPUnit_Framework_TestCase
    {
        /**
         * @group specification
         */
        public function testSomething()
        {
        }

        /**
         * @group regresssion
         * @group bug2204
         */
        public function testSomethingElse()
        {
        }
    }

Tests can be selected for execution based on groups using the `--group`
and `--exclude-group` options of the command-line test runner or using
the respective directives of the XML configuration file.

@large {#appendixes.annotations.large}
======

`@large` The `@large` annotation is an alias for `@group large`.

`PHP_Invoker` `timeoutForLargeTests` If the `PHP_Invoker` package is
installed and strict mode is enabled, a large test will fail if it takes
longer than 60 seconds to execute. This timeout is configurable via the
`timeoutForLargeTests` attribute in the XML configuration file.

@medium {#appendixes.annotations.medium}
=======

`@medium` The `@medium` annotation is an alias for `@group medium`. A
medium test must not depend on a test marked as `@large`.

`PHP_Invoker` `timeoutForMediumTests` If the `PHP_Invoker` package is
installed and strict mode is enabled, a medium test will fail if it
takes longer than 10 seconds to execute. This timeout is configurable
via the `timeoutForMediumTests` attribute in the XML configuration file.

@preserveGlobalState {#appendixes.annotations.preserveGlobalState}
====================

@preserveGlobalState When a test is run in a separate process, PHPUnit
will attempt to preserve the global state from the parent process by
serializing all globals in the parent process and unserializing them in
the child process. This can cause problems if the parent process
contains globals that are not serializable. To fix this, you can prevent
PHPUnit from preserving global state with the `@preserveGlobalState`
annotation.

    class MyTest extends PHPUnit_Framework_TestCase
    {
        /**
         * @runInSeparateProcess
         * @preserveGlobalState disabled
         */
        public function testInSeparateProcess()
        {
            // ...
        }
    }

@requires {#appendixes.annotations.requires}
=========

`@requires` The `@requires` annotation can be used skip tests when
common preconditions, like the PHP Version or installed extensions, are
not met.

`@requires` A complete list of possibilities and examples can be found
at ?

@runTestsInSeparateProcesses {#appendixes.annotations.runTestsInSeparateProcesses}
============================

@runTestsInSeparateProcesses Indicates that all tests in a test class
should be run in a separate PHP process.

    /**
     * @runTestsInSeparateProcesses
     */
    class MyTest extends PHPUnit_Framework_TestCase
    {
        // ...
    }

**Note:** By default, PHPUnit will attempt to preserve the global state
from the parent process by serializing all globals in the parent process
and unserializing them in the child process. This can cause problems if
the parent process contains globals that are not serializable. See ? for
information on how to fix this.

@runInSeparateProcess {#appendixes.annotations.runInSeparateProcess}
=====================

@runInSeparateProcess Indicates that a test should be run in a separate
PHP process.

    class MyTest extends PHPUnit_Framework_TestCase
    {
        /**
         * @runInSeparateProcess
         */
        public function testInSeparateProcess()
        {
            // ...
        }
    }

**Note:** By default, PHPUnit will attempt to preserve the global state
from the parent process by serializing all globals in the parent process
and unserializing them in the child process. This can cause problems if
the parent process contains globals that are not serializable. See ? for
information on how to fix this.

@small {#appendixes.annotations.small}
======

`@small` The `@small` annotation is an alias for `@group small`. A small
test must not depend on a test marked as `@medium` or `@large`.

`PHP_Invoker` `timeoutForSmallTests` If the `PHP_Invoker` package is
installed and strict mode is enabled, a small test will fail if it takes
longer than 1 second to execute. This timeout is configurable via the
`timeoutForSmallTests` attribute in the XML configuration file.

> **Note**
>
> By default, all tests are considered to be small if they are not
> marked as `@medium` or `@large`. Please note, however, that `--group`
> and the related options will only consider a test to be in the `small`
> group if it is explicitly marked with the appropriate annotation.

@test {#appendixes.annotations.test}
=====

@test As an alternative to prefixing your test method names with `test`,
you can use the `@test` annotation in a method's DocBlock to mark it as
a test method.

    /**
     * @test
     */
    public function initialBalanceShouldBe0()
    {
        $this->assertEquals(0, $this->ba->getBalance());
    }

@testdox {#appendixes.annotations.testdox}
========

TestDox @testdox

@ticket {#appendixes.annotations.ticket}
=======

@ticket

@uses {#appendixes.annotations.uses}
=====

@uses The `@uses` annotation specifies code which will be executed by a
test, but is not intended to be covered by the test. A good example is a
value object which is necessary for testing a unit of code.

    /**
     * @covers BankAccount::deposit
     * @uses   Money
     */
    public function testMoneyCanBeDepositedInAccount()
    {
        // ...
    }

This annotation is especially useful in strict coverage mode where
unintentionally covered code will cause a test to fail. See ? for more
information regarding strict coverage mode.
