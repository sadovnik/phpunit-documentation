Organizing Tests
================

Test Suite One of the goals of PHPUnit is that tests should be
composable: we want to be able to run any number or combination of tests
together, for instance all tests for the whole project, or the tests for
all classes of a component that is part of the project, or just the
tests for a single class.

PHPUnit supports different ways of organizing tests and composing them
into a test suite. This chapter shows the most commonly used approaches.

Composing a Test Suite Using the Filesystem {#organizing-tests.filesystem}
===========================================

Probably the easiest way to compose a test suite is to keep all test
case source files in a test directory. PHPUnit can automatically
discover and run the tests by recursively traversing the test directory.

Lets take a look at the test suite of the
[sebastianbergmann/money](http://github.com/sebastianbergmann/money/)
library. Looking at this project's directory structure, we see that the
test case classes in the `tests` directory mirror the package and class
structure of the System Under Test (SUT) in the `src` directory:

    src                                 tests
    `-- Currency.php                    `-- CurrencyTest.php
    `-- IntlFormatter.php               `-- IntlFormatterTest.php
    `-- Money.php                       `-- MoneyTest.php
    `-- autoload.php

To run all tests for the library we just need to point the PHPUnit
command-line test runner to the test directory:

    phpunit --bootstrap src/autoload.php tests
    PHPUnit 5.3.0 by Sebastian Bergmann.

    .................................

    Time: 636 ms, Memory: 3.50Mb

    OK (33 tests, 52 assertions)

> **Note**
>
> If you point the PHPUnit command-line test runner to a directory it
> will look for `*Test.php` files.

To run only the tests that are declared in the `CurrencyTest` test case
class in `tests/CurrencyTest.php` we can use the following command:

    phpunit --bootstrap src/autoload.php tests/CurrencyTest
    PHPUnit 5.3.0 by Sebastian Bergmann.

    ........

    Time: 280 ms, Memory: 2.75Mb

    OK (8 tests, 8 assertions)

For more fine-grained control of which tests to run we can use the
`--filter` option:

    phpunit --bootstrap src/autoload.php --filter testObjectCanBeConstructedForValidConstructorArgument tests
    PHPUnit 5.3.0 by Sebastian Bergmann.

    ..

    Time: 167 ms, Memory: 3.00Mb

    OK (2 test, 2 assertions)

> **Note**
>
> A drawback of this approach is that we have no control over the order
> in which the tests are run. This can lead to problems with regard to
> test dependencies, see ?. In the next section you will see how you can
> make the test execution order explicit by using the XML configuration
> file.

Composing a Test Suite Using XML Configuration {#organizing-tests.xml-configuration}
==============================================

XML Configuration
PHPUnit's XML configuration file (?) can also be used to compose a test
suite. ? shows a minimal `phpunit.xml` file that will add all `*Test`
classes that are found in `*Test.php` files when the `tests` directory
is recursively traversed.

    <phpunit bootstrap="src/autoload.php">
      <testsuites>
        <testsuite name="money">
          <directory>tests</directory>
        </testsuite>
      </testsuites>
    </phpunit>

If `phpunit.xml` or `phpunit.xml.dist` (in that order) exist in the
current working directory and `--configuration` is *not* used, the
configuration will be automatically read from that file.

The order in which tests are executed can be made explicit:

    <phpunit bootstrap="src/autoload.php">
      <testsuites>
        <testsuite name="money">
          <file>tests/IntlFormatterTest.php</file>
          <file>tests/MoneyTest.php</file>
          <file>tests/CurrencyTest.php</file>
        </testsuite>
      </testsuites>
    </phpunit>
