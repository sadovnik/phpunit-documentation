Extending PHPUnit
=================

PHPUnit can be extended in various ways to make the writing of tests
easier and customize the feedback you get from running tests. Here are
common starting points to extend PHPUnit.

Subclass PHPUnit\_Framework\_TestCase {#extending-phpunit.PHPUnit_Framework_TestCase}
=====================================

PHPUnit\_Framework\_TestCase Write custom assertions and utility methods
in an abstract subclass of `PHPUnit_Framework_TestCase` and derive your
test case classes from that class. This is one of the easiest ways to
extend PHPUnit.

Write custom assertions {#extending-phpunit.custom-assertions}
=======================

When writing custom assertions it is the best practice to follow how
PHPUnit's own assertions are implemented. As you can see in ?, the
`assertTrue()` method is just a wrapper around the `isTrue()` and
`assertThat()` methods: `isTrue()` creates a matcher object that is
passed on to `assertThat()` for evaluation.

    <?php
    abstract class PHPUnit_Framework_Assert
    {
        // ...

        /**
         * Asserts that a condition is true.
         *
         * @param  boolean $condition
         * @param  string  $message
         * @throws PHPUnit_Framework_AssertionFailedError
         */
        public static function assertTrue($condition, $message = '')
        {
            self::assertThat($condition, self::isTrue(), $message);
        }

        // ...

        /**
         * Returns a PHPUnit_Framework_Constraint_IsTrue matcher object.
         *
         * @return PHPUnit_Framework_Constraint_IsTrue
         * @since  Method available since Release 3.3.0
         */
        public static function isTrue()
        {
            return new PHPUnit_Framework_Constraint_IsTrue;
        }

        // ...
    }?>

? shows how `PHPUnit_Framework_Constraint_IsTrue` extends the abstract
base class for matcher objects (or constraints),
`PHPUnit_Framework_Constraint`.

    <?php
    class PHPUnit_Framework_Constraint_IsTrue extends PHPUnit_Framework_Constraint
    {
        /**
         * Evaluates the constraint for parameter $other. Returns TRUE if the
         * constraint is met, FALSE otherwise.
         *
         * @param mixed $other Value or object to evaluate.
         * @return bool
         */
        public function matches($other)
        {
            return $other === TRUE;
        }

        /**
         * Returns a string representation of the constraint.
         *
         * @return string
         */
        public function toString()
        {
            return 'is true';
        }
    }?>

The effort of implementing the `assertTrue()` and `isTrue()` methods as
well as the `PHPUnit_Framework_Constraint_IsTrue` class yields the
benefit that `assertThat()` automatically takes care of evaluating the
assertion and bookkeeping tasks such as counting it for statistics.
Furthermore, the `isTrue()` method can be used as a matcher when
configuring mock objects.

Implement PHPUnit\_Framework\_TestListener {#extending-phpunit.PHPUnit_Framework_TestListener}
==========================================

PHPUnit\_Framework\_TestListener ? shows a simple implementation of the
`PHPUnit_Framework_TestListener` interface.

    <?php
    class SimpleTestListener implements PHPUnit_Framework_TestListener
    {
        public function addError(PHPUnit_Framework_Test $test, Exception $e, $time)
        {
            printf("Error while running test '%s'.\n", $test->getName());
        }

        public function addFailure(PHPUnit_Framework_Test $test, PHPUnit_Framework_AssertionFailedError $e, $time)
        {
            printf("Test '%s' failed.\n", $test->getName());
        }

        public function addIncompleteTest(PHPUnit_Framework_Test $test, Exception $e, $time)
        {
            printf("Test '%s' is incomplete.\n", $test->getName());
        }

        public function addRiskyTest(PHPUnit_Framework_Test $test, Exception $e, $time)
        {
            printf("Test '%s' is deemed risky.\n", $test->getName());
        }

        public function addSkippedTest(PHPUnit_Framework_Test $test, Exception $e, $time)
        {
            printf("Test '%s' has been skipped.\n", $test->getName());
        }

        public function startTest(PHPUnit_Framework_Test $test)
        {
            printf("Test '%s' started.\n", $test->getName());
        }

        public function endTest(PHPUnit_Framework_Test $test, $time)
        {
            printf("Test '%s' ended.\n", $test->getName());
        }

        public function startTestSuite(PHPUnit_Framework_TestSuite $suite)
        {
            printf("TestSuite '%s' started.\n", $suite->getName());
        }

        public function endTestSuite(PHPUnit_Framework_TestSuite $suite)
        {
            printf("TestSuite '%s' ended.\n", $suite->getName());
        }
    }
    ?>

PHPUnit\_Framework\_BaseTestListener ? shows how to subclass the
`PHPUnit_Framework_BaseTestListener` abstract class, which lets you
specify only the interface methods that are interesting for your use
case, while providing empty implementations for all the others.

    <?php
    class ShortTestListener extends PHPUnit_Framework_BaseTestListener
    {
        public function endTest(PHPUnit_Framework_Test $test, $time)
        {
            printf("Test '%s' ended.\n", $test->getName());
        }
    }
    ?>

In ? you can see how to configure PHPUnit to attach your test listener
to the test execution.

Subclass PHPUnit\_Extensions\_TestDecorator {#extending-phpunit.PHPUnit_Extensions_TestDecorator}
===========================================

PHPUnit\_Extensions\_TestDecorator You can wrap test cases or test
suites in a subclass of `PHPUnit_Extensions_TestDecorator` and use the
Decorator design pattern to perform some actions before and after the
test runs.

PHPUnit\_Extensions\_RepeatedTest PHPUnit ships with one concrete test
decorator: `PHPUnit_Extensions_RepeatedTest`. It is used to run a test
repeatedly and only count it as a success if all iterations are
successful.

? shows a cut-down version of the `PHPUnit_Extensions_RepeatedTest` test
decorator that illustrates how to write your own test decorators.

    <?php
    require_once 'PHPUnit/Extensions/TestDecorator.php';

    class PHPUnit_Extensions_RepeatedTest extends PHPUnit_Extensions_TestDecorator
    {
        private $timesRepeat = 1;

        public function __construct(PHPUnit_Framework_Test $test, $timesRepeat = 1)
        {
            parent::__construct($test);

            if (is_integer($timesRepeat) &&
                $timesRepeat >= 0) {
                $this->timesRepeat = $timesRepeat;
            }
        }

        public function count()
        {
            return $this->timesRepeat * $this->test->count();
        }

        public function run(PHPUnit_Framework_TestResult $result = NULL)
        {
            if ($result === NULL) {
                $result = $this->createResult();
            }

            for ($i = 0; $i < $this->timesRepeat && !$result->shouldStop(); $i++) {
                $this->test->run($result);
            }

            return $result;
        }
    }
    ?>

Implement PHPUnit\_Framework\_Test {#extending-phpunit.PHPUnit_Framework_Test}
==================================

PHPUnit\_Framework\_Test Data-Driven Tests The `PHPUnit_Framework_Test`
interface is narrow and easy to implement. You can write an
implementation of `PHPUnit_Framework_Test` that is simpler than
`PHPUnit_Framework_TestCase` and that runs *data-driven tests*, for
instance.

? shows a data-driven test case class that compares values from a file
with Comma-Separated Values (CSV). Each line of such a file looks like
`foo;bar`, where the first value is the one we expect and the second
value is the actual one.

    <?php
    class DataDrivenTest implements PHPUnit_Framework_Test
    {
        private $lines;

        public function __construct($dataFile)
        {
            $this->lines = file($dataFile);
        }

        public function count()
        {
            return 1;
        }

        public function run(PHPUnit_Framework_TestResult $result = NULL)
        {
            if ($result === NULL) {
                $result = new PHPUnit_Framework_TestResult;
            }

            foreach ($this->lines as $line) {
                $result->startTest($this);
                PHP_Timer::start();
                $stopTime = NULL;

                list($expected, $actual) = explode(';', $line);

                try {
                    PHPUnit_Framework_Assert::assertEquals(
                      trim($expected), trim($actual)
                    );
                }

                catch (PHPUnit_Framework_AssertionFailedError $e) {
                    $stopTime = PHP_Timer::stop();
                    $result->addFailure($this, $e, $stopTime);
                }

                catch (Exception $e) {
                    $stopTime = PHP_Timer::stop();
                    $result->addError($this, $e, $stopTime);
                }

                if ($stopTime === NULL) {
                    $stopTime = PHP_Timer::stop();
                }

                $result->endTest($this, $stopTime);
            }

            return $result;
        }
    }

    $test = new DataDrivenTest('data_file.csv');
    $result = PHPUnit_TextUI_TestRunner::run($test);
    ?>

    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    .F

    Time: 0 seconds

    There was 1 failure:

    1) DataDrivenTest
    Failed asserting that two strings are equal.
    expected string <bar>
    difference      <  x>
    got string      <baz>
    /home/sb/DataDrivenTest.php:32
    /home/sb/DataDrivenTest.php:53

    FAILURES!
    Tests: 2, Failures: 1.
