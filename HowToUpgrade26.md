# Spacewalk Upgrade Instructions

These are upgrade instructions for upgrading Spacewalk 2.5 to Spacewalk 2.6

These upgrade instruction apply to Spacewalk installations meeting the following criteria:

  *  Spacewalk 2.5 running on Red Hat Enterprise Linux/CentOS/Scientific Linux 6/7 Server, or Fedora 22/23.
  *  Your Spacewalk uses one of Oracle 10g (including XE) / Oracle 11g / PostgreSQL 8.4+ as a database backend.
  *  In most cases it's possible to perform Package upgrade and Schema upgrade steps from any previous version to the latest one directly (e.g. from 1.6 to 2.6).   There are package dependency changes that must be made on Fedora 22 and RHEL 7 in transition from 2.5 to 2.6.  Make sure you have a valid backup in case anything will go wrong.
## Archive of older upgrade instructions



 * Spacewalk 2.4 to 2.5 upgrade instructions, are available at [[HowToUpgrade25]]
 * Spacewalk 2.3 to 2.4 upgrade instructions, are available at [[HowToUpgrade24]]
 * Spacewalk 2.2 to 2.3 upgrade instructions, are available at [[HowToUpgrade23]]
 * Spacewalk 2.1 to 2.2 upgrade instructions, are available at [[HowToUpgrade22]]
 * Spacewalk 2.0 to 2.1 upgrade instructions, are available at [[HowToUpgrade21]]
 * Spacewalk 1.9 to 2.0 upgrade instructions, are available at [[HowToUpgrade20]]
 * Spacewalk 1.8 to 1.9 upgrade instructions, are available at [[HowToUpgrade19]]
 * Spacewalk 1.7 to 1.8 upgrade instructions, are available at [[HowToUpgrade18]]
 * Spacewalk 1.6 to 1.7 upgrade instructions, are available at [[HowToUpgrade17]]
 * Spacewalk 1.5 to 1.6 upgrade instructions, are available at [[HowToUpgrade16]]

----
## Assumptions



  * For RHEL, CentOS, or Scientific Linux, you have the base-OS and EPEL repositories enabled.
  * For RHEL, you have the appropriate 'Optional Server' channel enabled.
  * For Fedora, your Fedora yum repositories are setup properly.
  * You have set up your yum to point to Spacewalk 2.6 repository. For the repo setup specifics, see [HowToInstall#setting-up-spacewalk-repo](https://github.com/spacewalkproject/spacewalk/wiki/HowToInstall#setting-up-spacewalk-repo).
    * In particular, make sure your jpackage repo is setup properly: [HowToInstall#jpackage-all-systems](https://github.com/spacewalkproject/spacewalk/wiki/HowToInstall#jpackage-all-systems)
## Database and configuration backup



  *  For existing configuration files, create a backup of everything under /etc/sysconfig/rhn /etc/rhn and /etc/jabberd
  *  Backup your SSL build directory, ordinarily /root/ssl-build
  *  *BACKUP YOUR DATABASE*. For instructions on how to create a backup of your existing Spacewalk database consult either Oracle / PostgreSQL documentation or contact your DBA
## Package upgrade

### Lock versions




When running on Fedora 22, you may need to lock the versions of several third-party libraries prior to upgrading, due to changes in the Fedora repositories.
Execute the following commands:

    # yum install yum-versionlock
    # yum versionlock quartz maven-model

When running on RHEL 7, you may need to lock the versions of several third-party libraries prior to upgrading, due to changes in the RHEL 7 Optional repository.
Execute the following commands:

    # yum install yum-versionlock
    # yum versionlock cglib
### All systems



Perform package upgrade using yum:

    # yum upgrade

During the upgrade, you may notice messages printed to the terminal when installing oracle-instantclient-selinux and spacewalk-selinux. These messages are produced by restorecon and do not pose any harm.

Check any `.rpmnew`/`.rpmsave` files that were created during the upgrade for your configuration files and make sure changes to your configuration files are preserved while new content from the distribution files is carried over.

    # yum install rpmconf
    # rpmconf -a
## Schema upgrade



Make sure your Spacewalk server is down:

    # /usr/sbin/spacewalk-service status
    # /usr/sbin/spacewalk-service stop

*Do a backup of your database.* (No, really - go do that.  We'll wait.)

If you are running PostgreSQL database backend check whether you have created language pltclu:

    su - postgres -c "PGPASSWORD=spacepw; createlang pltclu $(spacewalk-cfg-get db_name) ;"

If you are running Oracle database backend grant these rights to your database user:

Update spacewalk database user - replace <spacewalk> with your database user name 

    # sqlplus 'sys/<password>@//localhost/XE as sysdba' 
    SQL> grant create table to <spacewalk>; 
    SQL> grant create trigger to <spacewalk>; 
    SQL> quit 

Make sure your database server is running. Run spacewalk-schema-upgrade script to upgrade database schema:

    # /usr/bin/spacewalk-schema-upgrade

Important notes:

  * The above command will inform you whether or not the schema upgrade was successful.
    * Log files from schema upgrade are stored in `/var/log/spacewalk/schema-upgrade`.
    * Should the schema upgrade fail, investigate, *restore from backup*, fix the cause (for example, if it failed because of insufficient space in tablespace, extend the tablespaces) and rerun spacewalk-schema-upgrade.
## Upgrade of Spacewalk configuration



  1. Use `spacewalk-setup` to upgrade Spacewalk configuration. 

  If you are using the default database setup, run:

    # spacewalk-setup --upgrade

  If you are using an external database, then run

    # spacewalk-setup --external-$DB --upgrade

  where `DB` will be either `oracle` or `postgresql`, depending on what your external database is.

  2. Restore some of the custom values you might have set previously in /etc/rhn/rhn.conf from the backup of your configuration files, such as:

  *  debug = 3
  *  pam_auth_service = rhn-satellite
## Restart Spacewalk



If you are running Spacewalk on Fedora run following command before starting Spacewalk services

    systemctl daemon-reload

Then, start all Spacewalk services:

    # /usr/sbin/spacewalk-service start