- Linux: `sudo -u postgres psql`

You should see a new prompt that looks like this:

```bash
postgres=#
```

6. [ ] Create a new database. I called mine `gator`:

```sql
CREATE DATABASE macdeez;
```

7. [ ] Connect to the new database:

```sql
\c gator
```

You should see a new prompt that looks like this:

```bash
gator=#
```

8. [ ] Set the user password (Linux only)

```sql
ALTER USER postgres PASSWORD 'postgres';
```

For simplicity, I used `postgres` as the password. Before, we altered the _system_ user's password, now we're altering the _database_ user's password.

9. [ ] Query the database

From here you can run SQL queries against the `gator` database. For example, to see the version of Postgres you're running, you can run:

```sql
SELECT version();
```

If everything is working, you can move on. _You can type `exit` to leave the `psql` shell._

## Goose

	# Goose Migrations

[Goose](https://github.com/pressly/goose) is a database migration tool written in Go. It runs migrations from a set of SQL files, making it a perfect fit for this project (we wanna stay close to the raw SQL).

## What is a migration?

A migration is just a set of changes to your database table. You can have as many migrations as needed as your requirements change over time. For example, one migration might create a new table, one might delete a column, and one might add 2 new columns.

An "up" migration moves the state of the database from its current schema to the schema that you want. So, to get a "blank" database to the state it needs to be ready to run your application, you run all the "up" migrations.

If something breaks, you can run one of the "down" migrations to revert the database to a previous state. "Down" migrations are also used if you need to reset a local testing database to a known state.

## Assignment

1. [ ] Install Goose

Goose is just a command line tool that happens to be written in Go. I recommend [installing](https://github.com/pressly/goose#install) it using `go install`:

```bash
go install github.com/pressly/goose/v3/cmd/goose@latest
```

Run `goose -version` to make sure it's installed correctly.

2. [ ] Create a `users` migration in a new `sql/schema` directory.

A "migration" in Goose is just a `.sql` file with some SQL queries and some special comments. Our first migration should just create a `users` table. The simplest format for these files is:

```
number_name.sql
```

For example, I created a file in `sql/schema` called `001_users.sql` with the following contents:

```sql
-- +goose Up
CREATE TABLE ...

-- +goose Down
DROP TABLE users;
```

Write out the `CREATE TABLE` statement in full, I left it blank for you to fill in. A `user` should have 4 fields:

- `id`: a [`UUID`](https://en.wikipedia.org/wiki/Universally_unique_identifier) that will serve as the _primary key_
- `created_at`: a `TIMESTAMP` that can _not be null_
- `updated_at`: a `TIMESTAMP` that can _not be null_
- `name`: a _unique_ string that can _not be null_

The `-- +goose Up` and `-- +goose Down` comments are case sensitive and required. They tell Goose how to run the migration in each direction.

3. [ ] Get your connection string. A connection string is just a URL with all of the information needed to connect to a database. The format is:

```
protocol://username:password@host:port/database
```

Here are examples:

- Mac OS (no password, your username): `postgres://wagslane:@localhost:5432/gator`
- Linux (password from last lesson, postgres user): `postgres://postgres:postgres@localhost:5432/gator`

Test your connection string by running `psql`, for example:

```bash
psql "postgres://wagslane:@localhost:5432/gator"
```

It should connect you to the `gator` database directly. If it's working, great. `exit` out of `psql` and save the connection string.

4. [ ] Run the up migration.

`cd` into the `sql/schema` directory and run:

```bash
goose postgres <connection_string> up
```

Run your migration! Make sure it works by using `psql` to find your newly created `users` table:

```bash
psql gator
\dt
```

5. Run the down migration to make sure it works (it should just drop the table). When you're satisifed, run the up migration again to recreate the table.
    
6. Add the connection string to the `.gatorconfig.json` file instead of the example string we used earlier. When using it with `goose`, you'll use it in the format we just used. However, here in the config file it needs an additional `sslmode=disable` query string:
    

```
protocol://username:password@host:port/database?sslmode=disable
```

Your application code needs to know to not try to use SSL locally.

# SQLC
# 

[SQLC](https://sqlc.dev/) is an _amazing_ Go program that generates Go code from SQL queries. It's not exactly an [ORM](https://www.freecodecamp.org/news/what-is-an-orm-the-meaning-of-object-relational-mapping-database-tools/), but rather a tool that makes working with raw SQL easy and type-safe.

We will use Goose to manage our database migrations (the schema), and SQLC to generate Go code that our application can use to interact with the database (run queries).

## Assignment

1. [ ] Install SQLC.

SQLC is just a command line tool, it's not a package that we need to import. I recommend [installing](https://docs.sqlc.dev/en/latest/overview/install.html) it using `go install`. Installing Go CLI tools with `go install` is easy and ensures compatibility with your Go environment.

```bash
go install github.com/sqlc-dev/sqlc/cmd/sqlc@latest
```

Then run `sqlc version` to make sure it's installed correctly.

2. [ ] Configure [SQLC](https://docs.sqlc.dev/en/latest/tutorials/getting-started-postgresql.html). You'll always run the `sqlc` command from the root of your project. Create a file called `sqlc.yaml` in the root of your project. Here is mine:

```yaml
version: "2"
sql:
  - schema: "sql/schema"
    queries: "sql/queries"
    engine: "postgresql"
    gen:
      go:
        out: "internal/database"
```

We're telling SQLC to look in the `sql/schema` directory for our schema structure (which is the same set of files that Goose uses, but `sqlc` automatically ignores "down" migrations), and in the `sql/queries` directory for queries. We're also telling it to generate Go code in the `internal/database` directory.

3. [ ] Write a query to create a user. Inside the `sql/queries` directory, create a file called `users.sql`. Here's the format:

```sql
-- name: CreateUser :one
INSERT INTO users (id, created_at, updated_at, name)
VALUES (
    $1,
    $2,
    $3,
    $4
)
RETURNING *;
```

`$1`, `$2`, `$3`, and `$4` are parameters that we'll be able to pass into the query in our Go code. The `:one` at the end of the query name tells SQLC that we expect to get back a single row (the created user).

Keep the [SQLC postgres docs](https://docs.sqlc.dev/en/latest/tutorials/getting-started-postgresql.html) handy, you'll probably need to refer to them again later.

4. [ ] Generate the Go code. Run `sqlc generate` from the root of your project. It should create a new package of go code in `internal/database`. You'll notice that the generated code relies on Google's [uuid](https://pkg.go.dev/github.com/google/uuid) package, so you'll need to add that to your module:

```bash
go get github.com/google/uuid
```

5. Write another query to get a user by name. I used the comment `-- name: GetUser :one` to tell SQLC that I expect to get back a single row. Again, generate the Go code to ensure that it works.
    
6. Import a PostgreSQL driver.
    

We need to add and import a [Postgres driver](https://github.com/lib/pq) so our program knows how to talk to the database. Install it in your module:

```bash
go get github.com/lib/pq
```

Add this import to the top of your `main.go` file:

```go
import _ "github.com/lib/pq"
```

In `main()`, load in your database URL to the config struct and [sql.Open()](https://pkg.go.dev/database/sql#Open) a connection to your database:

```go
db, err := sql.Open("postgres", dbURL)
```

Use your generated `database` package to create a new `*database.Queries`, and store it in your `state` struct:

```go
dbQueries := database.New(db)
```

```go
type state struct {
	db  *database.Queries
	cfg *config.Config
}
```