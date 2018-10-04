# DBT Tutorial

## Load the DVD rental Database to Local Postgres

Log into your local postgresql server with psql.
```bash
$ psql
```

create a database we'll use for the project, and create a dbt user.

TODO: figure out how to do this without giving dbt superuser permissions.

```psql
# create database dvd_rental
# \c dvd_rental
# create user dbt with password 'password123'
# alter user dbt with superuser
# \q
```

Download the sample dvd database from [this link](http://www.postgresqltutorial.com/postgresql-sample-database/) and extract the zip file.

Load the data into your new database like so:

```bash
$ pg_restore -U postgres -d dvd_rental ~/Downloads/dvdrental
```

## Install DBT and Create a Project

After setting up a [virtualenv](https://virtualenvwrapper.readthedocs.io/en/latest/), install dbt and create a project:

```bash
$ pip install dbt
$ dbt init dvd-rentals
```

Save your username and password into a .env file and load that into your shell (or use something like [direnv](https://direnv.net/))

```bash
$ cd dvd-rentals
$ echo -e  "USERNAME=dbt\nPASSWORD=password123" > .env
$ cat .env
$ source .env
```

Create a `profiles.yml` file in this directory (i.e., inside of the `dvd-rentals` folder), with the following text:

```
config:
    use_colors: True 
default:
  outputs:
    dev:
      type: postgres
      threads: 1
      host: localhost
      port: 5432
      user: "{{ env_var('USERNAME') }}"
      pass: "{{ env_var('PASSWORD') }}"
      dbname: dvd_rental
      schema: dev
    prod:
      type: postgres
      threads: 1
      host: localhost
      port: 5432
      user: "{{ env_var('USERNAME') }}"
      pass: "{{ env_var('PASSWORD') }}"
      dbname: dvd_rental
      schema: analytics
  target: dev
```

## Test it out!

If the gods are kind, you should be able to run dbt.

```bash
$ dbt run --profiles-dir .
```

And see your first relation!
```
$ psql -d dvd_rental
# select * from dev.my_first_dbt_model ;
```

