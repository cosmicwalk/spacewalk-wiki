# How to upgrade operating system under installed Spacewalk



This page contains instructions for upgrading the operating system underlying your Spacewalk installation (e.g. Spacewalk 2.0 running on CentOS-5, to be upgraded to CentOS-6).
## Assumptions



* your Spacewalk is already upgraded to the version you will be using on the new operating system
* your Spacewalk is already using the desired database backend -- you will not be switching the database backend as a part of this procedure.
## Hostname change



You may decide to deploy the new operating system on a new hostname. Your instructions will then vary in details, depending on whether or not you are renaming your Spacewalk instance.
## Procedure

### 1. Create backups




Create backup of your current Spacewalk. Specifically, you need to create backup of:
* Database schema
* Content of your filer (containing packages, kickstart trees, comps), by default configured to point to _/var/satellite_ 
* Content of _/root/ssl-build/_
* Content of _~nocpulse/.ssh/_
* Content of _/var/lib/cobbler/config/profiles.d/_
* Content of _/var/lib/rhn/kickstarts/snippets/_
* Your current _/etc/rhn/rhn.conf_
### 2. Install new operating system



Install the desired operating system. You may deploy the new operating system on a machine with a hostname and IP address different from your previous Spacewalk deployment.
### 3. Install new Spacewalk instance



Install new Spacewalk instance. Keep in mind that:
* versions of your old and new Spacewalk instances have to match
* the database backends of your old and new Spacewalk instances have to match (i.e. Oracle -> Oracle, PostgreSQL -> PostgreSQL)
* you need to follow installation instructions as described in HowToInstall
### 4. Restore database schema



Restore the database schema from the backup created in step 1. After the restore completes, run the following command as root:


    $ spacewalk-schema-upgrade

The above command should finish instantly and its only purpose is to fix the release of deployed schema, to reflect the fact that the underlying operating system has changed.
### 5. Restore your filer



Restore the filer (_/var/satellite_) from backup created in step 1. After the restore completes, make sure the user and group ownerships are correct as well as SELinux context:


    $ chown -R apache.root /var/satellite
    $ restorecon -FRv /var/satellite >& /tmp/restorecon.log
### 6. Remove jabberd database



Remove jabberd database from the Spacewalk installation on your new operating system:


    $ rm -f /var/lib/jabberd/db/*
### 7. Update SSL configuration



In this step, your instructions will vary depending on whether or not you are renaming your Spacewalk (hostname) as a part of the operating system upgrade.

If you are deploying your Spacewalk on a new hostname (i.e. different from your previous deployment), run the following as root:

    $ rhn-ssl-dbstore -vvv --ca-cert=/root/ssl-build/RHN-ORG-TRUSTED-SSL-CERT

If you are keeping your original hostname, do the following steps:
  * Restore content of _/root/ssl-build_ directory from the old Satellite
  * Rebuild the _rhn-httpd-ssl-key-pair-<HOSTNAME>_ package to meet
    the standards of the latest Satellite. Change to the directory containing
    restored ssl-build directory (assuming ''/root''):

    $ cd /root

  * Rebuild the package using the following command:

    $ rhn-ssl-tool --gen-server --rpm-only
  * Install the new package (previous command will inform you of exact file
    name and its location):

    $ rpm -Uvh /root/ssl-build/<MACHINE_NAME>/rhn-org-httpd-ssl-key-pair-<VERSION>.noarch.rpm
  * Deploy the original CA certificate:

    $ rhn-deploy-ca-cert.pl \
            --source-dir=/root/ssl-build \
            --target-dir=/var/www/html/pub/
### 8. Re-build search indexes



Run the following command as root to re-build the search indexes:

    $ service rhn-search cleanindex
### 9. Restore monitoring setup



Restore SSH private and public key pair for nocpulse user from backup created in step 1:

    $ cp nocpulse-identity nocpulse-identity.pub ~nocpulse/.ssh
    $ chown nocpulse.nocpulse ~nocpulse/.ssh/nocpulse-identity*

If you wish to enable *just* monitoring, run as root:

    $ /usr/share/spacewalk/setup/upgrade/rhn-enable-monitoring.pl

If you wish to enable both monitoring and monitoring scout:

    $ /usr/share/spacewalk/setup/upgrade/rhn-enable-monitoring.pl --enable-scout
### 10. Restore Cobbler kickstart profiles and snippets



Restore the content of _/var/lib/cobbler/config/profiles.d/_ and _/var/lib/rhn/kickstarts/snippets/_ from backup created in step 1.
### 11. Re-configure your Spacewalk to use a new hostname



This step is required only if you are deploying your Spacewalk on a new hostname. If the hostname of your upgraded Spacewalk remains unchanged, skip this step.

To reconfigure your Spacewalk to use the new hostname, you will need IP address that the new hostname maps to. 

Run the following as root:

    $ spacewalk-hostname-rename <NEW-IP-ADDRESS>
### 12. Verify /etc/rhn/rhn.conf settings



Make sure any custom settings from your previous _rhn.conf_ are restored to the new _/etc/rhn/rhn.conf_.
### 13. Restart your Spacewalk



Run as root:

    $ spacewalk-service restart