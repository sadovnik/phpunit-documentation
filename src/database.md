Database Testing {#database}
================

Many beginner and intermediate unit testing examples in any programming
language suggest that it is perfectly easy to test your application's
logic with simple tests. For database-centric applications this is far
away from the reality. Start using Wordpress, TYPO3 or Symfony with
Doctrine or Propel, for example, and you will easily experience
considerable problems with PHPUnit: just because the database is so
tightly coupled to these libraries.

> **Note**
>
> Make sure you have the PHP extension `pdo` and database specific
> extensions such as `pdo_mysql` installed. Otherwise the examples shown
> below will not work.

You probably know this scenario from your daily work and projects, where
you want to put your fresh or experienced PHPUnit skills to work and get
stuck by one of the following problems:

1.  The method you want to test executes a rather large JOIN operation
    and uses the data to calculate some important results.

2.  Your business logic performs a mix of SELECT, INSERT, UPDATE and
    DELETE statements.

3.  You need to setup test data in (possibly much) more than two tables
    to get reasonable initial data for the methods you want to test.

The DbUnit extension considerably simplifies the setup of a database for
testing purposes and allows you to verify the contents of a database
after performing a series of operations.

Supported Vendors for Database Testing {#database.supported-vendors-for-database-testing}
======================================

DbUnit currently supports MySQL, PostgreSQL, Oracle and SQLite. Through
[Zend Framework](http://framework.zend.com) or [Doctrine
2](http://www.doctrine-project.org) integrations it has access to other
database systems such as IBM DB2 or Microsoft SQL Server.

Difficulties in Database Testing {#database.difficulties-in-database-testing}
================================

There is a good reason why all the examples on unit testing do not
include interactions with the database: these kind of tests are both
complex to setup and maintain. While testing against your database you
need to take care of the following variables:

-   The database schema and tables

-   Inserting the rows required for the test into these tables

-   Verifying the state of the database after your test has run

-   Cleanup the database for each new test

Because many database APIs such as PDO, MySQLi or OCI8 are cumbersome to
use and verbose in writing doing these steps manually is an absolute
nightmare.

Test code should be as short and precise as possible for several
reasons:

-   You do not want to modify considerable amount of test code for
    little changes in your production code.

-   You want to be able to read and understand the test code easily,
    even months after writing it.

Additionally you have to realize that the database is essentially a
global input variable to your code. Two tests in your test suite could
run against the same database, possibly reusing data multiple times.
Failures in one test can easily affect the result of the following tests
making your testing experience very difficult. The previously mentioned
cleanup step is of major importance to solve the “database is a global
input” problem.

DbUnit helps to simplify all these problems with database testing in an
elegant way.

What PHPUnit cannot help you with is the fact that database tests are
very slow compared to tests not using the database. Depending on how
large the interactions with your database are your tests could run a
considerable amount of time. However, if you keep the amount of data
used for each test small and try to test as much code using non-database
tests you can easily get away in under a minute even for large test
suites.

The [Doctrine 2 project](http://www.doctrine-project.org)'s test suite,
for example, currently has a test suite of about 1000 tests where nearly
half of them accesses the database and still runs in 15 seconds against
a MySQL database on a standard desktop computer.

The four stages of a database test {#database.the-four-stages-of-a-database-test}
==================================

In his book on xUnit Test Patterns Gerard Meszaros lists the four stages
of a unit-test:

1.  Set up fixture

2.  Exercise System Under Test

3.  Verify outcome

4.  Teardown

> **What is a Fixture?**
>
> A fixture describes the initial state your application and database
> are in when you execute a test.

Testing the database requires you to hook into at least the setup and
teardown to clean-up and write the required fixture data into your
tables. However, the database extension has good reason to revert the
four stages in a database test to resemble the following workflow that
is executed for each single test:

1. Clean-Up Database {#database.clean-up-database}
--------------------

Since there is always a first test that runs against the database you do
not know exactly if there is already data in the tables. PHPUnit will
execute a TRUNCATE against all the tables you specified to reset their
status to empty.

2. Set up fixture {#database.set-up-fixture}
-----------------

PHPUnit will then iterate over all the fixture rows specified and insert
them into their respective tables.

3–5. Run Test, Verify outcome and Teardown {#database.run-test-verify-outcome-and-teardown}
------------------------------------------

After the database is reset and loaded with its initial state the actual
test is executed by PHPUnit. This part of the test code does not require
awareness of the Database Extension at all, you can go on and test
whatever you like with your code.

In your test use a special assertion called `assertDataSetsEqual()` for
verification purposes, however, this is entirely optional. This feature
will be explained in the section “Database Assertions”.

Configuration of a PHPUnit Database TestCase {#database.configuration-of-a-phpunit-database-testcase}
============================================

Usually when using PHPUnit your testcases would extend the
`PHPUnit_Framework_TestCase` class in the following way:

    <?php
    class MyTest extends PHPUnit_Framework_TestCase
    {
        public function testCalculate()
        {
            $this->assertEquals(2, 1 + 1);
        }
    }
    ?>

If you want to test code that works with the Database Extension the
setup is a bit more complex and you have to extend a different abstract
TestCase requiring you to implement two abstract methods
`getConnection()` and `getDataSet()`:

    <?php
    class MyGuestbookTest extends PHPUnit_Extensions_Database_TestCase
    {
        /**
         * @return PHPUnit_Extensions_Database_DB_IDatabaseConnection
         */
        public function getConnection()
        {
            $pdo = new PDO('sqlite::memory:');
            return $this->createDefaultDBConnection($pdo, ':memory:');
        }

        /**
         * @return PHPUnit_Extensions_Database_DataSet_IDataSet
         */
        public function getDataSet()
        {
            return $this->createFlatXMLDataSet(dirname(__FILE__).'/_files/guestbook-seed.xml');
        }
    }
    ?>

Implementing getConnection() {#database.implementing-getconnection}
----------------------------

To allow the clean-up and fixture loading functionalities to work the
PHPUnit Database Extension requires access to a database connection
abstracted across vendors through the PDO library. It is important to
note that your application does not need to be based on PDO to use
PHPUnit's database extension, the connection is merely used for the
clean-up and fixture setup.

In the previous example we create an in-memory Sqlite connection and
pass it to the `createDefaultDBConnection` method which wraps the PDO
instance and the second parameter (the database-name) in a very simple
abstraction layer for database connections of the type
`PHPUnit_Extensions_Database_DB_IDatabaseConnection`.

The section “Using the Database Connection” explains the API of this
interface and how you can make the best use of it.

Implementing getDataSet() {#database.implementing-getdataset}
-------------------------

The `getDataSet()` method defines how the initial state of the database
should look before each test is executed. The state of a database is
abstracted through the concepts DataSet and DataTable both being
represented by the interfaces
`PHPUnit_Extensions_Database_DataSet_IDataSet` and
`PHPUnit_Extensions_Database_DataSet_IDataTable`. The next section will
describe in detail how these concepts work and what the benefits are for
using them in database testing.

For the implementation we only need to know that the `getDataSet()`
method is called once during `setUp()` to retrieve the fixture data-set
and insert it into the database. In the example we are using a factory
method `createFlatXMLDataSet($filename)` that represents a data-set
through an XML representation.

What about the Database Schema (DDL)? {#database.what-about-the-database-schema-ddl}
-------------------------------------

PHPUnit assumes that the database schema with all its tables, triggers,
sequences and views is created before a test is run. This means you as
developer have to make sure that the database is correctly setup before
running the suite.

There are several means to achieve this pre-condition to database
testing.

1.  If you are using a persistent database (not Sqlite Memory) you can
    easily setup the database once with tools such as phpMyAdmin for
    MySQL and re-use the database for every test-run.

2.  If you are using libraries such as [Doctrine
    2](http://www.doctrine-project.org) or
    [Propel](http://www.propelorm.org/) you can use their APIs to create
    the database schema you need once before you run the tests. You can
    utilize [PHPUnit's Bootstrap and Configuration](textui.html)
    capabilities to execute this code whenever your tests are run.

Tip: Use your own Abstract Database TestCase {#database.tip-use-your-own-abstract-database-testcase}
--------------------------------------------

From the previous implementation example you can easily see that
`getConnection()` method is pretty static and could be re-used in
different database test-cases. Additionally to keep performance of your
tests good and database overhead low you can refactor the code a little
bit to get a generic abstract test case for your application, which
still allows you to specify a different data-fixture for each test case:

    <?php
    abstract class MyApp_Tests_DatabaseTestCase extends PHPUnit_Extensions_Database_TestCase
    {
        // only instantiate pdo once for test clean-up/fixture load
        static private $pdo = null;

        // only instantiate PHPUnit_Extensions_Database_DB_IDatabaseConnection once per test
        private $conn = null;

        final public function getConnection()
        {
            if ($this->conn === null) {
                if (self::$pdo == null) {
                    self::$pdo = new PDO('sqlite::memory:');
                }
                $this->conn = $this->createDefaultDBConnection(self::$pdo, ':memory:');
            }

            return $this->conn;
        }
    }
    ?>

This has the database connection hardcoded in the PDO connection though.
PHPUnit has another awesome feature that could make this testcase even
more generic. If you use the [XML
Configuration](appendixes.configuration.html#appendixes.configuration.php-ini-constants-variables)
you could make the database connection configurable per test-run. First
let's create a “phpunit.xml” file in our tests/ directory of the
application that looks like:

    <?xml version="1.0" encoding="UTF-8" ?>
    <phpunit>
        <php>
            <var name="DB_DSN" value="mysql:dbname=myguestbook;host=localhost" />
            <var name="DB_USER" value="user" />
            <var name="DB_PASSWD" value="passwd" />
            <var name="DB_DBNAME" value="myguestbook" />
        </php>
    </phpunit>

We can now modify our test-case to look like:

    <?php
    abstract class Generic_Tests_DatabaseTestCase extends PHPUnit_Extensions_Database_TestCase
    {
        // only instantiate pdo once for test clean-up/fixture load
        static private $pdo = null;

        // only instantiate PHPUnit_Extensions_Database_DB_IDatabaseConnection once per test
        private $conn = null;

        final public function getConnection()
        {
            if ($this->conn === null) {
                if (self::$pdo == null) {
                    self::$pdo = new PDO( $GLOBALS['DB_DSN'], $GLOBALS['DB_USER'], $GLOBALS['DB_PASSWD'] );
                }
                $this->conn = $this->createDefaultDBConnection(self::$pdo, $GLOBALS['DB_DBNAME']);
            }

            return $this->conn;
        }
    }
    ?>

We can now run the database test suite using different configurations
from the command-line interface:

    user@desktop> phpunit --configuration developer-a.xml MyTests/
    user@desktop> phpunit --configuration developer-b.xml MyTests/

The possibility to run the database tests against different database
targets easily is very important if you are developing on the
development machine. If several developers run the database tests
against the same database connection you can easily experience
test-failures because of race-conditions.

Understanding DataSets and DataTables {#database.understanding-datasets-and-datatables}
=====================================

A central concept of PHPUnit's Database Extension are DataSets and
DataTables. You should try to understand this simple concept to master
database testing with PHPUnit. The DataSet and DataTable are an
abstraction layer around your database tables, rows and columns. A
simple API hides the underlying database contents in an object
structure, which can also be implemented by other non-database sources.

This abstraction is necessary to compare the actual contents of a
database against the expected contents. Expectations can be represented
as XML, YAML, CSV files or PHP array for example. The DataSet and
DataTable interfaces enable the comparison of these conceptually
different sources, emulating relational database storage in a
semantically similar approach.

A workflow for database assertions in your tests then consists of three
simple steps:

-   Specify one or more tables in your database by table name (actual
    dataset)

-   Specify the expected dataset in your preferred format (YAML, XML,
    ..)

-   Assert that both dataset representations equal each other.

Assertions are not the only use-case for the DataSet and DataTable in
PHPUnit's Database Extension. As shown in the previous section they also
describe the initial contents of a database. You are forced to define a
fixture dataset by the Database TestCase, which is then used to:

-   Delete all the rows from the tables specified in the dataset.

-   Write all the rows in the data-tables into the database.

Available Implementations {#database.available-implementations}
-------------------------

There are three different types of datasets/datatables:

-   File-Based DataSets and DataTables

-   Query-Based DataSet and DataTable

-   Filter and Composition DataSets and DataTables

The file-based datasets and tables are generally used for the initial
fixture and to describe the expected state of the database.

### Flat XML DataSet {#database.flat-xml-dataset}

The most common dataset is called Flat XML. It is a very simple xml
format where a tag inside the root node `<dataset>` represents exactly
one row in the database. The tags name equals the table to insert the
row into and an attribute represents the column. An example for a simple
guestbook application could look like this:

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="I like it!" user="nancy" created="2010-04-26 12:14:20" />
    </dataset>

This is obviously easy to write. Here `<guestbook>` is the table name
where two rows are inserted into each with four columns “id”, “content”,
“user” and “created” with their respective values.

However, this simplicity comes at a cost.

From the previous example it isn't obvious how you would specify an
empty table. You can insert a tag with no attributes with the name of
the empty table. A flat xml file for an empty guestbook table would then
look like:

    <?xml version="1.0" ?>
    <dataset>
        <guestbook />
    </dataset>

The handling of NULL values with the flat xml dataset is tedious. A NULL
value is different than an empty string value in almost any database
(Oracle being an exception), something that is difficult to describe in
the flat xml format. You can represent a NULL's value by omitting the
attribute from the row specification. If our guestbook would allow
anonymous entries represented by a NULL value in the user column, a
hypothetical state of the guestbook table could look like:

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="I like it!" created="2010-04-26 12:14:20" />
    </dataset>

In this case the second entry is posted anonymously. However, this leads
to a serious problem with column recognition. During dataset equality
assertions each dataset has to specify what columns a table holds. If an
attribute is NULL for all the rows of a data-table, how would the
Database Extension know that the column should be part of the table?

The flat xml dataset makes a crucial assumption now, defining that the
attributes on the first defined row of a table define the columns of
this table. In the previous example this would mean “id”, “content”,
“user” and “created” are columns of the guestbook table. For the second
row where “user” is not defined a NULL would be inserted into the
database.

When the first guestbook entry is deleted from the dataset only “id”,
“content” and “created” would be columns of the guestbook table, since
“user” is not specified.

To use the Flat XML dataset effectively when NULL values are relevant
the first row of each table must not contain any NULL value and only
successive rows are allowed to omit attributes. This can be awkward,
since the order of the rows is a relevant factor for database
assertions.

In turn, if you specify only a subset of the table columns in the Flat
XML dataset all the omitted values are set to their default values. This
will lead to errors if one of the omitted columns is defined as “NOT
NULL DEFAULT NULL”.

In conclusion I can only advise using the Flat XML datasets if you do
not need NULL values.

You can create a flat xml dataset instance from within your Database
TestCase by calling the `createFlatXmlDataSet($filename)` method:

    <?php
    class MyTestCase extends PHPUnit_Extensions_Database_TestCase
    {
        public function getDataSet()
        {
            return $this->createFlatXmlDataSet('myFlatXmlFixture.xml');
        }
    }
    ?>

### XML DataSet {#database.xml-dataset}

There is another more structured XML dataset, which is a bit more
verbose to write but avoids the NULL problems of the Flat XML dataset.
Inside the root node `<dataset>` you can specify `<table>`, `<column>`,
`<row>`, `<value>` and `<null />` tags. An equivalent dataset to the
previously defined Guestbook Flat XML looks like:

    <?xml version="1.0" ?>
    <dataset>
        <table name="guestbook">
            <column>id</column>
            <column>content</column>
            <column>user</column>
            <column>created</column>
            <row>
                <value>1</value>
                <value>Hello buddy!</value>
                <value>joe</value>
                <value>2010-04-24 17:15:23</value>
            </row>
            <row>
                <value>2</value>
                <value>I like it!</value>
                <null />
                <value>2010-04-26 12:14:20</value>
            </row>
        </table>
    </dataset>

Any defined `<table>` has a name and requires a definition of all the
columns with their names. It can contain zero or any positive number of
nested `<row>` elements. Defining no `<row>` element means the table is
empty. The `<value>` and `<null />` tags have to be specified in the
order of the previously given `<column>` elements. The `<null />` tag
obviously means that the value is NULL.

You can create a xml dataset instance from within your Database TestCase
by calling the `createXmlDataSet($filename)` method:

    <?php
    class MyTestCase extends PHPUnit_Extensions_Database_TestCase
    {
        public function getDataSet()
        {
            return $this->createXMLDataSet('myXmlFixture.xml');
        }
    }
    ?>

### MySQL XML DataSet {#database.mysql-xml-dataset}

This new XML format is specific to the [MySQL database
server](http://www.mysql.com). Support for it was added in PHPUnit 3.5.
Files in this format can be generated using the
[`mysqldump`](http://dev.mysql.com/doc/refman/5.0/en/mysqldump.html)
utility. Unlike CSV datasets, which `mysqldump` also supports, a single
file in this XML format can contain data for multiple tables. You can
create a file in this format by invoking `mysqldump` like so:

    mysqldump --xml -t -u [username] --password=[password] [database] > /path/to/file.xml

This file can be used in your Database TestCase by calling the
`createMySQLXMLDataSet($filename)` method:

    <?php
    class MyTestCase extends PHPUnit_Extensions_Database_TestCase
    {
        public function getDataSet()
        {
            return $this->createMySQLXMLDataSet('/path/to/file.xml');
        }
    }
    ?>

### YAML DataSet {#database.yaml-dataset}

Alternatively, you can use YAML dataset for the guestbook example:

    guestbook:
      -
        id: 1
        content: "Hello buddy!"
        user: "joe"
        created: 2010-04-24 17:15:23
      -
        id: 2
        content: "I like it!"
        user:
        created: 2010-04-26 12:14:20

This is simple, convient AND it solves the NULL issue that the similar
Flat XML dataset has. A NULL in YAML is just the column name without no
value specified. An empty string is specified as `column1: ""`.

The YAML Dataset has no factory method on the Database TestCase
currently, so you have to instantiate it manually:

    <?php
    class YamlGuestbookTest extends PHPUnit_Extensions_Database_TestCase
    {
        protected function getDataSet()
        {
            return new PHPUnit_Extensions_Database_DataSet_YamlDataSet(
                dirname(__FILE__)."/_files/guestbook.yml"
            );
        }
    }
    ?>

### CSV DataSet {#database.csv-dataset}

Another file-based dataset is based on CSV files. Each table of the
dataset is represented as a single CSV file. For our guestbook example
we would define a guestbook-table.csv file:

    id,content,user,created
    1,"Hello buddy!","joe","2010-04-24 17:15:23"
    2,"I like it!","nancy","2010-04-26 12:14:20"

While this is very convenient for editing with Excel or OpenOffice, you
cannot specify NULL values with the CSV dataset. An empty column will
lead to the database default empty value being inserted into the column.

You can create a CSV DataSet by calling:

    <?php
    class CsvGuestbookTest extends PHPUnit_Extensions_Database_TestCase
    {
        protected function getDataSet()
        {
            $dataSet = new PHPUnit_Extensions_Database_DataSet_CsvDataSet();
            $dataSet->addTable('guestbook', dirname(__FILE__)."/_files/guestbook.csv");
            return $dataSet;
        }
    }
    ?>

### Array DataSet {#database.array-dataset}

There is no Array based DataSet in PHPUnit's Database Extension (yet),
but we can implement our own easily. Our guestbook example should look
like:

    <?php
    class ArrayGuestbookTest extends PHPUnit_Extensions_Database_TestCase
    {
        protected function getDataSet()
        {
            return new MyApp_DbUnit_ArrayDataSet(array(
                'guestbook' => array(
                    array('id' => 1, 'content' => 'Hello buddy!', 'user' => 'joe', 'created' => '2010-04-24 17:15:23'),
                    array('id' => 2, 'content' => 'I like it!',   'user' => null,  'created' => '2010-04-26 12:14:20'),
                ),
            ));
        }
    }
    ?>

A PHP DataSet has obvious advantages over all the other file-based
datasets:

-   PHP Arrays can obviously handle `NULL` values.

-   You won't need additional files for assertions and can specify them
    directly in the TestCase.

For this dataset like the Flat XML, CSV and YAML DataSets the keys of
the first specified row define the table's column names, in the previous
case this would be “id”, “content”, “user” and “created”.

The implementation for this Array DataSet is simple and straightforward:

    <?php
    class MyApp_DbUnit_ArrayDataSet extends PHPUnit_Extensions_Database_DataSet_AbstractDataSet
    {
        /**
         * @var array
         */
        protected $tables = array();

        /**
         * @param array $data
         */
        public function __construct(array $data)
        {
            foreach ($data AS $tableName => $rows) {
                $columns = array();
                if (isset($rows[0])) {
                    $columns = array_keys($rows[0]);
                }

                $metaData = new PHPUnit_Extensions_Database_DataSet_DefaultTableMetaData($tableName, $columns);
                $table = new PHPUnit_Extensions_Database_DataSet_DefaultTable($metaData);

                foreach ($rows AS $row) {
                    $table->addRow($row);
                }
                $this->tables[$tableName] = $table;
            }
        }

        protected function createIterator($reverse = FALSE)
        {
            return new PHPUnit_Extensions_Database_DataSet_DefaultTableIterator($this->tables, $reverse);
        }

        public function getTable($tableName)
        {
            if (!isset($this->tables[$tableName])) {
                throw new InvalidArgumentException("$tableName is not a table in the current database.");
            }

            return $this->tables[$tableName];
        }
    }
    ?>

### Query (SQL) DataSet {#database.query-sql-dataset}

For database assertions you do not only need the file-based datasets but
also a Query/SQL based Dataset that contains the actual contents of the
database. This is where the Query DataSet shines:

    <?php
    $ds = new PHPUnit_Extensions_Database_DataSet_QueryDataSet($this->getConnection());
    $ds->addTable('guestbook');
    ?>

Adding a table just by name is an implicit way to define the data-table
with the following query:

    <?php
    $ds = new PHPUnit_Extensions_Database_DataSet_QueryDataSet($this->getConnection());
    $ds->addTable('guestbook', 'SELECT * FROM guestbook');
    ?>

You can make use of this by specifying arbitrary queries for your
tables, for example restricting rows, column or adding `ORDER BY`
clauses:

    <?php
    $ds = new PHPUnit_Extensions_Database_DataSet_QueryDataSet($this->getConnection());
    $ds->addTable('guestbook', 'SELECT id, content FROM guestbook ORDER BY created DESC');
    ?>

The section on Database Assertions will show some more details on how to
make use of the Query DataSet.

### Database (DB) Dataset {#database.database-db-dataset}

Accessing the Test Connection you can automatically create a DataSet
that consists of all the tables with their content in the database
specified as second parameter to the Connections Factory method.

You can either create a dataset for the complete database as shown in
`testGuestbook()`, or restrict it to a set of specified table names with
a whitelist as shown in `testFilteredGuestbook()` method.

    <?php
    class MySqlGuestbookTest extends PHPUnit_Extensions_Database_TestCase
    {
        /**
         * @return PHPUnit_Extensions_Database_DB_IDatabaseConnection
         */
        public function getConnection()
        {
            $database = 'my_database';
            $user = 'my_user';
            $password = 'my_password';
            $pdo = new PDO('mysql:...', $user, $password);
            return $this->createDefaultDBConnection($pdo, $database);
        }

        public function testGuestbook()
        {
            $dataSet = $this->getConnection()->createDataSet();
            // ...
        }

        public function testFilteredGuestbook()
        {
            $tableNames = array('guestbook');
            $dataSet = $this->getConnection()->createDataSet($tableNames);
            // ...
        }
    }
    ?>

### Replacement DataSet {#database.replacement-dataset}

I have been talking about NULL problems with the Flat XML and CSV
DataSet, but there is a slightly complicated workaround to get both
types of datasets working with NULLs.

The Replacement DataSet is a decorator for an existing dataset and
allows you to replace values in any column of the dataset by another
replacement value. To get our guestbook example working with NULL values
we specify the file like:

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="I like it!" user="##NULL##" created="2010-04-26 12:14:20" />
    </dataset>

We then wrap the Flat XML DataSet into a Replacement DataSet:

    <?php
    class ReplacementTest extends PHPUnit_Extensions_Database_TestCase
    {
        public function getDataSet()
        {
            $ds = $this->createFlatXmlDataSet('myFlatXmlFixture.xml');
            $rds = new PHPUnit_Extensions_Database_DataSet_ReplacementDataSet($ds);
            $rds->addFullReplacement('##NULL##', null);
            return $rds;
        }
    }
    ?>

### DataSet Filter {#database.dataset-filter}

If you have a large fixture file you can use the DataSet Filter for
white- and blacklisting of tables and columns that should be contained
in a sub-dataset. This is especially handy in combination with the DB
DataSet to filter the columns of the datasets.

    <?php
    class DataSetFilterTest extends PHPUnit_Extensions_Database_TestCase
    {
        public function testIncludeFilteredGuestbook()
        {
            $tableNames = array('guestbook');
            $dataSet = $this->getConnection()->createDataSet();

            $filterDataSet = new PHPUnit_Extensions_Database_DataSet_DataSetFilter($dataSet);
            $filterDataSet->addIncludeTables(array('guestbook'));
            $filterDataSet->setIncludeColumnsForTable('guestbook', array('id', 'content'));
            // ..
        }

        public function testExcludeFilteredGuestbook()
        {
            $tableNames = array('guestbook');
            $dataSet = $this->getConnection()->createDataSet();

            $filterDataSet = new PHPUnit_Extensions_Database_DataSet_DataSetFilter($dataSet);
            $filterDataSet->addExcludeTables(array('foo', 'bar', 'baz')); // only keep the guestbook table!
            $filterDataSet->setExcludeColumnsForTable('guestbook', array('user', 'created'));
            // ..
        }
    }
    ?>

> **NOTE** You cannot use both exclude and include column filtering on
> the same table, only on different ones. Plus it is only possible to
> either white- or blacklist tables, not both of them.

### Composite DataSet {#database.composite-dataset}

The composite DataSet is very useful for aggregating several already
existing datasets into a single dataset. When several datasets contain
the same table the rows are appended in the specified order. For example
if we have two datasets *fixture1.xml*:

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
    </dataset>

and *fixture2.xml*:

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="2" content="I like it!" user="##NULL##" created="2010-04-26 12:14:20" />
    </dataset>

Using the Composite DataSet we can aggregate both fixture files:

    <?php
    class CompositeTest extends PHPUnit_Extensions_Database_TestCase
    {
        public function getDataSet()
        {
            $ds1 = $this->createFlatXmlDataSet('fixture1.xml');
            $ds2 = $this->createFlatXmlDataSet('fixture2.xml');

            $compositeDs = new PHPUnit_Extensions_Database_DataSet_CompositeDataSet();
            $compositeDs->addDataSet($ds1);
            $compositeDs->addDataSet($ds2);

            return $compositeDs;
        }
    }
    ?>

Beware of Foreign Keys {#database.beware-of-foreign-keys}
----------------------

During Fixture SetUp PHPUnit's Database Extension inserts the rows into
the database in the order they are specified in your fixture. If your
database schema uses foreign keys this means you have to specify the
tables in an order that does not cause foreign key constraints to fail.

Implementing your own DataSets/DataTables {#database.implementing-your-own-datasetsdatatables}
-----------------------------------------

To understand the internals of DataSets and DataTables, lets have a look
at the interface of a DataSet. You can skip this part if you do not plan
to implement your own DataSet or DataTable.

    <?php
    interface PHPUnit_Extensions_Database_DataSet_IDataSet extends IteratorAggregate
    {
        public function getTableNames();
        public function getTableMetaData($tableName);
        public function getTable($tableName);
        public function assertEquals(PHPUnit_Extensions_Database_DataSet_IDataSet $other);

        public function getReverseIterator();
    }
    ?>

The public interface is used internally by the `assertDataSetsEqual()`
assertion on the Database TestCase to check for dataset quality. From
the `IteratorAggregate` interface the IDataSet inherits the
`getIterator()` method to iterate over all tables of the dataset. The
reverse iterator allows PHPUnit to truncate tables opposite the order
they were created to satisfy foreign key constraints.

Depending on the implementation different approaches are taken to add
table instances to a dataset. For example, tables are added internally
during construction from the source file in all file-based datasets such
as `YamlDataSet`, `XmlDataSet` or `FlatXmlDataSet`.

A table is also represented by the following interface:

    <?php
    interface PHPUnit_Extensions_Database_DataSet_ITable
    {
        public function getTableMetaData();
        public function getRowCount();
        public function getValue($row, $column);
        public function getRow($row);
        public function assertEquals(PHPUnit_Extensions_Database_DataSet_ITable $other);
    }
    ?>

Except the `getTableMetaData()` method it is pretty self-explainatory.
The used methods are all required for the different assertions of the
Database Extension that are explained in the next chapter. The
`getTableMetaData()` method has to return an implementation of the
`PHPUnit_Extensions_Database_DataSet_ITableMetaData` interface, which
describes the structure of the table. It holds information on:

-   The table name

-   An array of column-names of the table, ordered by their appearance
    in the result-set.

-   An array of the primary-key columns.

This interface also has an assertion that checks if two instances of
Table Metadata equal each other, which is used by the data-set equality
assertion.

The Connection API {#database.the-connection-api}
==================

There are three interesting methods on the Connection interface which
has to be returned from the `getConnection()` method on the Database
TestCase:

    <?php
    interface PHPUnit_Extensions_Database_DB_IDatabaseConnection
    {
        public function createDataSet(Array $tableNames = NULL);
        public function createQueryTable($resultName, $sql);
        public function getRowCount($tableName, $whereClause = NULL);

        // ...
    }
    ?>

1.  The `createDataSet()` method creates a Database (DB) DataSet as
    described in the DataSet implementations section.

        <?php
        class ConnectionTest extends PHPUnit_Extensions_Database_TestCase
        {
            public function testCreateDataSet()
            {
                $tableNames = array('guestbook');
                $dataSet = $this->getConnection()->createDataSet();
            }
        }
        ?>

2.  The `createQueryTable()` method can be used to create instances of a
    QueryTable, give them a result name and SQL query. This is a handy
    method when it comes to result/table assertions as will be shown in
    the next section on the Database Assertions API.

        <?php
        class ConnectionTest extends PHPUnit_Extensions_Database_TestCase
        {
            public function testCreateQueryTable()
            {
                $tableNames = array('guestbook');
                $queryTable = $this->getConnection()->createQueryTable('guestbook', 'SELECT * FROM guestbook');
            }
        }
        ?>

3.  The `getRowCount()` method is a convienent way to access the number
    of rows in a table, optionally filtered by an additional where
    clause. This can be used with a simple equality assertion:

        <?php
        class ConnectionTest extends PHPUnit_Extensions_Database_TestCase
        {
            public function testGetRowCount()
            {
                $this->assertEquals(2, $this->getConnection()->getRowCount('guestbook'));
            }
        }
        ?>

Database Assertions API {#database.database-assertions-api}
=======================

For a testing tool the Database Extension surely provides some
assertions that you can use to verify the current state of the database,
tables and the row-count of tables. This section describes this
functionality in detail:

Asserting the Row-Count of a Table {#database.asserting-the-row-count-of-a-table}
----------------------------------

It is often helpful to check if a table contains a specific amount of
rows. You can easily achieve this without additional glue code using the
Connection API. Say we wanted to check that after insertion of a row
into our guestbook we not only have the two initial entries that have
accompanied us in all the previous examples, but a third one:

    <?php
    class GuestbookTest extends PHPUnit_Extensions_Database_TestCase
    {
        public function testAddEntry()
        {
            $this->assertEquals(2, $this->getConnection()->getRowCount('guestbook'), "Pre-Condition");

            $guestbook = new Guestbook();
            $guestbook->addEntry("suzy", "Hello world!");

            $this->assertEquals(3, $this->getConnection()->getRowCount('guestbook'), "Inserting failed");
        }
    }
    ?>

Asserting the State of a Table {#database.asserting-the-state-of-a-table}
------------------------------

The previous assertion is helpful, but we surely want to check the
actual contents of the table to verify that all the values were written
into the correct columns. This can be achieved by a table assertion.

For this we would define a Query Table instance which derives its
content from a table name and SQL query and compare it to a File/Array
Based Data Set:

    <?php
    class GuestbookTest extends PHPUnit_Extensions_Database_TestCase
    {
        public function testAddEntry()
        {
            $guestbook = new Guestbook();
            $guestbook->addEntry("suzy", "Hello world!");

            $queryTable = $this->getConnection()->createQueryTable(
                'guestbook', 'SELECT * FROM guestbook'
            );
            $expectedTable = $this->createFlatXmlDataSet("expectedBook.xml")
                                  ->getTable("guestbook");
            $this->assertTablesEqual($expectedTable, $queryTable);
        }
    }
    ?>

Now we have to write the *expectedBook.xml* Flat XML file for this
assertion:

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="I like it!" user="nancy" created="2010-04-26 12:14:20" />
        <guestbook id="3" content="Hello world!" user="suzy" created="2010-05-01 21:47:08" />
    </dataset>

This assertion would only pass on exactly one second of the universe
though, on *2010–05–01 21:47:08*. Dates pose a special problem to
database testing and we can circumvent the failure by omitting the
“created” column from the assertion.

The adjusted *expectedBook.xml* Flat XML file would probably have to
look like the following to make the assertion pass:

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" />
        <guestbook id="2" content="I like it!" user="nancy" />
        <guestbook id="3" content="Hello world!" user="suzy" />
    </dataset>

We have to fix up the Query Table call:

    <?php
    $queryTable = $this->getConnection()->createQueryTable(
        'guestbook', 'SELECT id, content, user FROM guestbook'
    );
    ?>

Asserting the Result of a Query {#database.asserting-the-result-of-a-query}
-------------------------------

You can also assert the result of complex queries with the Query Table
approach, just specify a result name with a query and compare it to a
dataset:

    <?php
    class ComplexQueryTest extends PHPUnit_Extensions_Database_TestCase
    {
        public function testComplexQuery()
        {
            $queryTable = $this->getConnection()->createQueryTable(
                'myComplexQuery', 'SELECT complexQuery...'
            );
            $expectedTable = $this->createFlatXmlDataSet("complexQueryAssertion.xml")
                                  ->getTable("myComplexQuery");
            $this->assertTablesEqual($expectedTable, $queryTable);
        }
    }
    ?>

Asserting the State of Multiple Tables {#database.asserting-the-state-of-multiple-tables}
--------------------------------------

For sure you can assert the state of multiple tables at once and compare
a query dataset against a file based dataset. There are two different
ways for DataSet assertions.

1.  You can use the Database (DB) DataSet from the Connection and
    compare it to a File-Based DataSet.

        <?php
        class DataSetAssertionsTest extends PHPUnit_Extensions_Database_TestCase
        {
            public function testCreateDataSetAssertion()
            {
                $dataSet = $this->getConnection()->createDataSet(array('guestbook'));
                $expectedDataSet = $this->createFlatXmlDataSet('guestbook.xml');
                $this->assertDataSetsEqual($expectedDataSet, $dataSet);
            }
        }
        ?>

2.  You can construct the DataSet on your own:

        <?php
        class DataSetAssertionsTest extends PHPUnit_Extensions_Database_TestCase
        {
            public function testManualDataSetAssertion()
            {
                $dataSet = new PHPUnit_Extensions_Database_DataSet_QueryDataSet();
                $dataSet->addTable('guestbook', 'SELECT id, content, user FROM guestbook'); // additional tables
                $expectedDataSet = $this->createFlatXmlDataSet('guestbook.xml');

                $this->assertDataSetsEqual($expectedDataSet, $dataSet);
            }
        }
        ?>

Frequently Asked Questions {#database.frequently-asked-questions}
==========================

Will PHPUnit (re-)create the database schema for each test? {#database.will-phpunit-re-create-the-database-schema-for-each-test}
-----------------------------------------------------------

No, PHPUnit requires all database objects to be available when the suite
is started. The Database, tables, sequences, triggers and views have to
be created before you run the test suite.

[Doctrine 2](http://www.doctrine-project.org) or [eZ
Components](http://www.ezcomponents.org) have powerful tools that allow
you to create the database schema from pre-defined datastructures.
However, these have to be hooked into the PHPUnit extension to allow an
automatic database re-creation before the complete test-suite is run.

Since each test completely cleans the database you are not even required
to re-create the database for each test-run. A permanently available
database works perfectly.

Am I required to use PDO in my application for the Database Extension to work? {#database.am-i-required-to-use-pdo-in-my-application-for-the-database-extension-to-work}
------------------------------------------------------------------------------

No, PDO is only required for the fixture clean- and set-up and for
assertions. You can use whatever database abstraction you want inside
your own code.

What can I do, when I get a “Too much Connections” Error? {#database.what-can-i-do-when-i-get-a-too-much-connections-error}
---------------------------------------------------------

If you do not cache the PDO instance that is created from the TestCase
`getConnection()` method the number of connections to the database is
increasing by one or more with each database test. With default
configuration MySql only allows 100 concurrent connections other vendors
also have maximum connection limits.

The SubSection “Use your own Abstract Database TestCase” shows how you
can prevent this error from happening by using a single cached PDO
instance in all your tests.

How to handle NULL with Flat XML / CSV Datasets? {#database.how-to-handle-null-with-flat-xml-csv-datasets}
------------------------------------------------

Do not do this. Instead, you should use either the XML or the YAML
DataSets.
