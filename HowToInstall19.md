# Spacewalk Installation Instructions



These are installation instructions for new installations of Spacewalk 1.9. If you are upgrading from older versions, see HowToUpgrade.

Spacewalk 1.8 installation instructions are available at [[HowToInstall18]].

----
## Prerequisites



 * Outbound open ports 80, 443, 4545 (only if you want to enable monitoring)
 * Inbound open ports 80, 443, 5222 (only if you want to push actions to client machines) and 5269 (only for push actions to a Spacewalk Proxy), 69 udp if you want to use tftp
 * Storage for database: 250 KiB per client system +  500 KiB per channel + 230 KiB per package in channel (i.e. 1.1GiB for channel with 5000 packages)
 * Storage for packages (default /var/satellite): Depends on what you're storing; Red Hat recommend 6GB per channel for their channels
 * 2GB RAM minimum, 4GB recommended
 * Make sure your underlying OS up-to-date.
 * If you use LDAP as a central identity service and wish to pull user and group information from it, see [[SpacewalkWithLDAP]]
 * Make sure your operating system is fully up-to-date.
 * In the following steps we assume you have a default, vanilla installation of your operating system, without any customized setup of yum repositories, user management, security, etc.
## Setting up Spacewalk repo



RPM downloads of the project are available through yum repositories at

  * http://yum.spacewalkproject.org/ - Binary RPMs

To use this repository easily, install spacewalk-repo package with commands below:
### Red Hat Enterprise Linux 5, CentOS 5




    rpm -Uvh http://yum.spacewalkproject.org/1.9/RHEL/5/x86_64/spacewalk-repo-1.9-1.el5.noarch.rpm
### Red Hat Enterprise Linux 6, Scientific Linux 6, CentOS 6




    rpm -Uvh http://yum.spacewalkproject.org/1.9/RHEL/6/x86_64/spacewalk-repo-1.9-1.el6.noarch.rpm
### Fedora 17




    rpm -Uvh http://yum.spacewalkproject.org/1.9/Fedora/17/x86_64/spacewalk-repo-1.9-1.fc17.noarch.rpm
### Fedora 18




    rpm -Uvh http://yum.spacewalkproject.org/1.9/Fedora/18/x86_64/spacewalk-repo-1.9-1.fc18.noarch.rpm
### Nightly builds



If you want to use the nightly builds, install the `spacewalk-repo` package based on your operating system (see above) and then enable the nightly repository:


    sed -i 's/enabled=0/enabled=1/' /etc/yum.repos.d/spacewalk-nightly.repo
    sed -i 's/enabled=1/enabled=0/' /etc/yum.repos.d/spacewalk.repo

*NOTE:* Nigthly repo contains developers snapshot and it is not suitable for production environment. Especially beware that you might not be able to upgrade from the nightly installation to the next release, especially with respect to the database schema.
## Additional repos & packages

### Red Hat Optional Server 6




When using RHEL 6, make sure you are subscribed to following RHN channel:

  * *Red Hat Optional Server 6*
### Jpackage repository

#### Red Hat Enterprise Linux 6, Scientific Linux 6, CentOS 6 and Fedora




For Spacewalk on EL 6 and Fedora, a couple of additional dependencies are needed from jpackage.
If repo is broken, mirror list here: http://www.jpackage.org/mirroring.php
Please configure the following yum repository before beginning your Spacewalk installation:


    cat > /etc/yum.repos.d/jpackage-generic.repo << EOF
    [jpackage-generic]
    name=JPackage generic
    #baseurl=http://mirrors.dotsrc.org/pub/jpackage/5.0/generic/free/
    mirrorlist=http://www.jpackage.org/mirrorlist.php?dist=generic&type=free&release=5.0
    enabled=1
    gpgcheck=1
    gpgkey=http://www.jpackage.org/jpackage.asc
    EOF

We specifically want 5.0 generic directory in the above URL.
Make sure, you do not use the jpackage-generic repository on RHEL 5.
### EPEL repository



Spacewalk requires a Java Virtual Machine with version 1.6.0 or greater.  [EPEL - Extra Packages for Enterprise Linux](http://fedoraproject.org/wiki/EPEL) contains a version of the openjdk that works with Spacewalk.  Other dependencies can get installed from EPEL as well.
To get packages from EPEL just install this RPM:
#### EPEL 5 (use for Red Hat Enterprise Linux 5, Scientific Linux 5, CentOS 5)



    rpm -Uvh http://dl.fedoraproject.org/pub/epel/5/x86_64/epel-release-5-4.noarch.rpm
#### EPEL 6 (use for Red Hat Enterprise Linux 6, Scientific Linux 6, CentOS 6)



    rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
### CentOS 5



Follow the instructions to use *EPEL 5* with the additions:
 * Necessary packages rhn-client-tools and rhnlib were removed from CentOS, they can be found in spacewalk-client repo. Setup it by installing spacewalk-client-repo package.

    rpm -ihv http://yum.spacewalkproject.org/1.9-client/RHEL/5/x86_64/spacewalk-client-repo-1.9-1.el5.noarch.rpm
 *  Import Red Hat's RPM GPG key:

    # wget -O /etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release http://www.redhat.com/security/37017186.txt
    # rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
## Database server



Spacewalk uses database server to store its primary data. It supports either PostgreSQL (version 8.4 and higher) or Oracle RDBMS (version 10g or higher).
### PostgreSQL server, set up by Spacewalk (embedded)



You can let Spacewalk setup the PostgreSQL server on your machine without any manual intervention. Run:

    yum install spacewalk-setup-embedded-postgresql
and skip to the section *Installing Spacewalk*.
### PostgreSQL server, set up manually



If you prefer to setup the PostgreSQL manually, you have a choice to install it on the same machine as Spacewalk or different machine. Use [[PostgreSQLServerSetup]] as a guide to get the server installed and setup. Namely, you need a database and a user, the user should be a superuser and the database should have the plpgsql language created.

When using external PostgreSQL database, make sure the postgresql-contrib (or postgresql84-contrib on RHEL 5) package is installed on the database server.
### Oracle Pre-Requisites



In order to get Spacewalk to run with Oracle database backend, you need:

 * Instant Client 11.2.0.3.0 installed on the Spacewalk machine
 * Oracle database server, either on the Spacewalk machine or on another host; versions 10g and 11g are known to work

Installation of the free but limited Oracle 11g XE database server can be found on the [Oracle 11g Express Edition Setup](OracleXeSetup) page. Hints for making Spacewalk work with external Oracle database server are on the [Full Oracle Setup](FullOracleSetup) page.
## Installing Spacewalk



Just ask yum to install the necessary packages. This will pull down and install the set of RPMs required to get Spacewalk to run.

If you prefer the [[PostgreSQL]] backend, including the embedded version mentioned above:

    yum install spacewalk-postgresql 

If you tend to use the Oracle backend:

    yum install spacewalk-oracle 
## Configuring the firewall



Spacewalk needs various inbound ports to be connectible. Use `system-config-firewall` or edit `/etc/sysconfig/iptables`, adding the ports needed -- 80 and 443.
On system with `firewalld` use `firewall-cmd --add-service=http ; firewall-cmd --add-service=https`. 
Add port 5222 if you want to push actions to client machines and 5269 for push actions to a Spacewalk Proxy, 69 udp if you want to use tftp.
## Configuring Spacewalk



Your Spacewalk server should have a resolvable FQDN such as 'hostname.domain.com'.  If the installer complains that the hostname is not the FQDN, do not use the --skip-fqdn-test flag to skip !  

The setup requires that the database account has a password.

*Note:* Please don't use * '#' * (number sign/pound/hash) and * '@' * in your database password otherwise installation will fail.

Once the Spacewalk RPM is installed you need to configure the application.

If you have installed `spacewalk-setup-embedded-postgresql`, run


    spacewalk-setup --disconnected

If you've set up the database server manually (both on the same and on different machine), run


    spacewalk-setup --disconnected --external-db



An example session is as follows:


    # spacewalk-setup --disconnected --external-db
    * Setting up Oracle environment.
    * Setting up database.
    ** Database: Setting up database connection for Oracle backend.
    Database service name (SID)? XE
    Username? spacewalk
    Password? 
    ** Database: Testing database connection.
    ** Database: Populating database.
    *** Progress: ####
    * Setting up users and groups.
    ** GPG: Initializing GPG and importing key.
    ** GPG: Creating /root/.gnupg directory
    You must enter an email address.
    Admin Email Address? root@localhost
    * Performing initial configuration.
    * Activating Spacewalk.
    ** Loading Spacewalk Certificate.
    ** Verifying certificate locally.
    ** Activating Spacewalk.
    * Enabling Monitoring.
    * Configuring apache SSL virtual host.
    Should setup configure apache's default ssl server for you (saves original ssl.conf) [Y]? 
    ** /etc/httpd/conf.d/ssl.conf has been backed up to ssl.conf-swsave
    * Configuring tomcat.
    ** /etc/tomcat5/tomcat5.conf has been backed up to tomcat5.conf-swsave
    ** /etc/tomcat5/server.xml has been backed up to server.xml-swsave
    ** /etc/tomcat5/web.xml has been backed up to web.xml-swsave
    * Configuring jabberd.
    * Creating SSL certificates.
    CA certificate password? 
    Re-enter CA certificate password? 
    Organization? Fedora
    Organization Unit [spacewalk.server.com]? Spacewalk Unit
    Email Address [root@localhost]? 
    City? Brno
    State? CZ
    Country code (Examples: "US", "JP", "IN", or type "?" to see a list)? CZ
    ** SSL: Generating CA certificate.
    ** SSL: Deploying CA certificate.
    ** SSL: Generating server certificate.
    ** SSL: Storing SSL certificates.
    * Deploying configuration files.
    * Update configuration in database.
    * Setting up Cobbler..
    Cobbler requires tftp and xinetd services be turned on for PXE provisioning functionality. Enable these services [Y/n]?
    cobblerd does not appear to be running/accessible
    * Restarting services.
    Installation complete.
    Visit https://spacewalk.server.com to create the Spacewalk administrator account.

Should the spacewalk-setup fail, check the error it gives you and also investigate the logs in /var/log/rhn, as well as the logs from your database server, Apache server and tomcat.
### Configuring Spacewalk with an Answer File



You can also configure Spacewalk by using an answer file, by running `spacewalk-setup` like


    spacewalk-setup --disconnected --answer-file=<FILENAME>

An example answer file for the Oracle database backend:


    admin-email = root@localhost
    ssl-set-org = Spacewalk Org
    ssl-set-org-unit = spacewalk
    ssl-set-city = My City
    ssl-set-state = My State
    ssl-set-country = US
    ssl-password = spacewalk
    ssl-set-email = root@localhost
    ssl-config-sslvhost = Y
    db-backend=oracle
    db-user=spacewalk
    db-password=spacewalk
    db-name=xe
    db-host=localhost
    db-port=1521
    enable-tftp=Y

If you do not supply a value or leave out a key you will be prompted to supply that answer.

For PostgreSQL, you need to create something like this:


    admin-email = root@localhost
    ssl-set-org = Spacewalk Org
    ssl-set-org-unit = spacewalk
    ssl-set-city = My City
    ssl-set-state = My State
    ssl-set-country = US
    ssl-password = spacewalk
    ssl-set-email = root@localhost
    ssl-config-sslvhost = Y
    db-backend=postgresql
    db-name=spaceschema
    db-user=spaceuser
    db-password=spacepw
    db-host=host
    db-port=5432
    enable-tftp=Y

After `spacewalk-setup` is complete your application is ready to go!

Once you've made sure you can login to the Spacewalk WebUI, you can then proceed to the next step: [Uploading Content](UploadFedoraContent).
## Managing Spacewalk



Spacewalk consists of several services. Each of them has its own init.d script to stop/start/restart. If you want manage all spacewalk services at once use 

    /usr/sbin/spacewalk-service [stop|start|restart].

