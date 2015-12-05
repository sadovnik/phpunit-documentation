Incomplete and Skipped Tests
============================

Incomplete Tests {#incomplete-and-skipped-tests.incomplete-tests}
================

When you are working on a new test case class, you might want to begin
by writing empty test methods such as:

    public function testSomething()
    {
    }

to keep track of the tests that you have to write. The problem with
empty test methods is that they are interpreted as a success by the
PHPUnit framework. This misinterpretation leads to the test reports
being useless -- you cannot see whether a test is actually successful or
just not yet implemented. Calling `$this->fail()` in the unimplemented
test method does not help either, since then the test will be
interpreted as a failure. This would be just as wrong as interpreting an
unimplemented test as a success.

Incomplete Test PHPUnit\_Framework\_IncompleteTest
PHPUnit\_Framework\_IncompleteTestError If we think of a successful test
as a green light and a test failure as a red light, we need an
additional yellow light to mark a test as being incomplete or not yet
implemented. `PHPUnit_Framework_IncompleteTest` is a marker interface
for marking an exception that is raised by a test method as the result
of the test being incomplete or currently not implemented.
`PHPUnit_Framework_IncompleteTestError` is the standard implementation
of this interface.

? shows a test case class, `SampleTest`, that contains one test method,
`testSomething()`. By calling the convenience method
`markTestIncomplete()` (which automatically raises an
`PHPUnit_Framework_IncompleteTestError` exception) in the test method,
we mark the test as being incomplete.

    <?php
    class SampleTest extends PHPUnit_Framework_TestCase
    {
        public function testSomething()
        {
            // Optional: Test anything here, if you want.
            $this->assertTrue(TRUE, 'This should already work.');

            // Stop here and mark this test as incomplete.
            $this->markTestIncomplete(
              'This test has not been implemented yet.'
            );
        }
    }
    ?>

An incomplete test is denoted by an `I` in the output of the PHPUnit
command-line test runner, as shown in the following example:

    phpunit --verbose SampleTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    I

    Time: 0 seconds, Memory: 3.95Mb

    There was 1 incomplete test:

    1) SampleTest::testSomething
    This test has not been implemented yet.

    /home/sb/SampleTest.php:12
    OK, but incomplete or skipped tests!
    Tests: 1, Assertions: 1, Incomplete: 1.

? shows the API for marking tests as incomplete.

  Method                                       Meaning
  -------------------------------------------- ----------------------------------------------------------------------------------
  `void markTestIncomplete()`                  Marks the current test as incomplete.
  `void markTestIncomplete(string $message)`   Marks the current test as incomplete using `$message` as an explanatory message.

  : API for Incomplete Tests

Skipping Tests {#incomplete-and-skipped-tests.skipping-tests}
==============

Not all tests can be run in every environment. Consider, for instance, a
database abstraction layer that has several drivers for the different
database systems it supports. The tests for the MySQL driver can of
course only be run if a MySQL server is available.

? shows a test case class, `DatabaseTest`, that contains one test
method, `testConnection()`. In the test case class' `setUp()` template
method we check whether the MySQLi extension is available and use the
`markTestSkipped()` method to skip the test if it is not.

    <?php
    class DatabaseTest extends PHPUnit_Framework_TestCase
    {
        protected function setUp()
        {
            if (!extension_loaded('mysqli')) {
                $this->markTestSkipped(
                  'The MySQLi extension is not available.'
                );
            }
        }

        public function testConnection()
        {
            // ...
        }
    }
    ?>

A test that has been skipped is denoted by an `S` in the output of the
PHPUnit command-line test runner, as shown in the following example:

    phpunit --verbose DatabaseTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    S

    Time: 0 seconds, Memory: 3.95Mb

    There was 1 skipped test:

    1) DatabaseTest::testConnection
    The MySQLi extension is not available.

    /home/sb/DatabaseTest.php:9
    OK, but incomplete or skipped tests!
    Tests: 1, Assertions: 0, Skipped: 1.

? shows the API for skipping tests.

  Method                                    Meaning
  ----------------------------------------- -------------------------------------------------------------------------------
  `void markTestSkipped()`                  Marks the current test as skipped.
  `void markTestSkipped(string $message)`   Marks the current test as skipped using `$message` as an explanatory message.

  : API for Skipping Tests

Skipping Tests using @requires {#incomplete-and-skipped-tests.skipping-tests-using-requires}
==============================

In addition to the above methods it is also possible to use the
`@requires` annotation to express common preconditions for a test case.

  Type          Possible Values                                                                                Examples                        Another example
  ------------- ---------------------------------------------------------------------------------------------- ------------------------------- ----------------------------------------------------
  `PHP`         Any PHP version identifier                                                                     @requires PHP 5.3.3             @requires PHP 5.4-dev
  `PHPUnit`     Any PHPUnit version identifier                                                                 @requires PHPUnit 3.6.3         @requires PHPUnit 4.6
  `OS`          A regexp matching [PHP\_OS](http://php.net/manual/en/reserved.constants.php#constant.php-os)   @requires OS Linux              @requires OS WIN32|WINNT
  `function`    Any valid parameter to [function\_exists](http://php.net/function_exists)                      @requires function imap\_open   @requires function ReflectionMethod::setAccessible
  `extension`   Any extension name                                                                             @requires extension mysqli      @requires extension curl

  : Possible @requires usages

    <?php
    /**
     * @requires extension mysqli
     */
    class DatabaseTest extends PHPUnit_Framework_TestCase
    {
        /**
         * @requires PHP 5.3
         */
        public function testConnection()
        {
            // Test requires the mysqli extension and PHP >= 5.3
        }

        // ... All other tests require the mysqli extension
    }
    ?>

If you are using syntax that doesn't compile with a certain PHP Version
look into the xml configuration for version dependent includes in ?
