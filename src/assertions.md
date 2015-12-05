Assertions {#appendixes.assertions}
==========

This appendix lists the various assertion methods that are available.

assertArrayHasKey() {#appendixes.assertions.assertArrayHasKey}
===================

assertArrayHasKey()
assertArrayNotHasKey()
`assertArrayHasKey(mixed $key, array $array[, string $message = ''])`

Reports an error identified by `$message` if `$array` does not have the
`$key`.

`assertArrayNotHasKey()` is the inverse of this assertion and takes the
same arguments.

    <?php
    class ArrayHasKeyTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertArrayHasKey('foo', array('bar' => 'baz'));
        }
    }
    ?>

    phpunit ArrayHasKeyTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ArrayHasKeyTest::testFailure
    Failed asserting that an array has the key 'foo'.

    /home/sb/ArrayHasKeyTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertClassHasAttribute() {#appendixes.assertions.assertClassHasAttribute}
=========================

assertClassHasAttribute()
assertClassNotHasAttribute()
`assertClassHasAttribute(string $attributeName, string $className[, string $message = ''])`

Reports an error identified by `$message` if `$className::attributeName`
does not exist.

`assertClassNotHasAttribute()` is the inverse of this assertion and
takes the same arguments.

    <?php
    class ClassHasAttributeTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertClassHasAttribute('foo', 'stdClass');
        }
    }
    ?>

    phpunit ClassHasAttributeTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ClassHasAttributeTest::testFailure
    Failed asserting that class "stdClass" has attribute "foo".

    /home/sb/ClassHasAttributeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertArraySubset() {#appendixes.assertions.assertArraySubset}
===================

assertArraySubset()
assertArraySubset()
`assertArraySubset(array $subset, array $array[, bool $strict = '', string $message = ''])`

Reports an error identified by `$message` if `$array` does not contains
the `$subset`.

`$strict` is a flag used to compare the identity of objects within
arrays.

    <?php
    class ArraySubsetTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertArraySubset(['config' => ['key-a', 'key-b']], ['config' => ['key-a']]);
        }
    }
    ?>

    phpunit ArrayHasKeyTest
    PHPUnit 4.4.0 by Sebastian Bergmann.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) Epilog\EpilogTest::testNoFollowOption
    Failed asserting that an array has the subset Array &0 (
        'config' => Array &1 (
            0 => 'key-a'
            1 => 'key-b'
        )
    ).

    /home/sb/ArraySubsetTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertClassHasStaticAttribute() {#appendixes.assertions.assertClassHasStaticAttribute}
===============================

assertClassHasStaticAttribute()
assertClassNotHasStaticAttribute()
`assertClassHasStaticAttribute(string $attributeName, string $className[, string $message = ''])`

Reports an error identified by `$message` if `$className::attributeName`
does not exist.

`assertClassNotHasStaticAttribute()` is the inverse of this assertion
and takes the same arguments.

    <?php
    class ClassHasStaticAttributeTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertClassHasStaticAttribute('foo', 'stdClass');
        }
    }
    ?>

    phpunit ClassHasStaticAttributeTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ClassHasStaticAttributeTest::testFailure
    Failed asserting that class "stdClass" has static attribute "foo".

    /home/sb/ClassHasStaticAttributeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertContains() {#appendixes.assertions.assertContains}
================

assertContains()
assertNotContains()
assertAttributeContains()
assertAttributeNotContains()
`assertContains(mixed $needle, Iterator|array $haystack[, string $message = ''])`

Reports an error identified by `$message` if `$needle` is not an element
of `$haystack`.

`assertNotContains()` is the inverse of this assertion and takes the
same arguments.

`assertAttributeContains()` and `assertAttributeNotContains()` are
convenience wrappers that use a `public`, `protected`, or `private`
attribute of a class or object as the haystack.

    <?php
    class ContainsTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertContains(4, array(1, 2, 3));
        }
    }
    ?>

    phpunit ContainsTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsTest::testFailure
    Failed asserting that an array contains 4.

    /home/sb/ContainsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

`assertContains(string $needle, string $haystack[, string $message = '', boolean $ignoreCase = FALSE])`

Reports an error identified by `$message` if `$needle` is not a
substring of `$haystack`.

If `$ignoreCase` is `TRUE`, the test will be case insensitive.

    <?php
    class ContainsTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertContains('baz', 'foobar');
        }
    }
    ?>

    phpunit ContainsTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsTest::testFailure
    Failed asserting that 'foobar' contains "baz".

    /home/sb/ContainsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

    <?php
    class ContainsTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertContains('foo', 'FooBar');
        }

        public function testOK()
        {
            $this->assertContains('foo', 'FooBar', '', true);
        }
    }
    ?>

    phpunit ContainsTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F.

    Time: 0 seconds, Memory: 2.75Mb

    There was 1 failure:

    1) ContainsTest::testFailure
    Failed asserting that 'FooBar' contains "foo".

    /home/sb/ContainsTest.php:6

    FAILURES!
    Tests: 2, Assertions: 2, Failures: 1.

assertContainsOnly() {#appendixes.assertions.assertContainsOnly}
====================

assertContainsOnly()
assertNotContainsOnly()
assertAttributeContainsOnly()
assertAttributeNotContainsOnly()
`assertContainsOnly(string $type, Iterator|array $haystack[, boolean $isNativeType = NULL, string $message = ''])`

Reports an error identified by `$message` if `$haystack` does not
contain only variables of type `$type`.

`$isNativeType` is a flag used to indicate whether `$type` is a native
PHP type or not.

`assertNotContainsOnly()` is the inverse of this assertion and takes the
same arguments.

`assertAttributeContainsOnly()` and `assertAttributeNotContainsOnly()`
are convenience wrappers that use a `public`, `protected`, or `private`
attribute of a class or object as the haystack.

    <?php
    class ContainsOnlyTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertContainsOnly('string', array('1', '2', 3));
        }
    }
    ?>

    phpunit ContainsOnlyTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsOnlyTest::testFailure
    Failed asserting that Array (
        0 => '1'
        1 => '2'
        2 => 3
    ) contains only values of type "string".

    /home/sb/ContainsOnlyTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertContainsOnlyInstancesOf() {#appendixes.assertions.assertContainsOnlyInstancesOf}
===============================

assertContainsOnlyInstancesOf()
`assertContainsOnlyInstancesOf(string $classname, Traversable|array $haystack[, string $message = ''])`

Reports an error identified by `$message` if `$haystack` does not
contain only instances of class `$classname`.

    <?php
    class ContainsOnlyInstancesOfTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertContainsOnlyInstancesOf('Foo', array(new Foo(), new Bar(), new Foo()));
        }
    }
    ?>

    phpunit ContainsOnlyInstancesOfTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsOnlyInstancesOfTest::testFailure
    Failed asserting that Array ([0]=> Bar Object(...)) is an instance of class "Foo".

    /home/sb/ContainsOnlyInstancesOfTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertCount() {#appendixes.assertions.assertCount}
=============

assertCount()
assertNotCount()
`assertCount($expectedCount, $haystack[, string $message = ''])`

Reports an error identified by `$message` if the number of elements in
`$haystack` is not `$expectedCount`.

`assertNotCount()` is the inverse of this assertion and takes the same
arguments.

    <?php
    class CountTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertCount(0, array('foo'));
        }
    }
    ?>

    phpunit CountTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) CountTest::testFailure
    Failed asserting that actual size 1 matches expected size 0.

    /home/sb/CountTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertEmpty() {#appendixes.assertions.assertEmpty}
=============

assertEmpty()
assertNotEmpty()
assertAttributeEmpty()
assertAttributeNotEmpty()
`assertEmpty(mixed $actual[, string $message = ''])`

Reports an error identified by `$message` if `$actual` is not empty.

`assertNotEmpty()` is the inverse of this assertion and takes the same
arguments.

`assertAttributeEmpty()` and `assertAttributeNotEmpty()` are convenience
wrappers that can be applied to a `public`, `protected`, or `private`
attribute of a class or object.

    <?php
    class EmptyTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertEmpty(array('foo'));
        }
    }
    ?>

    phpunit EmptyTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) EmptyTest::testFailure
    Failed asserting that an array is empty.

    /home/sb/EmptyTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertEqualXMLStructure() {#appendixes.assertions.assertEqualXMLStructure}
=========================

assertEqualXMLStructure()
`assertEqualXMLStructure(DOMElement $expectedElement, DOMElement $actualElement[, boolean $checkAttributes = FALSE, string $message = ''])`

Reports an error identified by `$message` if the XML Structure of the
DOMElement in `$actualElement` is not equal to the XML structure of the
DOMElement in `$expectedElement`.

    <?php
    class EqualXMLStructureTest extends PHPUnit_Framework_TestCase
    {
        public function testFailureWithDifferentNodeNames()
        {
            $expected = new DOMElement('foo');
            $actual = new DOMElement('bar');

            $this->assertEqualXMLStructure($expected, $actual);
        }

        public function testFailureWithDifferentNodeAttributes()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo bar="true" />');

            $actual = new DOMDocument;
            $actual->loadXML('<foo/>');

            $this->assertEqualXMLStructure(
              $expected->firstChild, $actual->firstChild, TRUE
            );
        }

        public function testFailureWithDifferentChildrenCount()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo><bar/><bar/><bar/></foo>');

            $actual = new DOMDocument;
            $actual->loadXML('<foo><bar/></foo>');

            $this->assertEqualXMLStructure(
              $expected->firstChild, $actual->firstChild
            );
        }

        public function testFailureWithDifferentChildren()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo><bar/><bar/><bar/></foo>');

            $actual = new DOMDocument;
            $actual->loadXML('<foo><baz/><baz/><baz/></foo>');

            $this->assertEqualXMLStructure(
              $expected->firstChild, $actual->firstChild
            );
        }
    }
    ?>

    phpunit EqualXMLStructureTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    FFFF

    Time: 0 seconds, Memory: 5.75Mb

    There were 4 failures:

    1) EqualXMLStructureTest::testFailureWithDifferentNodeNames
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'foo'
    +'bar'

    /home/sb/EqualXMLStructureTest.php:9

    2) EqualXMLStructureTest::testFailureWithDifferentNodeAttributes
    Number of attributes on node "foo" does not match
    Failed asserting that 0 matches expected 1.

    /home/sb/EqualXMLStructureTest.php:22

    3) EqualXMLStructureTest::testFailureWithDifferentChildrenCount
    Number of child nodes of "foo" differs
    Failed asserting that 1 matches expected 3.

    /home/sb/EqualXMLStructureTest.php:35

    4) EqualXMLStructureTest::testFailureWithDifferentChildren
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'bar'
    +'baz'

    /home/sb/EqualXMLStructureTest.php:48

    FAILURES!
    Tests: 4, Assertions: 8, Failures: 4.

assertEquals() {#appendixes.assertions.assertEquals}
==============

assertEquals()
assertNotEquals()
assertAttributeEquals()
assertAttributeNotEquals()
`assertEquals(mixed $expected, mixed $actual[, string $message = ''])`

Reports an error identified by `$message` if the two variables
`$expected` and `$actual` are not equal.

`assertNotEquals()` is the inverse of this assertion and takes the same
arguments.

`assertAttributeEquals()` and `assertAttributeNotEquals()` are
convenience wrappers that use a `public`, `protected`, or `private`
attribute of a class or object as the actual value.

    <?php
    class EqualsTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertEquals(1, 0);
        }

        public function testFailure2()
        {
            $this->assertEquals('bar', 'baz');
        }

        public function testFailure3()
        {
            $this->assertEquals("foo\nbar\nbaz\n", "foo\nbah\nbaz\n");
        }
    }
    ?>

    phpunit EqualsTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    FFF

    Time: 0 seconds, Memory: 5.25Mb

    There were 3 failures:

    1) EqualsTest::testFailure
    Failed asserting that 0 matches expected 1.

    /home/sb/EqualsTest.php:6

    2) EqualsTest::testFailure2
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'bar'
    +'baz'

    /home/sb/EqualsTest.php:11

    3) EqualsTest::testFailure3
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
     'foo
    -bar
    +bah
     baz
     '

    /home/sb/EqualsTest.php:16

    FAILURES!
    Tests: 3, Assertions: 3, Failures: 3.

More specialized comparisons are used for specific argument types for
`$expected` and `$actual`, see below.

`assertEquals(float $expected, float $actual[, string $message = '', float $delta = 0])`

Reports an error identified by `$message` if the two floats `$expected`
and `$actual` are not within `$delta` of each other.

Please read "[What Every Computer Scientist Should Know About
Floating-Point
Arithmetic](http://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html)"
to understand why `$delta` is neccessary.

    <?php
    class EqualsTest extends PHPUnit_Framework_TestCase
    {
        public function testSuccess()
        {
            $this->assertEquals(1.0, 1.1, '', 0.2);
        }

        public function testFailure()
        {
            $this->assertEquals(1.0, 1.1);
        }
    }
    ?>

    phpunit EqualsTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    .F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that 1.1 matches expected 1.0.

    /home/sb/EqualsTest.php:11

    FAILURES!
    Tests: 2, Assertions: 2, Failures: 1.

`assertEquals(DOMDocument $expected, DOMDocument $actual[, string $message = ''])`

Reports an error identified by `$message` if the uncommented canonical
form of the XML documents represented by the two DOMDocument objects
`$expected` and `$actual` are not equal.

    <?php
    class EqualsTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo><bar/></foo>');

            $actual = new DOMDocument;
            $actual->loadXML('<bar><foo/></bar>');

            $this->assertEquals($expected, $actual);
        }
    }
    ?>

    phpunit EqualsTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
    -<foo>
    -  <bar/>
    -</foo>
    +<bar>
    +  <foo/>
    +</bar>

    /home/sb/EqualsTest.php:12

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

`assertEquals(object $expected, object $actual[, string $message = ''])`

Reports an error identified by `$message` if the two objects `$expected`
and `$actual` do not have equal attribute values.

    <?php
    class EqualsTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $expected = new stdClass;
            $expected->foo = 'foo';
            $expected->bar = 'bar';

            $actual = new stdClass;
            $actual->foo = 'bar';
            $actual->baz = 'bar';

            $this->assertEquals($expected, $actual);
        }
    }
    ?>

    phpunit EqualsTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that two objects are equal.
    --- Expected
    +++ Actual
    @@ @@
     stdClass Object (
    -    'foo' => 'foo'
    -    'bar' => 'bar'
    +    'foo' => 'bar'
    +    'baz' => 'bar'
     )

    /home/sb/EqualsTest.php:14

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

`assertEquals(array $expected, array $actual[, string $message = ''])`

Reports an error identified by `$message` if the two arrays `$expected`
and `$actual` are not equal.

    <?php
    class EqualsTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertEquals(array('a', 'b', 'c'), array('a', 'c', 'd'));
        }
    }
    ?>

    phpunit EqualsTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
     Array (
         0 => 'a'
    -    1 => 'b'
    -    2 => 'c'
    +    1 => 'c'
    +    2 => 'd'
     )

    /home/sb/EqualsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertFalse() {#appendixes.assertions.assertFalse}
=============

assertFalse()
`assertFalse(bool $condition[, string $message = ''])`

Reports an error identified by `$message` if `$condition` is `TRUE`.

`assertNotFalse()` is the inverse of this assertion and takes the same
arguments.

    <?php
    class FalseTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertFalse(TRUE);
        }
    }
    ?>

    phpunit FalseTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) FalseTest::testFailure
    Failed asserting that true is false.

    /home/sb/FalseTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertFileEquals() {#appendixes.assertions.assertFileEquals}
==================

assertFileEquals()
assertFileNotEquals()
`assertFileEquals(string $expected, string $actual[, string $message = ''])`

Reports an error identified by `$message` if the file specified by
`$expected` does not have the same contents as the file specified by
`$actual`.

`assertFileNotEquals()` is the inverse of this assertion and takes the
same arguments.

    <?php
    class FileEqualsTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertFileEquals('/home/sb/expected', '/home/sb/actual');
        }
    }
    ?>

    phpunit FileEqualsTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) FileEqualsTest::testFailure
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'expected
    +'actual
     '

    /home/sb/FileEqualsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

assertFileExists() {#appendixes.assertions.assertFileExists}
==================

assertFileExists()
assertFileNotExists()
`assertFileExists(string $filename[, string $message = ''])`

Reports an error identified by `$message` if the file specified by
`$filename` does not exist.

`assertFileNotExists()` is the inverse of this assertion and takes the
same arguments.

    <?php
    class FileExistsTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertFileExists('/path/to/file');
        }
    }
    ?>

    phpunit FileExistsTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) FileExistsTest::testFailure
    Failed asserting that file "/path/to/file" exists.

    /home/sb/FileExistsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertGreaterThan() {#appendixes.assertions.assertGreaterThan}
===================

assertGreaterThan()
assertAttributeGreaterThan()
`assertGreaterThan(mixed $expected, mixed $actual[, string $message = ''])`

Reports an error identified by `$message` if the value of `$actual` is
not greater than the value of `$expected`.

`assertAttributeGreaterThan()` is a convenience wrapper that uses a
`public`, `protected`, or `private` attribute of a class or object as
the actual value.

    <?php
    class GreaterThanTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertGreaterThan(2, 1);
        }
    }
    ?>

    phpunit GreaterThanTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) GreaterThanTest::testFailure
    Failed asserting that 1 is greater than 2.

    /home/sb/GreaterThanTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertGreaterThanOrEqual() {#appendixes.assertions.assertGreaterThanOrEqual}
==========================

assertGreaterThanOrEqual()
assertAttributeGreaterThanOrEqual()
`assertGreaterThanOrEqual(mixed $expected, mixed $actual[, string $message = ''])`

Reports an error identified by `$message` if the value of `$actual` is
not greater than or equal to the value of `$expected`.

`assertAttributeGreaterThanOrEqual()` is a convenience wrapper that uses
a `public`, `protected`, or `private` attribute of a class or object as
the actual value.

    <?php
    class GreatThanOrEqualTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertGreaterThanOrEqual(2, 1);
        }
    }
    ?>

    phpunit GreaterThanOrEqualTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) GreatThanOrEqualTest::testFailure
    Failed asserting that 1 is equal to 2 or is greater than 2.

    /home/sb/GreaterThanOrEqualTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

assertInfinite() {#appendixes.assertions.assertInfinite}
================

assertInfinite()
assertFinite()
`assertInfinite(mixed $variable[, string $message = ''])`

Reports an error identified by `$message` if `$variable` is not `INF`.

`assertFinite()` is the inverse of this assertion and takes the same
arguments.

    <?php
    class InfiniteTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertInfinite(1);
        }
    }
    ?>

    phpunit InfiniteTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) InfiniteTest::testFailure
    Failed asserting that 1 is infinite.

    /home/sb/InfiniteTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertInstanceOf() {#appendixes.assertions.assertInstanceOf}
==================

assertInstanceOf()
assertNotInstanceOf()
assertAttributeInstanceOf()
assertAttributeNotInstanceOf()
`assertInstanceOf($expected, $actual[, $message = ''])`

Reports an error identified by `$message` if `$actual` is not an
instance of `$expected`.

`assertNotInstanceOf()` is the inverse of this assertion and takes the
same arguments.

`assertAttributeInstanceOf()` and `assertAttributeNotInstanceOf()` are
convenience wrappers that can be applied to a `public`, `protected`, or
`private` attribute of a class or object.

    <?php
    class InstanceOfTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertInstanceOf('RuntimeException', new Exception);
        }
    }
    ?>

    phpunit InstanceOfTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) InstanceOfTest::testFailure
    Failed asserting that Exception Object (...) is an instance of class "RuntimeException".

    /home/sb/InstanceOfTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertInternalType() {#appendixes.assertions.assertInternalType}
====================

assertInternalType()
assertNotInternalType()
assertAttributeInternalType()
assertAttributeNotInternalType()
`assertInternalType($expected, $actual[, $message = ''])`

Reports an error identified by `$message` if `$actual` is not of the
`$expected` type.

`assertNotInternalType()` is the inverse of this assertion and takes the
same arguments.

`assertAttributeInternalType()` and `assertAttributeNotInternalType()`
are convenience wrappers that can be applied to a `public`, `protected`,
or `private` attribute of a class or object.

    <?php
    class InternalTypeTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertInternalType('string', 42);
        }
    }
    ?>

    phpunit InternalTypeTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) InternalTypeTest::testFailure
    Failed asserting that 42 is of type "string".

    /home/sb/InternalTypeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertJsonFileEqualsJsonFile() {#appendixes.assertions.assertJsonFileEqualsJsonFile}
==============================

assertJsonFileEqualsJsonFile()
assertJsonFileNotEqualsJsonFile()
`assertJsonFileEqualsJsonFile(mixed $expectedFile, mixed $actualFile[, string $message = ''])`

Reports an error identified by `$message` if the value of `$actualFile`
does not match the value of `$expectedFile`.

    <?php
    class JsonFileEqualsJsonFileTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertJsonFileEqualsJsonFile(
              'path/to/fixture/file', 'path/to/actual/file');
        }
    }
    ?>

    phpunit JsonFileEqualsJsonFileTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) JsonFileEqualsJsonFile::testFailure
    Failed asserting that '{"Mascott":"Tux"}' matches JSON string "["Mascott", "Tux", "OS", "Linux"]".

    /home/sb/JsonFileEqualsJsonFileTest.php:5

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

assertJsonStringEqualsJsonFile() {#appendixes.assertions.assertJsonStringEqualsJsonFile}
================================

assertJsonStringEqualsJsonFile()
assertJsonStringNotEqualsJsonFile()
`assertJsonStringEqualsJsonFile(mixed $expectedFile, mixed $actualJson[, string $message = ''])`

Reports an error identified by `$message` if the value of `$actualJson`
does not match the value of `$expectedFile`.

    <?php
    class JsonStringEqualsJsonFileTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertJsonStringEqualsJsonFile(
              'path/to/fixture/file', json_encode(array("Mascott" => "ux"))
            );
        }
    }
    ?>

    phpunit JsonStringEqualsJsonFileTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) JsonStringEqualsJsonFile::testFailure
    Failed asserting that '{"Mascott":"ux"}' matches JSON string "{"Mascott":"Tux"}".

    /home/sb/JsonStringEqualsJsonFileTest.php:5

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

assertJsonStringEqualsJsonString() {#appendixes.assertions.assertJsonStringEqualsJsonString}
==================================

assertJsonStringEqualsJsonString()
assertJsonStringNotEqualsJsonString()
`assertJsonStringEqualsJsonString(mixed $expectedJson, mixed $actualJson[, string $message = ''])`

Reports an error identified by `$message` if the value of `$actualJson`
does not match the value of `$expectedJson`.

    <?php
    class JsonStringEqualsJsonStringTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertJsonStringEqualsJsonString(
              json_encode(array("Mascott" => "Tux")), json_encode(array("Mascott" => "ux"))
            );
        }
    }
    ?>

    phpunit JsonStringEqualsJsonStringTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) JsonStringEqualsJsonStringTest::testFailure
    Failed asserting that two objects are equal.
    --- Expected
    +++ Actual
    @@ @@
     stdClass Object (
     -    'Mascott' => 'Tux'
     +    'Mascott' => 'ux'
    )

    /home/sb/JsonStringEqualsJsonStringTest.php:5

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

assertLessThan() {#appendixes.assertions.assertLessThan}
================

assertLessThan()
assertAttributeLessThan()
`assertLessThan(mixed $expected, mixed $actual[, string $message = ''])`

Reports an error identified by `$message` if the value of `$actual` is
not less than the value of `$expected`.

`assertAttributeLessThan()` is a convenience wrapper that uses a
`public`, `protected`, or `private` attribute of a class or object as
the actual value.

    <?php
    class LessThanTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertLessThan(1, 2);
        }
    }
    ?>

    phpunit LessThanTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) LessThanTest::testFailure
    Failed asserting that 2 is less than 1.

    /home/sb/LessThanTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertLessThanOrEqual() {#appendixes.assertions.assertLessThanOrEqual}
=======================

assertLessThanOrEqual()
assertAttributeLessThanOrEqual()
`assertLessThanOrEqual(mixed $expected, mixed $actual[, string $message = ''])`

Reports an error identified by `$message` if the value of `$actual` is
not less than or equal to the value of `$expected`.

`assertAttributeLessThanOrEqual()` is a convenience wrapper that uses a
`public`, `protected`, or `private` attribute of a class or object as
the actual value.

    <?php
    class LessThanOrEqualTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertLessThanOrEqual(1, 2);
        }
    }
    ?>

    phpunit LessThanOrEqualTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) LessThanOrEqualTest::testFailure
    Failed asserting that 2 is equal to 1 or is less than 1.

    /home/sb/LessThanOrEqualTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

assertNan() {#appendixes.assertions.assertNan}
===========

assertNan()
`assertNan(mixed $variable[, string $message = ''])`

Reports an error identified by `$message` if `$variable` is not `NAN`.

    <?php
    class NanTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertNan(1);
        }
    }
    ?>

    phpunit NanTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) NanTest::testFailure
    Failed asserting that 1 is nan.

    /home/sb/NanTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertNull() {#appendixes.assertions.assertNull}
============

assertNull()
assertNotNull()
`assertNull(mixed $variable[, string $message = ''])`

Reports an error identified by `$message` if `$variable` is not `NULL`.

`assertNotNull()` is the inverse of this assertion and takes the same
arguments.

    <?php
    class NullTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertNull('foo');
        }
    }
    ?>

    phpunit NotNullTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) NullTest::testFailure
    Failed asserting that 'foo' is null.

    /home/sb/NotNullTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertObjectHasAttribute() {#appendixes.assertions.assertObjectHasAttribute}
==========================

assertObjectHasAttribute()
assertObjectNotHasAttribute()
`assertObjectHasAttribute(string $attributeName, object $object[, string $message = ''])`

Reports an error identified by `$message` if `$object->attributeName`
does not exist.

`assertObjectNotHasAttribute()` is the inverse of this assertion and
takes the same arguments.

    <?php
    class ObjectHasAttributeTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertObjectHasAttribute('foo', new stdClass);
        }
    }
    ?>

    phpunit ObjectHasAttributeTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ObjectHasAttributeTest::testFailure
    Failed asserting that object of class "stdClass" has attribute "foo".

    /home/sb/ObjectHasAttributeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertRegExp() {#appendixes.assertions.assertRegExp}
==============

assertRegExp()
assertNotRegExp()
`assertRegExp(string $pattern, string $string[, string $message = ''])`

Reports an error identified by `$message` if `$string` does not match
the regular expression `$pattern`.

`assertNotRegExp()` is the inverse of this assertion and takes the same
arguments.

    <?php
    class RegExpTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertRegExp('/foo/', 'bar');
        }
    }
    ?>

    phpunit RegExpTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) RegExpTest::testFailure
    Failed asserting that 'bar' matches PCRE pattern "/foo/".

    /home/sb/RegExpTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertStringMatchesFormat() {#appendixes.assertions.assertStringMatchesFormat}
===========================

assertStringMatchesFormat()
assertStringNotMatchesFormat()
`assertStringMatchesFormat(string $format, string $string[, string $message = ''])`

Reports an error identified by `$message` if the `$string` does not
match the `$format` string.

`assertStringNotMatchesFormat()` is the inverse of this assertion and
takes the same arguments.

    <?php
    class StringMatchesFormatTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertStringMatchesFormat('%i', 'foo');
        }
    }
    ?>

    phpunit StringMatchesFormatTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) StringMatchesFormatTest::testFailure
    Failed asserting that 'foo' matches PCRE pattern "/^[+-]?\d+$/s".

    /home/sb/StringMatchesFormatTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

The format string may contain the following placeholders:

-   `%e`: Represents a directory separator, for example `/` on Linux.

-   `%s`: One or more of anything (character or white space) except the
    end of line character.

-   `%S`: Zero or more of anything (character or white space) except the
    end of line character.

-   `%a`: One or more of anything (character or white space) including
    the end of line character.

-   `%A`: Zero or more of anything (character or white space) including
    the end of line character.

-   `%w`: Zero or more white space characters.

-   `%i`: A signed integer value, for example `+3142`, `-3142`.

-   `%d`: An unsigned integer value, for example `123456`.

-   `%x`: One or more hexadecimal character. That is, characters in the
    range `0-9`, `a-f`, `A-F`.

-   `%f`: A floating point number, for example: `3.142`, `-3.142`,
    `3.142E-10`, `3.142e+10`.

-   `%c`: A single character of any sort.

assertStringMatchesFormatFile() {#appendixes.assertions.assertStringMatchesFormatFile}
===============================

assertStringMatchesFormatFile()
assertStringNotMatchesFormatFile()
`assertStringMatchesFormatFile(string $formatFile, string $string[, string $message = ''])`

Reports an error identified by `$message` if the `$string` does not
match the contents of the `$formatFile`.

`assertStringNotMatchesFormatFile()` is the inverse of this assertion
and takes the same arguments.

    <?php
    class StringMatchesFormatFileTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertStringMatchesFormatFile('/path/to/expected.txt', 'foo');
        }
    }
    ?>

    phpunit StringMatchesFormatFileTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) StringMatchesFormatFileTest::testFailure
    Failed asserting that 'foo' matches PCRE pattern "/^[+-]?\d+
    $/s".

    /home/sb/StringMatchesFormatFileTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

assertSame() {#appendixes.assertions.assertSame}
============

assertSame()
assertNotSame()
assertAttributeSame()
assertAttributeNotSame()
`assertSame(mixed $expected, mixed $actual[, string $message = ''])`

Reports an error identified by `$message` if the two variables
`$expected` and `$actual` do not have the same type and value.

`assertNotSame()` is the inverse of this assertion and takes the same
arguments.

`assertAttributeSame()` and `assertAttributeNotSame()` are convenience
wrappers that use a `public`, `protected`, or `private` attribute of a
class or object as the actual value.

    <?php
    class SameTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertSame('2204', 2204);
        }
    }
    ?>

    phpunit SameTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) SameTest::testFailure
    Failed asserting that 2204 is identical to '2204'.

    /home/sb/SameTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

`assertSame(object $expected, object $actual[, string $message = ''])`

Reports an error identified by `$message` if the two variables
`$expected` and `$actual` do not reference the same object.

    <?php
    class SameTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertSame(new stdClass, new stdClass);
        }
    }
    ?>

    phpunit SameTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) SameTest::testFailure
    Failed asserting that two variables reference the same object.

    /home/sb/SameTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertStringEndsWith() {#appendixes.assertions.assertStringEndsWith}
======================

assertStringEndsWith()
assertStringEndsNotWith()
`assertStringEndsWith(string $suffix, string $string[, string $message = ''])`

Reports an error identified by `$message` if the `$string` does not end
with `$suffix`.

`assertStringEndsNotWith()` is the inverse of this assertion and takes
the same arguments.

    <?php
    class StringEndsWithTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertStringEndsWith('suffix', 'foo');
        }
    }
    ?>

    phpunit StringEndsWithTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 1 second, Memory: 5.00Mb

    There was 1 failure:

    1) StringEndsWithTest::testFailure
    Failed asserting that 'foo' ends with "suffix".

    /home/sb/StringEndsWithTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertStringEqualsFile() {#appendixes.assertions.assertStringEqualsFile}
========================

assertStringEqualsFile()
assertStringNotEqualsFile()
`assertStringEqualsFile(string $expectedFile, string $actualString[, string $message = ''])`

Reports an error identified by `$message` if the file specified by
`$expectedFile` does not have `$actualString` as its contents.

`assertStringNotEqualsFile()` is the inverse of this assertion and takes
the same arguments.

    <?php
    class StringEqualsFileTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertStringEqualsFile('/home/sb/expected', 'actual');
        }
    }
    ?>

    phpunit StringEqualsFileTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) StringEqualsFileTest::testFailure
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'expected
    -'
    +'actual'

    /home/sb/StringEqualsFileTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

assertStringStartsWith() {#appendixes.assertions.assertStringStartsWith}
========================

assertStringStartsWith()
assertStringStartsNotWith()
`assertStringStartsWith(string $prefix, string $string[, string $message = ''])`

Reports an error identified by `$message` if the `$string` does not
start with `$prefix`.

`assertStringStartsNotWith()` is the inverse of this assertion and takes
the same arguments.

    <?php
    class StringStartsWithTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertStringStartsWith('prefix', 'foo');
        }
    }
    ?>

    phpunit StringStartsWithTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) StringStartsWithTest::testFailure
    Failed asserting that 'foo' starts with "prefix".

    /home/sb/StringStartsWithTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertThat() {#appendixes.assertions.assertThat}
============

assertThat()
More complex assertions can be formulated using the
`PHPUnit_Framework_Constraint` classes. They can be evaluated using the
`assertThat()` method. ? shows how the `logicalNot()` and `equalTo()`
constraints can be used to express the same assertion as
`assertNotEquals()`.

`assertThat(mixed $value, PHPUnit_Framework_Constraint $constraint[, $message = ''])`

Reports an error identified by `$message` if the `$value` does not match
the `$constraint`.

    <?php
    class BiscuitTest extends PHPUnit_Framework_TestCase
    {
        public function testEquals()
        {
            $theBiscuit = new Biscuit('Ginger');
            $myBiscuit  = new Biscuit('Ginger');

            $this->assertThat(
              $theBiscuit,
              $this->logicalNot(
                $this->equalTo($myBiscuit)
              )
            );
        }
    }
    ?>

? shows the available `PHPUnit_Framework_Constraint` classes.

  Constraint                                                                                                      Meaning
  --------------------------------------------------------------------------------------------------------------- ---------------------------------------------------------------------------------------------------------------------------------------------------------------
  `PHPUnit_Framework_Constraint_Attribute attribute(PHPUnit_Framework_Constraint $constraint, $attributeName)`    Constraint that applies another constraint to an attribute of a class or an object.
  `PHPUnit_Framework_Constraint_IsAnything anything()`                                                            Constraint that accepts any input value.
  `PHPUnit_Framework_Constraint_ArrayHasKey arrayHasKey(mixed $key)`                                              Constraint that asserts that the array it is evaluated for has a given key.
  `PHPUnit_Framework_Constraint_TraversableContains contains(mixed $value)`                                       Constraint that asserts that the `array` or object that implements the `Iterator` interface it is evaluated for contains a given value.
  `PHPUnit_Framework_Constraint_TraversableContainsOnly containsOnly(string $type)`                               Constraint that asserts that the `array` or object that implements the `Iterator` interface it is evaluated for contains only values of a given type.
  `PHPUnit_Framework_Constraint_TraversableContainsOnly containsOnlyInstancesOf(string $classname)`               Constraint that asserts that the `array` or object that implements the `Iterator` interface it is evaluated for contains only instances of a given classname.
  `PHPUnit_Framework_Constraint_IsEqual equalTo($value, $delta = 0, $maxDepth = 10)`                              Constraint that checks if one value is equal to another.
  `PHPUnit_Framework_Constraint_Attribute attributeEqualTo($attributeName, $value, $delta = 0, $maxDepth = 10)`   Constraint that checks if a value is equal to an attribute of a class or of an object.
  `PHPUnit_Framework_Constraint_FileExists fileExists()`                                                          Constraint that checks if the file(name) that it is evaluated for exists.
  `PHPUnit_Framework_Constraint_GreaterThan greaterThan(mixed $value)`                                            Constraint that asserts that the value it is evaluated for is greater than a given value.
  `PHPUnit_Framework_Constraint_Or greaterThanOrEqual(mixed $value)`                                              Constraint that asserts that the value it is evaluated for is greater than or equal to a given value.
  `PHPUnit_Framework_Constraint_ClassHasAttribute classHasAttribute(string $attributeName)`                       Constraint that asserts that the class it is evaluated for has a given attribute.
  `PHPUnit_Framework_Constraint_ClassHasStaticAttribute classHasStaticAttribute(string $attributeName)`           Constraint that asserts that the class it is evaluated for has a given static attribute.
  `PHPUnit_Framework_Constraint_ObjectHasAttribute hasAttribute(string $attributeName)`                           Constraint that asserts that the object it is evaluated for has a given attribute.
  `PHPUnit_Framework_Constraint_IsIdentical identicalTo(mixed $value)`                                            Constraint that asserts that one value is identical to another.
  `PHPUnit_Framework_Constraint_IsFalse isFalse()`                                                                Constraint that asserts that the value it is evaluated is `FALSE`.
  `PHPUnit_Framework_Constraint_IsInstanceOf isInstanceOf(string $className)`                                     Constraint that asserts that the object it is evaluated for is an instance of a given class.
  `PHPUnit_Framework_Constraint_IsNull isNull()`                                                                  Constraint that asserts that the value it is evaluated is `NULL`.
  `PHPUnit_Framework_Constraint_IsTrue isTrue()`                                                                  Constraint that asserts that the value it is evaluated is `TRUE`.
  `PHPUnit_Framework_Constraint_IsType isType(string $type)`                                                      Constraint that asserts that the value it is evaluated for is of a specified type.
  `PHPUnit_Framework_Constraint_LessThan lessThan(mixed $value)`                                                  Constraint that asserts that the value it is evaluated for is smaller than a given value.
  `PHPUnit_Framework_Constraint_Or lessThanOrEqual(mixed $value)`                                                 Constraint that asserts that the value it is evaluated for is smaller than or equal to a given value.
  `logicalAnd()`                                                                                                  Logical AND.
  `logicalNot(PHPUnit_Framework_Constraint $constraint)`                                                          Logical NOT.
  `logicalOr()`                                                                                                   Logical OR.
  `logicalXor()`                                                                                                  Logical XOR.
  `PHPUnit_Framework_Constraint_PCREMatch matchesRegularExpression(string $pattern)`                              Constraint that asserts that the string it is evaluated for matches a regular expression.
  `PHPUnit_Framework_Constraint_StringContains stringContains(string $string, bool $case)`                        Constraint that asserts that the string it is evaluated for contains a given string.
  `PHPUnit_Framework_Constraint_StringEndsWith stringEndsWith(string $suffix)`                                    Constraint that asserts that the string it is evaluated for ends with a given suffix.
  `PHPUnit_Framework_Constraint_StringStartsWith stringStartsWith(string $prefix)`                                Constraint that asserts that the string it is evaluated for starts with a given prefix.

  : Constraints

assertTrue() {#appendixes.assertions.assertTrue}
============

assertTrue()
`assertTrue(bool $condition[, string $message = ''])`

Reports an error identified by `$message` if `$condition` is `FALSE`.

`assertNotTrue()` is the inverse of this assertion and takes the same
arguments.

    <?php
    class TrueTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertTrue(FALSE);
        }
    }
    ?>

    phpunit TrueTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) TrueTest::testFailure
    Failed asserting that false is true.

    /home/sb/TrueTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertXmlFileEqualsXmlFile() {#appendixes.assertions.assertXmlFileEqualsXmlFile}
============================

assertXmlFileEqualsXmlFile()
assertXmlFileNotEqualsXmlFile()
`assertXmlFileEqualsXmlFile(string $expectedFile, string $actualFile[, string $message = ''])`

Reports an error identified by `$message` if the XML document in
`$actualFile` is not equal to the XML document in `$expectedFile`.

`assertXmlFileNotEqualsXmlFile()` is the inverse of this assertion and
takes the same arguments.

    <?php
    class XmlFileEqualsXmlFileTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertXmlFileEqualsXmlFile(
              '/home/sb/expected.xml', '/home/sb/actual.xml');
        }
    }
    ?>

    phpunit XmlFileEqualsXmlFileTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) XmlFileEqualsXmlFileTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
     <foo>
    -  <bar/>
    +  <baz/>
     </foo>

    /home/sb/XmlFileEqualsXmlFileTest.php:7

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

assertXmlStringEqualsXmlFile() {#appendixes.assertions.assertXmlStringEqualsXmlFile}
==============================

assertXmlStringEqualsXmlFile()
assertXmlStringNotEqualsXmlFile()
`assertXmlStringEqualsXmlFile(string $expectedFile, string $actualXml[, string $message = ''])`

Reports an error identified by `$message` if the XML document in
`$actualXml` is not equal to the XML document in `$expectedFile`.

`assertXmlStringNotEqualsXmlFile()` is the inverse of this assertion and
takes the same arguments.

    <?php
    class XmlStringEqualsXmlFileTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertXmlStringEqualsXmlFile(
              '/home/sb/expected.xml', '<foo><baz/></foo>');
        }
    }
    ?>

    phpunit XmlStringEqualsXmlFileTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) XmlStringEqualsXmlFileTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
     <foo>
    -  <bar/>
    +  <baz/>
     </foo>

    /home/sb/XmlStringEqualsXmlFileTest.php:7

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

assertXmlStringEqualsXmlString() {#appendixes.assertions.assertXmlStringEqualsXmlString}
================================

assertXmlStringEqualsXmlString()
assertXmlStringNotEqualsXmlString()
`assertXmlStringEqualsXmlString(string $expectedXml, string $actualXml[, string $message = ''])`

Reports an error identified by `$message` if the XML document in
`$actualXml` is not equal to the XML document in `$expectedXml`.

`assertXmlStringNotEqualsXmlString()` is the inverse of this assertion
and takes the same arguments.

    <?php
    class XmlStringEqualsXmlStringTest extends PHPUnit_Framework_TestCase
    {
        public function testFailure()
        {
            $this->assertXmlStringEqualsXmlString(
              '<foo><bar/></foo>', '<foo><baz/></foo>');
        }
    }
    ?>

    phpunit XmlStringEqualsXmlStringTest
    PHPUnit 5.3.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) XmlStringEqualsXmlStringTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
     <foo>
    -  <bar/>
    +  <baz/>
     </foo>

    /home/sb/XmlStringEqualsXmlStringTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.
