
 # ****DEPRECATED, NO LONGER USED****

This page is useful *only* if you are currently on Spacewalk-2.2 or earlier, and need to migrate from Oracle to Postgresql.

Starting with Spacewalk-2.3, *see [[DatabaseMigrations]]*.

# Migrating Spacewalk on Oracle installation to PostgreSQL



If you have an existing Spacewalk 1.3+ installation with Oracle backend, you can migrate it to use the PostgreSQL database server instead.
## Schema version is important



When migrating the data from database schema from Oracle to PostgreSQL, you will use a dump of the data from the Oracle database and import it to a fresh PostgreSQL database schema. Therefore, you need the PostgreSQL schema to be of the same version as the one in Oracle. In other words, you need to upgrade the Spacewalk and the Oracle schema to the latest version, to match the schema that you will likely have in PostgreSQL.

Generally, the steps at HowToUpgrade apply.
## Perform database and configuration backup



HowToUpgrade#Databaseandconfigurationbackup
## Upgrade the Spacewalk packages



HowToUpgrade#Packageupgrade
## Upgrade the Oracle schema



Make sure your Spacewalk server is down:


    # /usr/sbin/spacewalk-service status

Make sure your Oracle server is running if you have the server on the same machine as Spacewalk itself. For example, to start the Oracle XE, you can use


    # service oracle-xe start

Run *spacewalk-schema-upgrade* script to upgrade the database schema:


    # /usr/bin/spacewalk-schema-upgrade

Log files from schema upgrade are being put into `/var/log/spacewalk/schema-upgrade`.
## Make sure spacewalk-utils is installed




    # yum install spacewalk-utils
## Make an Oracle data dump




    # spacewalk-dump-schema --raw --db=xe --user=spacewalk --password=o9k2HInsl > spacewalk-oracle.dump

Make sure you have enough disk space for the dump.
### Stop Oracle



At this point, you can stop the Oracle server if it is on the same box as the Spacewalk installation. For example, for Oracle XE you would do something like


    # service oracle-xe stop
    # chkconfig oracle-xe off
### Direct dump to psql



Alternatively, you can just keep the Oracle server running and instead of redirecting the *spacewalk-dump-schema* output to a file, you can pipe it directly to *psql* in the step below.
## Install and start the PostgreSQL server



[[PostgreSQLServerSetup]]
## Install spacewalk-postgresql backend



We will want to remove spacewalk-oracle and install spacewalk-postgresql.


    # yum remove spacewalk-oracle
    [...]
    # yum install spacewalk-postgresql
## Populate the PostgreSQL database with Spacewalk schema




    # spacewalk-setup --db-only
## Load the Spacewalk data dump to PostgreSQL




    # PGPASSWORD=o9k2HInsl psql -h localhost -U spaceuser spaceschema < spacewalk-oracle.dump

Make sure you have enough disk space for the dump and for the newly populated PostgreSQL database.

If your Oracle server is still running, you can do


    # spacewalk-dump-schema --raw --db=xe --user=spacewalk --password=o9k2HInsl \
       | PGPASSWORD=o9k2HInsl psql -h localhost -U spaceuser spaceschema

Without storing the dump on disk.
## Continue with the upgrade



Continue from HowToUpgrade#UpgradeofSpacewalkconfiguration.
