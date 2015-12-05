Test Doubles
============

Gerard Meszaros introduces the concept of Test Doubles in ? like this:

> System Under Test Sometimes it is just plain hard to test the system
> under test (SUT) because it depends on other components that cannot be
> used in the test environment. This could be because they aren't
> available, they will not return the results needed for the test or
> because executing them would have undesirable side effects. In other
> cases, our test strategy requires us to have more control or
> visibility of the internal behavior of the SUT.
>
> Depended-On Component Test Double When we are writing a test in which
> we cannot (or chose not to) use a real depended-on component (DOC), we
> can replace it with a Test Double. The Test Double doesn't have to
> behave exactly like the real DOC; it merely has to provide the same
> API as the real one so that the SUT thinks it is the real one!
>
> — Gerard Meszaros

The `getMockBuilder($type)` method provided by PHPUnit can be used in a
test to automatically generate an object that can act as a test double
for the specified original type (interface or class name). This test
double object can be used in every context where an object of the
original type is expected or required.

By default, all methods of the original class are replaced with a dummy
implementation that just returns `null` (without calling the original
method). Using the `will($this->returnValue())` method, for instance,
you can configure these dummy implementations to return a value when
called.

> **Note**
>
> Please note that `final`, `private` and `static` methods cannot be
> stubbed or mocked. They are ignored by PHPUnit's test double
> functionality and retain their original behavior.

> **Warning**
>
> Please pay attention to the fact that the parameters managing has been
> changed. The previous implementation clones all object parameters. It
> did not allow to check whether the same object was passed to method or
> not. ? shows where the new implementation could be useful. ? shows how
> to switch back to previous behavior.

Stubs {#test-doubles.stubs}
=====

Stub The practice of replacing an object with a test double that
(optionally) returns configured return values is referred to as
*stubbing*. You can use a *stub* to "replace a real component on which
the SUT depends so that the test has a control point for the indirect
inputs of the SUT. This allows the test to force the SUT down paths it
might not otherwise execute".

Fluent Interface ? shows how to stub method calls and set up return
values. We first use the `getMockBuilder()` method that is provided by
the `PHPUnit_Framework_TestCase` class to set up a stub object that
looks like an object of `SomeClass` (?). We then use the [Fluent
Interface](http://martinfowler.com/bliki/FluentInterface.html) that
PHPUnit provides to specify the behavior for the stub. In essence, this
means that you do not need to create several temporary objects and wire
them together afterwards. Instead, you chain method calls as shown in
the example. This leads to more readable and "fluent" code.

    <?php
    class SomeClass
    {
        public function doSomething()
        {
            // Do something.
        }
    }
    ?>

getMockBuilder()
method()
willReturn()
    <?php
    require_once 'SomeClass.php';

    class StubTest extends PHPUnit_Framework_TestCase
    {
        public function testStub()
        {
            // Create a stub for the SomeClass class.
            $stub = $this->getMockBuilder('SomeClass')
                         ->getMock();

            // Configure the stub.
            $stub->method('doSomething')
                 ->willReturn('foo');

            // Calling $stub->doSomething() will now return
            // 'foo'.
            $this->assertEquals('foo', $stub->doSomething());
        }
    }
    ?>

> **Note**
>
> The example shown above only works when the original class does not
> declare a method named "method".
>
> If the original class does declare a method named "method" then
> `$stub->expects($this->any())->method('doSomething')->willReturn('foo');`
> has to be used.

"Behind the scenes", PHPUnit automatically generates a new PHP class
that implements the desired behavior when the `getMock()` method is
used.

? shows an example of how to use the Mock Builder's fluent interface to
configure the creation of the test double.

getMockBuilder()
method()
willReturn()
    <?php
    require_once 'SomeClass.php';

    class StubTest extends PHPUnit_Framework_TestCase
    {
        public function testStub()
        {
            // Create a stub for the SomeClass class.
            $stub = $this->getMockBuilder('SomeClass')
                         ->disableOriginalConstructor()
                         ->getMock();

            // Configure the stub.
            $stub->method('doSomething')
                 ->willReturn('foo');

            // Calling $stub->doSomething() will now return
            // 'foo'.
            $this->assertEquals('foo', $stub->doSomething());
        }
    }
    ?>

Here is a list of methods provided by the Mock Builder:

-   `setMethods(array $methods)` can be called on the Mock Builder
    object to specify the methods that are to be replaced with a
    configurable test double. The behavior of the other methods is not
    changed. If you call `setMethods(null)`, then no methods will be
    replaced.

-   `setConstructorArgs(array $args)` can be called to provide a
    parameter array that is passed to the original class' constructor
    (which is not replaced with a dummy implementation by default).

-   `setMockClassName($name)` can be used to specify a class name for
    the generated test double class.

-   `disableOriginalConstructor()` can be used to disable the call to
    the original class' constructor.

-   `disableOriginalClone()` can be used to disable the call to the
    original class' clone constructor.

-   `disableAutoload()` can be used to disable `__autoload()` during the
    generation of the test double class.

In the examples so far we have been returning simple values using
`willReturn($value)`. This short syntax is the same as
`will($this->returnValue($value))`. We can use variations on this longer
syntax to achieve more complex stubbing behaviour.

Sometimes you want to return one of the arguments of a method call
(unchanged) as the result of a stubbed method call. ? shows how you can
achieve this using `returnArgument()` instead of `returnValue()`.

getMock()
method()
will()
returnArgument()
    <?php
    require_once 'SomeClass.php';

    class StubTest extends PHPUnit_Framework_TestCase
    {
        public function testReturnArgumentStub()
        {
            // Create a stub for the SomeClass class.
            $stub = $this->getMockBuilder('SomeClass')
                         ->getMock();

            // Configure the stub.
            $stub->method('doSomething')
                 ->will($this->returnArgument(0));

            // $stub->doSomething('foo') returns 'foo'
            $this->assertEquals('foo', $stub->doSomething('foo'));

            // $stub->doSomething('bar') returns 'bar'
            $this->assertEquals('bar', $stub->doSomething('bar'));
        }
    }
    ?>

When testing a fluent interface, it is sometimes useful to have a
stubbed method return a reference to the stubbed object. ? shows how you
can use `returnSelf()` to achieve this.

getMock()
method()
will()
returnSelf()
    <?php
    require_once 'SomeClass.php';

    class StubTest extends PHPUnit_Framework_TestCase
    {
        public function testReturnSelf()
        {
            // Create a stub for the SomeClass class.
            $stub = $this->getMockBuilder('SomeClass')
                         ->getMock();

            // Configure the stub.
            $stub->method('doSomething')
                 ->will($this->returnSelf());

            // $stub->doSomething() returns $stub
            $this->assertSame($stub, $stub->doSomething());
        }
    }
    ?>

Sometimes a stubbed method should return different values depending on a
predefined list of arguments. You can use `returnValueMap()` to create a
map that associates arguments with corresponding return values. See ?
for an example.

getMock()
method()
will()
returnValueMap()
    <?php
    require_once 'SomeClass.php';

    class StubTest extends PHPUnit_Framework_TestCase
    {
        public function testReturnValueMapStub()
        {
            // Create a stub for the SomeClass class.
            $stub = $this->getMockBuilder('SomeClass')
                         ->getMock();

            // Create a map of arguments to return values.
            $map = array(
              array('a', 'b', 'c', 'd'),
              array('e', 'f', 'g', 'h')
            );

            // Configure the stub.
            $stub->method('doSomething')
                 ->will($this->returnValueMap($map));

            // $stub->doSomething() returns different values depending on
            // the provided arguments.
            $this->assertEquals('d', $stub->doSomething('a', 'b', 'c'));
            $this->assertEquals('h', $stub->doSomething('e', 'f', 'g'));
        }
    }
    ?>

When the stubbed method call should return a calculated value instead of
a fixed one (see `returnValue()`) or an (unchanged) argument (see
`returnArgument()`), you can use `returnCallback()` to have the stubbed
method return the result of a callback function or method. See ? for an
example.

getMock()
method()
will()
returnCallback()
    <?php
    require_once 'SomeClass.php';

    class StubTest extends PHPUnit_Framework_TestCase
    {
        public function testReturnCallbackStub()
        {
            // Create a stub for the SomeClass class.
            $stub = $this->getMockBuilder('SomeClass')
                         ->getMock();

            // Configure the stub.
            $stub->method('doSomething')
                 ->will($this->returnCallback('str_rot13'));

            // $stub->doSomething($argument) returns str_rot13($argument)
            $this->assertEquals('fbzrguvat', $stub->doSomething('something'));
        }
    }
    ?>

A simpler alternative to setting up a callback method may be to specify
a list of desired return values. You can do this with the
`onConsecutiveCalls()` method. See ? for an example.

getMock()
method()
will()
onConsecutiveCalls()
    <?php
    require_once 'SomeClass.php';

    class StubTest extends PHPUnit_Framework_TestCase
    {
        public function testOnConsecutiveCallsStub()
        {
            // Create a stub for the SomeClass class.
            $stub = $this->getMockBuilder('SomeClass')
                         ->getMock();

            // Configure the stub.
            $stub->method('doSomething')
                 ->will($this->onConsecutiveCalls(2, 3, 5, 7));

            // $stub->doSomething() returns a different value each time
            $this->assertEquals(2, $stub->doSomething());
            $this->assertEquals(3, $stub->doSomething());
            $this->assertEquals(5, $stub->doSomething());
        }
    }
    ?>

Instead of returning a value, a stubbed method can also raise an
exception. ? shows how to use `throwException()` to do this.

getMock()
method()
will()
throwException()
    <?php
    require_once 'SomeClass.php';

    class StubTest extends PHPUnit_Framework_TestCase
    {
        public function testThrowExceptionStub()
        {
            // Create a stub for the SomeClass class.
            $stub = $this->getMockBuilder('SomeClass')
                         ->getMock();

            // Configure the stub.
            $stub->method('doSomething')
                 ->will($this->throwException(new Exception));

            // $stub->doSomething() throws Exception
            $stub->doSomething();
        }
    }
    ?>

Alternatively, you can write the stub yourself and improve your design
along the way. Widely used resources are accessed through a single
façade, so you can easily replace the resource with the stub. For
example, instead of having direct database calls scattered throughout
the code, you have a single `Database` object, an implementor of the
`IDatabase` interface. Then, you can create a stub implementation of
`IDatabase` and use it for your tests. You can even create an option for
running the tests with the stub database or the real database, so you
can use your tests for both local testing during development and
integration testing with the real database.

Functionality that needs to be stubbed out tends to cluster in the same
object, improving cohesion. By presenting the functionality with a
single, coherent interface you reduce the coupling with the rest of the
system.

Mock Objects {#test-doubles.mock-objects}
============

The practice of replacing an object with a test double that verifies
expectations, for instance asserting that a method has been called, is
referred to as *mocking*.

Mock Object You can use a *mock object* "as an observation point that is
used to verify the indirect outputs of the SUT as it is exercised.
Typically, the mock object also includes the functionality of a test
stub in that it must return values to the SUT if it hasn't already
failed the tests but the emphasis is on the verification of the indirect
outputs. Therefore, a mock object is a lot more than just a test stub
plus assertions; it is used in a fundamentally different way" (Gerard
Meszaros).

> **Note**
>
> Only mock objects generated within the scope of a test will be
> verified automatically by PHPUnit. Mock objects generated in data
> providers, for instance, or injected into the test using the
> `@depends` annotation will not be verified automatically by PHPUnit.

Here is an example: suppose we want to test that the correct method,
`update()` in our example, is called on an object that observes another
object. ? shows the code for the `Subject` and `Observer` classes that
are part of the System under Test (SUT).

    <?php
    class Subject
    {
        protected $observers = array();
        protected $name;

        public function __construct($name)
        {
            $this->name = $name;
        }

        public function getName()
        {
            return $this->name;
        }

        public function attach(Observer $observer)
        {
            $this->observers[] = $observer;
        }

        public function doSomething()
        {
            // Do something.
            // ...

            // Notify observers that we did something.
            $this->notify('something');
        }

        public function doSomethingBad()
        {
            foreach ($this->observers as $observer) {
                $observer->reportError(42, 'Something bad happened', $this);
            }
        }

        protected function notify($argument)
        {
            foreach ($this->observers as $observer) {
                $observer->update($argument);
            }
        }

        // Other methods.
    }

    class Observer
    {
        public function update($argument)
        {
            // Do something.
        }

        public function reportError($errorCode, $errorMessage, Subject $subject)
        {
            // Do something
        }

        // Other methods.
    }
    ?>

Mock Object ? shows how to use a mock object to test the interaction
between `Subject` and `Observer` objects.

We first use the `getMock()` method that is provided by the
`PHPUnit_Framework_TestCase` class to set up a mock object for the
`Observer`. Since we give an array as the second (optional) parameter
for the `getMock()` method, only the `update()` method of the `Observer`
class is replaced by a mock implementation.

Because we are interested in verifying that a method is called, and
which arguments it is called with, we introduce the `expects()` and
`with()` methods to specify how this interaction should look.

    <?php
    class SubjectTest extends PHPUnit_Framework_TestCase
    {
        public function testObserversAreUpdated()
        {
            // Create a mock for the Observer class,
            // only mock the update() method.
            $observer = $this->getMockBuilder('Observer')
                             ->setMethods(array('update'))
                             ->getMock();

            // Set up the expectation for the update() method
            // to be called only once and with the string 'something'
            // as its parameter.
            $observer->expects($this->once())
                     ->method('update')
                     ->with($this->equalTo('something'));

            // Create a Subject object and attach the mocked
            // Observer object to it.
            $subject = new Subject('My subject');
            $subject->attach($observer);

            // Call the doSomething() method on the $subject object
            // which we expect to call the mocked Observer object's
            // update() method with the string 'something'.
            $subject->doSomething();
        }
    }
    ?>

The `with()` method can take any number of arguments, corresponding to
the number of arguments to the method being mocked. You can specify more
advanced constraints on the method's arguments than a simple match.

    <?php
    class SubjectTest extends PHPUnit_Framework_TestCase
    {
        public function testErrorReported()
        {
            // Create a mock for the Observer class, mocking the
            // reportError() method
            $observer = $this->getMockBuilder('Observer')
                             ->setMethods(array('reportError'))
                             ->getMock();

            $observer->expects($this->once())
                     ->method('reportError')
                     ->with(
                           $this->greaterThan(0),
                           $this->stringContains('Something'),
                           $this->anything()
                       );

            $subject = new Subject('My subject');
            $subject->attach($observer);

            // The doSomethingBad() method should report an error to the observer
            // via the reportError() method
            $subject->doSomethingBad();
        }
    }
    ?>

The `withConsecutive()` method can take any number of arrays of
arguments, depending on the calls you want to test against. Each array
is a list of constraints corresponding to the arguments of the method
being mocked, like in `with()`.

    <?php
    class FooTest extends PHPUnit_Framework_TestCase
    {
        public function testFunctionCalledTwoTimesWithSpecificArguments()
        {
            $mock = $this->getMockBuilder('stdClass')
                         ->setMethods(array('set'))
                         ->getMock();

            $mock->expects($this->exactly(2))
                 ->method('set')
                 ->withConsecutive(
                     array($this->equalTo('foo'), $this->greaterThan(0)),
                     array($this->equalTo('bar'), $this->greaterThan(0))
                 );

            $mock->set('foo', 21);
            $mock->set('bar', 48);
        }
    }
    ?>

The `callback()` constraint can be used for more complex argument
verification. This constraint takes a PHP callback as its only argument.
The PHP callback will receive the argument to be verified as its only
argument and should return `TRUE` if the argument passes verification
and `FALSE` otherwise.

    <?php
    class SubjectTest extends PHPUnit_Framework_TestCase
    {
        public function testErrorReported()
        {
            // Create a mock for the Observer class, mocking the
            // reportError() method
            $observer = $this->getMockBuilder('Observer')
                             ->setMethods(array('reportError'))
                             ->getMock();

            $observer->expects($this->once())
                     ->method('reportError')
                     ->with($this->greaterThan(0),
                            $this->stringContains('Something'),
                            $this->callback(function($subject){
                              return is_callable(array($subject, 'getName')) &&
                                     $subject->getName() == 'My subject';
                            }));

            $subject = new Subject('My subject');
            $subject->attach($observer);

            // The doSomethingBad() method should report an error to the observer
            // via the reportError() method
            $subject->doSomethingBad();
        }
    }
    ?>

    <?php
    class FooTest extends PHPUnit_Framework_TestCase
    {
        public function testIdenticalObjectPassed()
        {
            $expectedObject = new stdClass;

            $mock = $this->getMockBuilder('stdClass')
                         ->setMethods(array('foo'))
                         ->getMock();

            $mock->expects($this->once())
                 ->method('foo')
                 ->with($this->identicalTo($expectedObject));

            $mock->foo($expectedObject);
        }
    }
    ?>

    <?php
    class FooTest extends PHPUnit_Framework_TestCase
    {
        public function testIdenticalObjectPassed()
        {
            $cloneArguments = true;

            $mock = $this->getMockBuilder('stdClass')
                         ->enableArgumentCloning()
                         ->getMock();

            // now your mock clones parameters so the identicalTo constraint
            // will fail.
        }
    }
    ?>

? shows the constraints that can be applied to method arguments and ?
shows the matchers that are available to specify the number of
invocations.

  Matcher                                                                   Meaning
  ------------------------------------------------------------------------- --------------------------------------------------------------------------------------------------------
  `PHPUnit_Framework_MockObject_Matcher_AnyInvokedCount any()`              Returns a matcher that matches when the method it is evaluated for is executed zero or more times.
  `PHPUnit_Framework_MockObject_Matcher_InvokedCount never()`               Returns a matcher that matches when the method it is evaluated for is never executed.
  `PHPUnit_Framework_MockObject_Matcher_InvokedAtLeastOnce atLeastOnce()`   Returns a matcher that matches when the method it is evaluated for is executed at least once.
  `PHPUnit_Framework_MockObject_Matcher_InvokedCount once()`                Returns a matcher that matches when the method it is evaluated for is executed exactly once.
  `PHPUnit_Framework_MockObject_Matcher_InvokedCount exactly(int $count)`   Returns a matcher that matches when the method it is evaluated for is executed exactly `$count` times.
  `PHPUnit_Framework_MockObject_Matcher_InvokedAtIndex at(int $index)`      Returns a matcher that matches when the method it is evaluated for is invoked at the given `$index`.

  : Matchers

> **Note**
>
> The `$index` parameter for the `at()` matcher refers to the index,
> starting at zero, in *all method invocations* for a given mock object.
> Exercise caution when using this matcher as it can lead to brittle
> tests which are too closely tied to specific implementation details.

Prophecy {#test-doubles.prophecy}
========

[Prophecy](https://github.com/phpspec/prophecy) is a "highly opinionated
yet very powerful and flexible PHP object mocking framework. Though
initially it was created to fulfil phpspec2 needs, it is flexible enough
to be used inside any testing framework out there with minimal effort".

PHPUnit has built-in support for using Prophecy to create test doubles
since version 4.5. ? shows how the same test shown in ? can be expressed
using Prophecy's philosophy of prophecies and revelations:

    <?php
    class SubjectTest extends PHPUnit_Framework_TestCase
    {
        public function testObserversAreUpdated()
        {
            $subject = new Subject('My subject');

            // Create a prophecy for the Observer class.
            $observer = $this->prophesize('Observer');

            // Set up the expectation for the update() method
            // to be called only once and with the string 'something'
            // as its parameter.
            $observer->update('something')->shouldBeCalled();

            // Reveal the prophecy and attach the mock object
            // to the Subject.
            $subject->attach($observer->reveal());

            // Call the doSomething() method on the $subject object
            // which we expect to call the mocked Observer object's
            // update() method with the string 'something'.
            $subject->doSomething();
        }
    }
    ?>

Please refer to the
[documentation](https://github.com/phpspec/prophecy#how-to-use-it) for
Prophecy for further details on how to create, configure, and use stubs,
spies, and mocks using this alternative test double framework.

Mocking Traits and Abstract Classes {#test-doubles.mocking-traits-and-abstract-classes}
===================================

getMockForTrait() The `getMockForTrait()` method returns a mock object
that uses a specified trait. All abstract methods of the given trait are
mocked. This allows for testing the concrete methods of a trait.

    <?php
    trait AbstractTrait
    {
        public function concreteMethod()
        {
            return $this->abstractMethod();
        }

        public abstract function abstractMethod();
    }

    class TraitClassTest extends PHPUnit_Framework_TestCase
    {
        public function testConcreteMethod()
        {
            $mock = $this->getMockForTrait('AbstractTrait');

            $mock->expects($this->any())
                 ->method('abstractMethod')
                 ->will($this->returnValue(TRUE));

            $this->assertTrue($mock->concreteMethod());
        }
    }
    ?>

getMockForAbstractClass() The `getMockForAbstractClass()` method returns
a mock object for an abstract class. All abstract methods of the given
abstract class are mocked. This allows for testing the concrete methods
of an abstract class.

    <?php
    abstract class AbstractClass
    {
        public function concreteMethod()
        {
            return $this->abstractMethod();
        }

        public abstract function abstractMethod();
    }

    class AbstractClassTest extends PHPUnit_Framework_TestCase
    {
        public function testConcreteMethod()
        {
            $stub = $this->getMockForAbstractClass('AbstractClass');

            $stub->expects($this->any())
                 ->method('abstractMethod')
                 ->will($this->returnValue(TRUE));

            $this->assertTrue($stub->concreteMethod());
        }
    }
    ?>

Stubbing and Mocking Web Services {#test-doubles.stubbing-and-mocking-web-services}
=================================

getMockFromWsdl() When your application interacts with a web service you
want to test it without actually interacting with the web service. To
make the stubbing and mocking of web services easy, the
`getMockFromWsdl()` can be used just like `getMock()` (see above). The
only difference is that `getMockFromWsdl()` returns a stub or mock based
on a web service description in WSDL and `getMock()` returns a stub or
mock based on a PHP class or interface.

? shows how `getMockFromWsdl()` can be used to stub, for example, the
web service described in `GoogleSearch.wsdl`.

    <?php
    class GoogleTest extends PHPUnit_Framework_TestCase
    {
        public function testSearch()
        {
            $googleSearch = $this->getMockFromWsdl(
              'GoogleSearch.wsdl', 'GoogleSearch'
            );

            $directoryCategory = new stdClass;
            $directoryCategory->fullViewableName = '';
            $directoryCategory->specialEncoding = '';

            $element = new stdClass;
            $element->summary = '';
            $element->URL = 'https://phpunit.de/';
            $element->snippet = '...';
            $element->title = '<b>PHPUnit</b>';
            $element->cachedSize = '11k';
            $element->relatedInformationPresent = TRUE;
            $element->hostName = 'phpunit.de';
            $element->directoryCategory = $directoryCategory;
            $element->directoryTitle = '';

            $result = new stdClass;
            $result->documentFiltering = FALSE;
            $result->searchComments = '';
            $result->estimatedTotalResultsCount = 3.9000;
            $result->estimateIsExact = FALSE;
            $result->resultElements = array($element);
            $result->searchQuery = 'PHPUnit';
            $result->startIndex = 1;
            $result->endIndex = 1;
            $result->searchTips = '';
            $result->directoryCategories = array();
            $result->searchTime = 0.248822;

            $googleSearch->expects($this->any())
                         ->method('doGoogleSearch')
                         ->will($this->returnValue($result));

            /**
             * $googleSearch->doGoogleSearch() will now return a stubbed result and
             * the web service's doGoogleSearch() method will not be invoked.
             */
            $this->assertEquals(
              $result,
              $googleSearch->doGoogleSearch(
                '00000000000000000000000000000000',
                'PHPUnit',
                0,
                1,
                FALSE,
                '',
                FALSE,
                '',
                '',
                ''
              )
            );
        }
    }
    ?>

Mocking the Filesystem {#test-doubles.mocking-the-filesystem}
======================

[vfsStream](https://github.com/mikey179/vfsStream) is a [stream
wrapper](http://www.php.net/streams) for a [virtual
filesystem](http://en.wikipedia.org/wiki/Virtual_file_system) that may
be helpful in unit tests to mock the real filesystem.

Simply add a dependency on `mikey179/vfsStream` to your project's
`composer.json` file if you use [Composer](https://getcomposer.org/) to
manage the dependencies of your project. Here is a minimal example of a
`composer.json` file that just defines a development-time dependency on
PHPUnit 4.6 and vfsStream:

    {
        "require-dev": {
            "phpunit/phpunit": "~4.6",
            "mikey179/vfsStream": "~1"
        }
    }

? shows a class that interacts with the filesystem.

    <?php
    class Example
    {
        protected $id;
        protected $directory;

        public function __construct($id)
        {
            $this->id = $id;
        }

        public function setDirectory($directory)
        {
            $this->directory = $directory . DIRECTORY_SEPARATOR . $this->id;

            if (!file_exists($this->directory)) {
                mkdir($this->directory, 0700, TRUE);
            }
        }
    }?>

Without a virtual filesystem such as vfsStream we cannot test the
`setDirectory()` method in isolation from external influence (see ?).

    <?php
    require_once 'Example.php';

    class ExampleTest extends PHPUnit_Framework_TestCase
    {
        protected function setUp()
        {
            if (file_exists(dirname(__FILE__) . '/id')) {
                rmdir(dirname(__FILE__) . '/id');
            }
        }

        public function testDirectoryIsCreated()
        {
            $example = new Example('id');
            $this->assertFalse(file_exists(dirname(__FILE__) . '/id'));

            $example->setDirectory(dirname(__FILE__));
            $this->assertTrue(file_exists(dirname(__FILE__) . '/id'));
        }

        protected function tearDown()
        {
            if (file_exists(dirname(__FILE__) . '/id')) {
                rmdir(dirname(__FILE__) . '/id');
            }
        }
    }
    ?>

The approach above has several drawbacks:

-   As with any external resource, there might be intermittent problems
    with the filesystem. This makes tests interacting with it flaky.

-   In the `setUp()` and `tearDown()` methods we have to ensure that the
    directory does not exist before and after the test.

-   When the test execution terminates before the `tearDown()` method is
    invoked the directory will stay in the filesystem.

? shows how vfsStream can be used to mock the filesystem in a test for a
class that interacts with the filesystem.

    <?php
    require_once 'vfsStream/vfsStream.php';
    require_once 'Example.php';

    class ExampleTest extends PHPUnit_Framework_TestCase
    {
        public function setUp()
        {
            vfsStreamWrapper::register();
            vfsStreamWrapper::setRoot(new vfsStreamDirectory('exampleDir'));
        }

        public function testDirectoryIsCreated()
        {
            $example = new Example('id');
            $this->assertFalse(vfsStreamWrapper::getRoot()->hasChild('id'));

            $example->setDirectory(vfsStream::url('exampleDir'));
            $this->assertTrue(vfsStreamWrapper::getRoot()->hasChild('id'));
        }
    }
    ?>

This has several advantages:

-   The test itself is more concise.

-   vfsStream gives the test developer full control over what the
    filesystem environment looks like to the tested code.

-   Since the filesystem operations do not operate on the real
    filesystem anymore, cleanup operations in a `tearDown()` method are
    no longer required.


