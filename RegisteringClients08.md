# Registering Clients



Instructions for registering client systems you wish to manage with Spacewalk

These are instruction for Spacewalk 0.8. 

Spacewalk 0.7 instructions are available at [[RegisteringClients07]]

----
# Before Starting

  1. Create a base channel within Spacewalk (Channels > Manage Software Channels > Create New Channel)

  2. Create an activation key with the new base channel.


    #!html
    <div style="background-color:#8e9f00; color:white; background-image:url('/spacewalk/attachment/wiki/WikiSnippetsAndTemplates/note.png?format=raw'); background-repeat:no-repeat; padding-left:7em;background-position:1em;" >
       <h2>Note</h2>
       <div style="padding-bottom:0.5em;" >
           <p>rhnreg_ks is used for registration of clients to Spacewalk. If you need to re-register a client to your Spacewalk server or change registration from one environment or server to another Spacewalk server then use the "--force" flag with rhnreg_ks, otherwise there is no need to use "--force".</p>
       </div>
    </div>


    #!html
    <div style="background-color:#8e9f00; color:white; background-image:url('/spacewalk/attachment/wiki/WikiSnippetsAndTemplates/note.png?format=raw'); background-repeat:no-repeat; padding-left:7em;background-position:1em;" >
       <h2>Note</h2>
       <div style="padding-bottom:0.5em;" >
           <p>When creating a registration key do not use the generate function, create a human-readable version. eg: fedora-server-channel. This makes your installation more understandable and provides greater logical consistency to the whole system.</p>
       </div>
    </div>
# Fedora 11



 1. Install the Spacewalk client yum repository
   
    # rpm -Uvh http://spacewalk.redhat.com/yum/0.8/Fedora/11/$(uname -i)/spacewalk-client-repo-0.8-1.fc11.noarch.rpm
       }}}
     1. Install client packages
       {{{
    # yum install rhn-client-tools rhn-check rhn-setup rhnsd m2crypto yum-rhn-plugin
       }}}
     1. Register your Fedora system to Spacewalk using the activation key you created earlier
       {{{
    # rhnreg_ks --serverUrl=http://YourSpacewalk.example.org/XMLRPC --activationkey=<key-with-fedora-custom-channel> 
       }}}
# Fedora 12

    

      1. Install the Spacewalk yum repository
       {{{
    # rpm -Uvh http://spacewalk.redhat.com/yum/0.8/Fedora/12/$(uname -i)/spacewalk-client-repo-0.8-1.fc12.noarch.rpm
       }}}
      1. Install client packages
        {{{
    # yum install rhn-client-tools rhn-check rhn-setup rhnsd m2crypto yum-rhn-plugin
        }}}
      1. Register your Fedora system to Spacewalk using the activation key you created earlier
       {{{
    # rhnreg_ks --serverUrl=http://YourSpacewalk.example.org/XMLRPC --activationkey=<key-with-fedora-custom-channel> 
       }}}
# CentOS 5 or Red Hat Enterprise Linux 5

    

    {{{
    #!html
    <div style="background-color:#9e292b; color:white; background-image:url('/spacewalk/attachment/wiki/WikiSnippetsAndTemplates/warning.png?format=raw'); background-repeat:no-repeat; padding-left:7em;background-position:1em;" >
       <h2>Warning</h2>
       <div style="padding-bottom:0.5em;" >
          <p>If you are installing these packages on a Red Hat Enterprise Linux installation it will override some of the original base packages and you may well be invalidating your support agreement with Redhat!</p>
       </div>
    </div>

  1. Install the Spacewalk yum repository
   
    # BASEARCH=$(uname -i)
    # rpm -Uvh http://spacewalk.redhat.com/yum/0.8/RHEL/5/$BASEARCH/spacewalk-client-repo-0.8-1.el5.noarch.rpm
       }}}
      1. For Red Hat Enterprise Linux 5.4 and older you have to install python-dmidecode from EPEL
       {{{
    # rpm -Uvh http://download.fedora.redhat.com/pub/epel/5/$BASEARCH/python-dmidecode-3.10.7-3.el5.$BASEARCH.rpm
       }}}
      1. Install client packages
        {{{
    # yum install rhn-client-tools rhn-check rhn-setup rhnsd m2crypto yum-rhn-plugin
        }}}
      1. Register your CentOS or Red Hat Enterprise Linux system to Spacewalk using the activation key you created earlier
       {{{
    # rhnreg_ks --serverUrl=http://YourSpacewalk.example.org/XMLRPC --activationkey=<key-with-rhel-custom-channel> 
       }}}
# CentOS 4

    

    Registering a CentOS 4 server to Spacewalk is exactly the same as it would be for CentOS 5, but rhnreg_ks, rhn_check and other related scripts are located in the package '''up2date''', and not in '''rhn-setup'''.
    
      1. Enable '''spacewalk-tools''' repo for Yum and install '''up2date''' package:
    {{{
    # rpm -ivh http://stahnma.fedorapeople.org/spacewalk-tools/spacewalk-client-tools-0.0-1.noarch.rpm
    # yum install up2date 
  1. Register your CentOS system to Spacewalk using the activation key you created earlier:

    # rhnreg_ks --serverUrl=http://YourSpacewalk.example.org/XMLRPC --activationkey=<key-with-centos-custom-channel>
# Solaris 8, 9 or 10



See [Solaris Support](Solaris).