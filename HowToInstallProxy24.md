# Spacewalk Proxy Installation Instructions



*These instructions are for Spacewalk Proxy 0.2 onwards*

If you're looking for instructions for the Red Hat Satellite Proxy product see the [Red Hat Satellite documentation](https://access.redhat.com/documentation/en-US/Red_Hat_Satellite/).

For more information on what Spacewalk is or for developer information see [this page](proxy).

For Spacewalk proxy installation for Spacewalk 2.3 see [[HowToInstallProxy23]]
## Prerequisites



  * Around 6GB storage per distribution under /var/spool/squid (or wherever you want your Squid cache to be)
  * Outbound open ports 80, 443, 4545 (only if you want to enable monitoring) and 5269
  * Inbound open ports 80, 443 and 5222
  * An upstream RHN Satellite server with an available Proxy entitlement or a Spacewalk server
  * Machine where you will install Spacewalk Proxy must be registered against Spacewalk Server, which you will proxy.
  * A provisioning entitlement for the Proxy server
  * Enable EPEL yum repository
## Repository



RPM downloads of the project are available through yum repositories. Set up your yum to point to Spacewalk 2.4 repositories (including *-client). For the repo setup specifics, see HowToInstall#SettingupSpacewalkrepo. 
## Installation



Ensure your machine is registered in Spacewalk and has a provisioning entitlement. Then just ask yum to install the application:


    yum install spacewalk-proxy-selinux spacewalk-proxy-installer

This will pull down and install the set of RPMs required for the installer to run. Below is an example of the set of packages pulled in:


    ===================================================================================
     Package                         Arch    Version          Repository         Size
    ===================================================================================
    Installing:
     spacewalk-proxy-installer       noarch  2.3.13-1.fc21    spacewalk          50 k
     spacewalk-proxy-selinux         noarch  2.0.1-1.fc21     spacewalk          19 k
    Installing for dependencies:
     rhncfg                          noarch  5.10.83-1.fc21   spacewalk-client   74 k
     rhncfg-actions                  noarch  5.10.83-1.fc21   spacewalk-client   47 k
     rhncfg-client                   noarch  5.10.83-1.fc21   spacewalk-client   44 k
     rhncfg-management               noarch  5.10.83-1.fc21   spacewalk-client   52 k
     rhnpush                         noarch  5.5.88-1.fc21    spacewalk          96 k
     rpm-build                       x86_64  4.12.0.1-5.fc21  updates           136 k
     spacewalk-backend               noarch  2.3.52-1.fc21    spacewalk         187 k
     spacewalk-backend-libs          noarch  2.3.52-1.fc21    spacewalk         157 k
     spacewalk-base-minimal          noarch  2.3.53-1.fc21    spacewalk          92 k
     spacewalk-base-minimal-config   noarch  2.3.53-1.fc21    spacewalk          78 k
     spacewalk-certs-tools           noarch  2.3.3-1.fc21     spacewalk          92 k
     spacewalk-proxy-broker          noarch  2.3.23-1.fc21    spacewalk          48 k
     spacewalk-proxy-common          noarch  2.3.23-1.fc21    spacewalk          66 k
     spacewalk-proxy-docs            noarch  2.3.1-1.fc21     spacewalk         4.9 M
     spacewalk-proxy-html            noarch  2.3.1-1.fc21     spacewalk          34 k
     spacewalk-proxy-management      noarch  2.3.23-1.fc21    spacewalk          26 k
     spacewalk-proxy-package-manager noarch  2.3.23-1.fc21    spacewalk          36 k
     spacewalk-proxy-redirect        noarch  2.3.23-1.fc21    spacewalk          33 k
     spacewalk-setup-jabberd         noarch  2.3.2-1.fc21     spacewalk          23 k
     spacewalk-ssl-cert-check        noarch  1:2.4-1.fc21     spacewalk          15 k


If this is the first time installing an RPM from the Spacewalk repo, yum will prompt you to install the GPG key:


    Retrieving key from http://yum.spacewalkproject.org/RPM-GPG-KEY-spacewalk-2014
    Importing GPG key 0x066E5810:
     Userid     : "Spacewalk <spacewalk-devel@redhat.com>"
     Fingerprint: 4bb1 438d 20ca af3a d230 e416 4160 5346 066e 5810
     From       : http://yum.spacewalkproject.org/RPM-GPG-KEY-spacewalk-2014
    Is this ok [y/N]: y

Then you need to configure the proxy. Run:


    configure-proxy.sh

and follow the prompts. This will download and configure the necessary packages. Once installed use /usr/sbin/rhn-proxy to stop and start the service.

You should now be able to navigate to the hostname of your proxy in a web browser and see the Spacewalk Proxy page. If you see an error here there's likely something wrong with your install.

  * If all seems well you can now try [Registering Clients](RegisteringClients)
  * If you're having problems check [Debugging Proxy](DebuggingProxy)
## Automated Installation



The configure-proxy.sh install script supports an answer file to allow you to preanswer the questions. For the full list of variables see "man configure-proxy.sh".


    configure-proxy.sh --answer-file=proxyanswers.txt

proxyanswers.txt:


    VERSION="2.4"
    RHN_PARENT="spacewalk.example.com"
    TRACEBACK_EMAIL="admin@example.com"
    USE_SSL="Y"
    CA_CHAIN="/usr/share/rhn/RHN-ORG-TRUSTED-SSL-CERT"
    HTTP_PROXY=
    SSL_CNAME_ASK=''
    SSL_ORG="Example Org"
    SSL_ORGUNIT="proxy1.example.com"
    SSL_COMMON="proxy1.example.com"
    SSL_CITY="New York"
    SSL_STATE="New York"
    SSL_COUNTRY="US"
    SSL_EMAIL="admin@example.com"
    POPULATE_CONFIG_CHANNEL="n"

As this example doesn't answer all the questions (e.g. SSL_PASSWORD) you'd still be prompted for that value. If you know that you've answered all questions then you can run with --non-interactive.