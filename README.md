# NAME

Test::DB - Temporary Testing Databases

# ABSTRACT

Temporary Databases for Testing

# SYNOPSIS

    use Test::DB;

    my $tdb = Test::DB->new;

    # my $tdbo = $tdb->create(database => 'sqlite');

    # my $dbh = $tdbo->dbh;

# DESCRIPTION

This package provides a framework for setting up and tearing down temporary
databases for testing purposes. This framework requires a user (optionally with
password) which has the ability to create new databases and works by creating
test-specific databases owned by the user specified. **Note:** Test databases
are not automatically destroyed and should be cleaned up manually by call the
`destroy` method on the database-specific test database object.

## process

**on create, clone**

- #1

    Establish a connection to the DB using some "initial" database.

        my $tdbo = $tdb->postgres(initial => 'template0');

- #2

    Using the established connection, create the test/temporary database.

        $tdbo->create;

- #3

    Establish a connection to the newly created test/temporary database.

        $tdbo->create->dbh;

- #4

    Make the test database object immutable.

        $tdbo->create->database('example'); # error

**on destroy**

- #1

    Establish a connection to the DB using the "initial" database.

        # using the created test/temporary database object

- #2

    Using the established connection, drop the test/temporary database.

        $tdbo->destroy;

## usages

**using DBI**

- #1

        my $tdb = Test::DB->new;
        my $dbh = $tdb->sqlite(%options)->dbh;

**using DBIx::Class**

- #1

        my $tdb = Test::DB->new;
        my $tdbo = $tdb->postgres(%options)->create;
        my $schema = DBIx::Class::Schema->connect(
          dsn => $tdbo->dsn,
          username => $tdbo->username,
          password => $tdbo->password,
        );

**using Mojo::mysql**

- #1

        my $tdb = Test::DB->new;
        my $tdbo = $tdb->mysql(%options)->create;
        my $mysql = Mojo::mysql->new($tdbo->uri);

**using Mojo::Pg**

- #1

        my $tdb = Test::DB->new;
        my $tdbo = $tdb->postgres(%options)->create;
        my $postgres = Mojo::Pg->new($tdbo->uri);

**using Mojo::Pg (with cloning)**

- #1

        my $tdb = Test::DB->new;
        my $tdbo = $tdb->postgres(%options)->clone('template0');
        my $postgres = Mojo::Pg->new($tdbo->uri);

**using Mojo::SQLite**

- #1

        my $tdb = Test::DB->new;
        my $tdbo = $tdb->sqlite(%options)->create;
        my $sqlite = Mojo::SQLite->new($tdbo->uri);

# LIBRARIES

This package uses type constraints from:

[Types::Standard](https://metacpan.org/pod/Types%3A%3AStandard)

# METHODS

This package implements the following methods:

## clone

    clone(Str :$database, Str %options) : Maybe[InstanceOf["Test::DB::Object"]]

The clone method generates a database based on the type and database template
specified and returns a `Test::DB::Object` with an active connection, `dbh`
and `dsn`. If the database specified doesn't have a corresponding database
driver this method will returned the undefined value. The type of database can
be omitted if the `TESTDB_DATABASE` environment variable is set, if not the
type of database must be either `sqlite`, `mysql`, `mssql` or `postgres`.
Any options provided are passed along to the test database object class
constructor.

- clone example #1

        # given: synopsis

        $ENV{TESTDB_DATABASE} = 'postgres';

        $tdb->clone(template => 'template0');

- clone example #2

        # given: synopsis

        $ENV{TESTDB_DATABASE} = 'postgres';
        $ENV{TESTDB_TEMPLATE} = 'template0';

        $tdb->clone;

- clone example #3

        # given: synopsis

        $ENV{TESTDB_TEMPLATE} = 'template0';

        $tdb->clone(database => 'postgres');

## create

    create(Str :$database, Str %options) : Maybe[InstanceOf["Test::DB::Object"]]

The create method generates a database based on the type specified and returns
a `Test::DB::Object` with an active connection, `dbh` and `dsn`. If the
database specified doesn't have a corresponding database driver this method
will returned the undefined value. The type of database can be omitted if the
`TESTDB_DATABASE` environment variable is set, if not the type of database
must be either `sqlite`, `mysql`, `mssql` or `postgres`. Any options
provided are passed along to the test database object class constructor.

- create example #1

        # given: synopsis

        $tdb->create;

- create example #2

        # given: synopsis

        $ENV{TESTDB_DATABASE} = 'sqlite';

        $tdb->create;

- create example #3

        # given: synopsis

        $tdb->create(database => 'sqlite');

## mssql

    mssql(Str %options) : Maybe[InstanceOf["Test::DB::Object"]]

The mssql method builds and returns a [Test::DB::Mssql](https://metacpan.org/pod/Test%3A%3ADB%3A%3AMssql) object.

- mssql example #1

        # given: synopsis

        $tdb->mssql;

## mysql

    mysql(Str %options) : Maybe[InstanceOf["Test::DB::Object"]]

The mysql method builds and returns a [Test::DB::Mysql](https://metacpan.org/pod/Test%3A%3ADB%3A%3AMysql) object.

- mysql example #1

        # given: synopsis

        $tdb->mysql;

## postgres

    postgres(Str %options) : Maybe[InstanceOf["Test::DB::Object"]]

The postgres method builds and returns a [Test::DB::Postgres](https://metacpan.org/pod/Test%3A%3ADB%3A%3APostgres) object.

- postgres example #1

        # given: synopsis

        $tdb->postgres;

## sqlite

    sqlite(Str %options) : Maybe[InstanceOf["Test::DB::Object"]]

The sqlite method builds and returns a [Test::DB::Sqlite](https://metacpan.org/pod/Test%3A%3ADB%3A%3ASqlite) object.

- sqlite example #1

        # given: synopsis

        $tdb->sqlite;

# AUTHOR

Al Newkirk, `awncorp@cpan.org`

# LICENSE

Copyright (C) 2011-2019, Al Newkirk, et al.

This is free software; you can redistribute it and/or modify it under the terms
of the The Apache License, Version 2.0, as elucidated in the ["license
file"](https://github.com/iamalnewkirk/test-db/blob/master/LICENSE).

# PROJECT

[Wiki](https://github.com/iamalnewkirk/test-db/wiki)

[Project](https://github.com/iamalnewkirk/test-db)

[Initiatives](https://github.com/iamalnewkirk/test-db/projects)

[Milestones](https://github.com/iamalnewkirk/test-db/milestones)

[Contributing](https://github.com/iamalnewkirk/test-db/blob/master/CONTRIBUTE.md)

[Issues](https://github.com/iamalnewkirk/test-db/issues)
