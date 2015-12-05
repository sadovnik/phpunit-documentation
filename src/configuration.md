The XML Configuration File {#appendixes.configuration}
==========================

PHPUnit {#appendixes.configuration.phpunit}
=======

The attributes of the `<phpunit>` element can be used to configure
PHPUnit's core functionality.

    <phpunit
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:noNamespaceSchemaLocation="http://schema.phpunit.de/4.5/phpunit.xsd"
             backupGlobals="true"
             backupStaticAttributes="false"
             <!--bootstrap="/path/to/bootstrap.php"-->
             cacheTokens="false"
             colors="false"
             convertErrorsToExceptions="true"
             convertNoticesToExceptions="true"
             convertWarningsToExceptions="true"
             forceCoversAnnotation="false"
             mapTestClassNameToCoveredClassName="false"
             printerClass="PHPUnit_TextUI_ResultPrinter"
             <!--printerFile="/path/to/ResultPrinter.php"-->
             processIsolation="false"
             stopOnError="false"
             stopOnFailure="false"
             stopOnIncomplete="false"
             stopOnSkipped="false"
             stopOnRisky="false"
             testSuiteLoaderClass="PHPUnit_Runner_StandardTestSuiteLoader"
             <!--testSuiteLoaderFile="/path/to/StandardTestSuiteLoader.php"-->
             timeoutForSmallTests="1"
             timeoutForMediumTests="10"
             timeoutForLargeTests="60"
             verbose="false">
      <!-- ... -->
    </phpunit>

The XML configuration above corresponds to the default behaviour of the
TextUI test runner documented in ?.

Additional options that are not available as command-line options are:

`convertErrorsToExceptions`

:   By default, PHPUnit will install an error handler that converts the
    following errors to exceptions:

    -   E\_WARNING
    -   E\_NOTICE
    -   E\_USER\_ERROR
    -   E\_USER\_WARNING
    -   E\_USER\_NOTICE
    -   E\_STRICT
    -   E\_RECOVERABLE\_ERROR
    -   E\_DEPRECATED
    -   E\_USER\_DEPRECATED

    Set `convertErrorsToExceptions` to `false` to disable this feature.

`convertNoticesToExceptions`

:   When set to `false`, the error handler installed by
    `convertErrorsToExceptions` will not convert `E_NOTICE`,
    `E_USER_NOTICE`, or `E_STRICT` errors to exceptions.

`convertWarningsToExceptions`

:   When set to `false`, the error handler installed by
    `convertErrorsToExceptions` will not convert `E_WARNING` or
    `E_USER_WARNING` errors to exceptions.

`forceCoversAnnotation`

:   Code Coverage will only be recorded for tests that use the `@covers`
    annotation documented in ?.

`timeoutForLargeTests`

:   If time limits based on test size are enforced then this attribute
    sets the timeout for all tests marked as `@large`. If a test does
    not complete within its configured timeout, it will fail.

`timeoutForMediumTests`

:   If time limits based on test size are enforced then this attribute
    sets the timeout for all tests marked as `@medium`. If a test does
    not complete within its configured timeout, it will fail.

`timeoutForSmallTests`

:   If time limits based on test size are enforced then this attribute
    sets the timeout for all tests not marked as `@medium` or `@large`.
    If a test does not complete within its configured timeout, it will
    fail.

Test Suites {#appendixes.configuration.testsuites}
===========

Test Suite The `<testsuites>` element and its one or more `<testsuite>`
children can be used to compose a test suite out of test suites and test
cases.

    <testsuites>
      <testsuite name="My Test Suite">
        <directory>/path/to/*Test.php files</directory>
        <file>/path/to/MyTest.php</file>
        <exclude>/path/to/exclude</exclude>
      </testsuite>
    </testsuites>

Using the `phpVersion` and `phpVersionOperator` attributes, a required
PHP version can be specified. The example below will only add the
`/path/to/*Test.php` files and `/path/to/MyTest.php` file if the PHP
version is at least 5.3.0.

      <testsuites>
        <testsuite name="My Test Suite">
          <directory suffix="Test.php" phpVersion="5.3.0" phpVersionOperator=">=">/path/to/files</directory>
          <file phpVersion="5.3.0" phpVersionOperator=">=">/path/to/MyTest.php</file>
        </testsuite>
      </testsuites>

The `phpVersionOperator` attribute is optional and defaults to `>=`.

Groups {#appendixes.configuration.groups}
======

Test Groups The `<groups>` element and its `<include>`, `<exclude>`, and
`<group>` children can be used to select groups of tests marked with the
`@group` annotation (documented in ?) that should (not) be run.

    <groups>
      <include>
        <group>name</group>
      </include>
      <exclude>
        <group>name</group>
      </exclude>
    </groups>

The XML configuration above corresponds to invoking the TextUI test
runner with the following options:

-   `--group name`

-   `--exclude-group name`

Whitelisting Files for Code Coverage {#appendixes.configuration.whitelisting-files}
====================================

Code Coverage Whitelist The `<filter>` element and its children can be
used to configure the whitelist for the code coverage reporting.

    <filter>
      <whitelist processUncoveredFilesFromWhitelist="true">
        <directory suffix=".php">/path/to/files</directory>
        <file>/path/to/file</file>
        <exclude>
          <directory suffix=".php">/path/to/files</directory>
          <file>/path/to/file</file>
        </exclude>
      </whitelist>
    </filter>

Logging {#appendixes.configuration.logging}
=======

Logging The `<logging>` element and its `<log>` children can be used to
configure the logging of the test execution.

    <logging>
      <log type="coverage-html" target="/tmp/report" lowUpperBound="35"
           highLowerBound="70"/>
      <log type="coverage-clover" target="/tmp/coverage.xml"/>
      <log type="coverage-php" target="/tmp/coverage.serialized"/>
      <log type="coverage-text" target="php://stdout" showUncoveredFiles="false"/>
      <log type="json" target="/tmp/logfile.json"/>
      <log type="tap" target="/tmp/logfile.tap"/>
      <log type="junit" target="/tmp/logfile.xml" logIncompleteSkipped="false"/>
      <log type="testdox-html" target="/tmp/testdox.html"/>
      <log type="testdox-text" target="/tmp/testdox.txt"/>
    </logging>

The XML configuration above corresponds to invoking the TextUI test
runner with the following options:

-   `--coverage-html /tmp/report`

-   `--coverage-clover /tmp/coverage.xml`

-   `--coverage-php /tmp/coverage.serialized`

-   `--coverage-text`

-   `--log-json /tmp/logfile.json`

-   `> /tmp/logfile.txt`

-   `--log-tap /tmp/logfile.tap`

-   `--log-junit /tmp/logfile.xml`

-   `--testdox-html /tmp/testdox.html`

-   `--testdox-text /tmp/testdox.txt`

The `lowUpperBound`, `highLowerBound`, `logIncompleteSkipped` and
`showUncoveredFiles` attributes have no equivalent TextUI test runner
option.

-   `lowUpperBound`: Maximum coverage percentage to be considered
    "lowly" covered.

-   `highLowerBound`: Minimum coverage percentage to be considered
    "highly" covered.

-   `showUncoveredFiles`: Show all whitelisted files in
    `--coverage-text` output not just the ones with coverage
    information.

-   `showOnlySummary`: Show only the summary in `--coverage-text`
    output.

Test Listeners {#appendixes.configuration.test-listeners}
==============

PHPUnit\_Framework\_TestListener Test Listener The `<listeners>` element
and its `<listener>` children can be used to attach additional test
listeners to the test execution.

    <listeners>
      <listener class="MyListener" file="/optional/path/to/MyListener.php">
        <arguments>
          <array>
            <element key="0">
              <string>Sebastian</string>
            </element>
          </array>
          <integer>22</integer>
          <string>April</string>
          <double>19.78</double>
          <null/>
          <object class="stdClass"/>
        </arguments>
      </listener>
    </listeners>

The XML configuration above corresponds to attaching the `$listener`
object (see below) to the test execution:

    $listener = new MyListener(
      array('Sebastian'),
      22,
      'April',
      19.78,
      NULL,
      new stdClass
    );

Setting PHP INI settings, Constants and Global Variables {#appendixes.configuration.php-ini-constants-variables}
========================================================

Constant Global Variable `php.ini` The `<php>` element and its children
can be used to configure PHP settings, constants, and global variables.
It can also be used to prepend the `include_path`.

    <php>
      <includePath>.</includePath>
      <ini name="foo" value="bar"/>
      <const name="foo" value="bar"/>
      <var name="foo" value="bar"/>
      <env name="foo" value="bar"/>
      <post name="foo" value="bar"/>
      <get name="foo" value="bar"/>
      <cookie name="foo" value="bar"/>
      <server name="foo" value="bar"/>
      <files name="foo" value="bar"/>
      <request name="foo" value="bar"/>
    </php>

The XML configuration above corresponds to the following PHP code:

    ini_set('foo', 'bar');
    define('foo', 'bar');
    $GLOBALS['foo'] = 'bar';
    $_ENV['foo'] = 'bar';
    $_POST['foo'] = 'bar';
    $_GET['foo'] = 'bar';
    $_COOKIE['foo'] = 'bar';
    $_SERVER['foo'] = 'bar';
    $_FILES['foo'] = 'bar';
    $_REQUEST['foo'] = 'bar';

Configuring Browsers for Selenium RC {#appendixes.configuration.selenium-rc}
====================================

Selenium RC The `<selenium>` element and its `<browser>` children can be
used to configure a list of Selenium RC servers.

    <selenium>
      <browser name="Firefox on Linux"
               browser="*firefox /usr/lib/firefox/firefox-bin"
               host="my.linux.box"
               port="4444"
               timeout="30000"/>
    </selenium>

The XML configuration above corresponds to the following PHP code:

    class WebTest extends PHPUnit_Extensions_SeleniumTestCase
    {
        public static $browsers = array(
          array(
            'name'    => 'Firefox on Linux',
            'browser' => '*firefox /usr/lib/firefox/firefox-bin',
            'host'    => 'my.linux.box',
            'port'    => 4444,
            'timeout' => 30000
          )
        );

        // ...
    }
