# PostgreSQL on Wodby

What Wodby and the image set up for this database service. Check it before creating databases, roles or extensions by hand, or adding connection settings to an application.

## Database, user and passwords

Wodby manages the database and its user; the application does not create them.

- One database and one user are created for the environment, both named after the application and the environment. The database is `UTF8` with the `en_US.utf8` locale.
- The user's password and the password of the administrator `postgres` are generated once per environment (tokens `password` and `root_password`).
- Further databases and users are added on Wodby, which runs the same create and grant actions. A role created by hand with a name Wodby later needs, but another password, makes the create action fail rather than replace it.

## Ownership inside a database

Each managed database is owned by its own role named `<database>:owner`, which cannot log in. Granting a user access makes it a member of that role, and the user's sessions in that database act as that role. What follows from it:

- Objects are created in the `public` schema and belong to the owner role, whichever user created them. All users granted access to the database share its data.
- In such a session `current_user` is the owner role; `session_user` is the user that logged in. Code that compares `current_user` with the login name sees the owner role instead.
- Only users granted access can connect to the database.
- A backup carries no role names and no grants, so it can be imported into a database with another name and other users.

## How a linked service reaches it

- Host: the name of this app service inside the environment. Port: `5432`.
- A service linked to this one receives the host, port, database name, user name and password as environment variables defined by its own link (for example `DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USERNAME`, `DB_PASSWORD` on the PHP service). Read those in the application; do not hardcode them and do not connect as `postgres` from the application.

## Extensions

Wodby creates extensions as the administrator. `POSTGRES_DB_EXTENSIONS` on this service is a comma-separated list of extensions created in a database when Wodby creates it. pgvector is bundled in the image but not enabled: add `vector` to that list. The label `pgvector` on the service means the library is present, not that the extension exists in the database.

## Server configuration

The container writes `/etc/postgresql/postgresql.conf` on every start from a template and environment variables. Two ways to change settings, neither of them editing the generated file:

- Set a variable on this service: `POSTGRES_MAX_CONNECTIONS`, `POSTGRES_SHARED_BUFFERS`, `POSTGRES_WORK_MEM`, `POSTGRES_MAINTENANCE_WORK_MEM`, `POSTGRES_EFFECTIVE_CACHE_SIZE`, `POSTGRES_MAX_WAL_SIZE`, `POSTGRES_TIMEZONE`, `POSTGRES_SHARED_PRELOAD_LIBRARIES`.
- Override the service's config file `config` ("PostgreSQL config"), which replaces the template, for a setting no variable covers.

A change applies with the next deployment of the service. The service runs a single instance.

## Data, backups and imports

- Data is on the `data` volume.
- The backup is a gzipped SQL dump of the environment's database. Tables matching its "excluded table contents" option keep their definition and lose their rows.
- The database import replaces the data volume: the dump is loaded while a new, empty data directory is initialized, and the imported objects are handed to the database's owner role. It accepts `.gz`, `.tar.gz`, `.tgz` and `.zip`. Roles a dump names that this server does not have exist only while the dump is loaded.

## Check the result

In the database container:

- `PGPASSWORD="$POSTGRES_PASSWORD" psql -h localhost -U postgres -c '\l'` lists the databases and their owner roles.
- `PGPASSWORD="$POSTGRES_PASSWORD" psql -h localhost -U postgres -d <database> -c '\dx'` lists the extensions in a database.
