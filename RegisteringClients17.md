# Registering Clients



Instructions for registering client systems you wish to manage with Spacewalk 1.7

Spacewalk 1.6 instructions are available at [[RegisteringClients16]].
## Before Starting

  1. Create a base channel within Spacewalk (Channels > Manage Software Channels > Create New Channel)

  2. Create an activation key with the new base channel. When creating a registration key do not use the generate function, create a human-readable version. eg: fedora-server-channel. This makes your installation more understandable and provides greater logical consistency to the whole system. On the other hand, if you want to prevent people from getting access to your channels, letting Spacewalk to generate random activation key name is the best way to go.
### Note about rhnreg_ks --force



rhnreg_ks is used for registration of clients to Spacewalk. If you need to re-register a client to your Spacewalk server or change registration from one environment or server to another Spacewalk server then use the "--force" flag with rhnreg_ks, otherwise there is no need to use "--force".
## Fedora



 1. Install the Spacewalk client yum repository
    * For Fedora 15:

    # rpm -Uvh http://spacewalk.redhat.com/yum/1.7/Fedora/15/x86_64/spacewalk-client-repo-1.7-5.fc15.noarch.rpm
    * For Fedora 16:

    # rpm -Uvh http://spacewalk.redhat.com/yum/1.7/Fedora/16/x86_64/spacewalk-client-repo-1.7-5.fc16.noarch.rpm
 2. Install client packages
   
    # yum install rhn-client-tools rhn-check rhn-setup rhnsd m2crypto yum-rhn-plugin
       }}}
     3. Register your Fedora system to Spacewalk using the activation key you created earlier
       {{{
    # rhnreg_ks --serverUrl=http://YourSpacewalk.example.org/XMLRPC --activationkey=<key-with-fedora-custom-channel> 
       }}}
## Red Hat Enterprise Linux 5 and 6, Scientific Linux 6, CentOS 5 or 6

    

    '''Warning:''' If you are installing these packages on a Red Hat Enterprise Linux installation it will override some of the original base packages and you may well be invalidating your support agreement with Red Hat!
    
      1. Install the Spacewalk yum repository
     * RHEL 5 / CentOS 5
       {{{
    # rpm -Uvh http://spacewalk.redhat.com/yum/1.7/RHEL/5/i386/spacewalk-client-repo-1.7-5.el5.noarch.rpm
       }}}
     * RHEL 6 / SL 6 / CentOS 6
       {{{
    # rpm -Uvh http://spacewalk.redhat.com/yum/1.7/RHEL/6/i386/spacewalk-client-repo-1.7-5.el6.noarch.rpm
       }}}
     2. The latest client tools bring the upstream development to your client boxes. That means that the packages may have dependencies that are not found in core Red Hat Enterprise Linux. These dependencies can be found in EPEL, just like for the Spacewalk server:
     * EPEL 5
       {{{
    # BASEARCH=$(uname -i)
    # rpm -Uvh http://dl.fedoraproject.org/pub/epel/5/$BASEARCH/epel-release-5-4.noarch.rpm
       }}}
     * EPEL 6
    {{{
    # BASEARCH=$(uname -i)
    # rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/$BASEARCH/epel-release-6-7.noarch.rpm
       }}}
    
      3. Install client packages
        {{{
    # yum install rhn-client-tools rhn-check rhn-setup rhnsd m2crypto yum-rhn-plugin
        }}}
      4. Register your CentOS or Red Hat Enterprise Linux system to Spacewalk using the activation key you created earlier
       {{{
    # rhnreg_ks --serverUrl=http://YourSpacewalk.example.org/XMLRPC --activationkey=<key-with-rhel-custom-channel> 
       }}}
## CentOS 4

    

    Registering a CentOS 4 server to Spacewalk is exactly the same as it would be for CentOS 5, but rhnreg_ks, rhn_check and other related scripts are located in the package '''up2date''', and not in '''rhn-setup'''.
    
      1. Enable '''spacewalk-tools''' repo for Yum and install '''up2date''' package:
    {{{
    # rpm -ivh http://stahnma.fedorapeople.org/spacewalk-tools/spacewalk-client-tools-0.0-1.noarch.rpm
    # yum install up2date 
  1. Register your CentOS system to Spacewalk using the activation key you created earlier:

    # rhnreg_ks --serverUrl=http://YourSpacewalk.example.org/XMLRPC --activationkey=<key-with-centos-custom-channel>
## SUSE



  1. Add the *spacewalk-tools* repo to get access to the tools and install them:

For openSUSE 11.4:

    #  zypper ar -f http://download.opensuse.org/repositories/systemsmanagement:/spacewalk:/1.7/openSUSE_11.4/ spacewalk-tools
    #  zypper install rhn-client-tools zypp-plugin-spacewalk rhnsd rhn-setup rhn-check yum

For openSUSE 12.1:

    #  zypper ar -f http://download.opensuse.org/repositories/systemsmanagement:/spacewalk:/1.7/openSUSE_12.1/ spacewalk-tools
    #  zypper install rhn-client-tools zypp-plugin-spacewalk rhnsd rhn-setup rhn-check yum

For Factory:

    #  zypper ar -f http://download.opensuse.org/repositories/systemsmanagement:/spacewalk:/1.7/openSUSE_Factory/ spacewalk-tools
    #  zypper install rhn-client-tools zypp-plugin-spacewalk rhnsd rhn-setup rhn-check yum

 2. Register your SUSE system to Spacewalk using the activation key you created earlier
   
    # rhnreg_ks --serverUrl=http://YourSpacewalk.example.org/XMLRPC --activationkey=<key-with-SUSE-custom-channel> 
       }}}
## Debian

    

    Add following line to file /etc/apt/sources.list:
    {{{
    deb http://miroslav.suchy.cz/spacewalk/debian spacewalk-unstable ./
*Note*: packages are not signed.

Install the packages.

    apt-get update
    apt-get install apt-spacewalk rhnsd rhnpush

If you want to run rhnsd. You have to manualy create one directory:

    mkdir /var/lock/subsys

And then register with your Spacewalk server using rhnreg_ks and activation key. In order to get dynamic repositories (as you know them with yum-rhn-plugin) you need to apply the [patch](http://isimluk.fedorapeople.org/sw_deb/apt-refresh-sources.patch) to your Apt. Eventually you can use patched version from the above repository.

    vim /etc/sysconfig/rhn/up2date
    rhnreg_ks --activationkey=1-my-debian-key

When updating the Apt cache you can watch the status of communication between Apt and Spacewalk.

    apt-get update

Finally, you rhnpush your favourite Debian packages to your Spacewalk server in order to be able to apply them using the Spacewalk WebUI.

*Note* that for Debian clients neither Spacewalk proxy, satellite-sync, repo-sync, source packages nor staging content will work.
# Solaris 8, 9 or 10



See [Solaris Support](Solaris).