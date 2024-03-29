[Docs](https://www.postgresql.org/docs/13/app-pg-dumpall.html)

1. Backup

```bash
1. structure only
PGPASSWORD=<pass> pg_dump -U <user> -h <host> <database_name> >> sqlfile.sql
pg_dump -h use_your_host -U use_your_username -Fc use_your_dbname > dbdump.sql

2. Structure and data
PGPASSWORD=<pass> pg_dumpall -U <user> -h <host> --database=<database_name> >> sqlfile.sql
```

2. Restore

```bash
psql -f db.out postgres
psql databasename < sqlfile.sql
PGPASSWORD=<pass> psql <database_name> -f sqlfile.sql -U <user>
# It is not important to which database you connect here since the script file created by pg_dumpall will contain the appropriate commands to create and connect to the saved databases. An exception is that if you specified --clean, you must connect to the postgres database initially; the script will attempt to drop other databases immediately, and that will fail for the database you are connected to.
```
