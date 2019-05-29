# Spacewalk and IPA integration



New with Spacewalk 2.1, it is possible to set up the Spacewalk server WebUI authentication to use external identity providers and management solutions, for example [FreeIPA](http://www.freeipa.org/) or [Identity Management](https://access.redhat.com/site/documentation/en-US/Red_Hat_Enterprise_Linux/6/html-single/Identity_Management_Guide/). The new feature extends the original per-user PAM authentication, and includes support for:

* Kerberos authentication in the WebUI (tickets);
* Users do not need to be pre-created in Spacewalk database;
* The PAM authentication can be enabled for all users;
* User roles can be derived from user group membership in the external identity provider.
* System Groups administrators can be derived from user group membership in the external identity provider per Organization.

From technical point of view, both the Kerberos authentication and the PAM authentication take place in the Apache HTTP server, not in the Spacewalk application. That makes it possible to extend this authentication mechanism to other authentication providers. Here we will focus on getting it up and running with FreeIPA.
## Enrolling Spacewalk to identity provider



In this document, we assume that there is a FreeIPA server installed and running and that the Spacewalk server is IPA-enrolled to it. The enrolement can be achieved using the `ipa-client-install` command:


    yum install /usr/sbin/ipa-client-install -y
    # Use FreeIPA server autodiscovery
    ipa-client-install

If we want to enroll the Spacewalk server directly to a particular FreeIPA server to a particular domain, we can point the `resolv.conf` to that server and specify the domain:


    cp /etc/resolv.conf /etc/resolv.conf.orig
    echo nameserver IP-address-of-the-FreeIPA-server > /etc/resolv.conf
    ipa-client-install --domain example.com


Create the Spacewalk HTTP service on the FreeIPA server:


    kinit admin
    ipa service-add HTTP/hostname-of-the-Spacewalk-server

We can also run this directly on the Spacewalk server if the `ipa` command is installed there:


    yum install /usr/bin/ipa -y
    kinit admin
    ipa service-add HTTP/$( hostname )


If you use

    rpm -q spacewalk-setup
spacewalk-setup-2.3.1-1 or newer, you may use [spacewalk-setup-ipa-authentication script]() to 
configure pam, sssd, apache and tomcat for you. Otherwise, you need to follow the [manual steps]().
## Automatic setup using the spacewalk-setup-ipa-authentication script
 #automatic-setup


Run the spacewalk-setup-ipa-authentication script as root:

    $ spacewalk-setup-ipa-authentication 
    Enabling authentication against [ipa.example.com].
    Retrieving HTTP/ service keytab into [/etc/httpd/conf/http.keytab] ...
    Keytab successfully retrieved and stored in: /etc/httpd/conf/http.keytab
    changed ownership of `/etc/httpd/conf/http.keytab' to apache
    Configuring PAM service [spacewalk].
    Will install additional packages ...
    <installation of mod_auth_kerb, mod_authnz_pam, mod_intercept_form_submit, mod_lookup_identity, sssd-dbus>
    ** /etc/sssd/sssd.conf has been backed up to sssd.conf-swsave
    Updated sssd configuration.
    Turning SELinux boolean [httpd_dbus_sssd] on ...
            ... done.
    Turning SELinux boolean [allow_httpd_mod_auth_pam] on ...
            ... done.
    Configuring Apache modules.
    ** /etc/tomcat6/server.xml has been backed up to server.xml-swsave.ipa
    Stopping sssd: [  OK  ]
    Starting sssd: [  OK  ]
    Stopping tomcat6: [  OK  ]
    Starting tomcat6: [  OK  ]
    Stopping httpd: [  OK  ]
    Starting httpd: httpd: [  OK  ]
    Waiting for tomcat to be ready ...
    Authentication against [ipa.example.com] sucessfully enabled.
    As admin, at Admin > Users > External Authentication, select
              Default organization to autopopulate new users into.

Your Spacewalk services were configured to work with the IPA server.
Continue with [Host based access control prerequisites]()
## Manual setup
 #manual-setup

### Keytab retrieval



For the Kerberos authentication to work, we need to retrieve the keytab of the Spacewalk HTTP service on the Spacewalk server:


    kinit admin
    ipa-getkeytab -s $( awk '/^server/ { print $3 }' /etc/ipa/default.conf ) -k /etc/http.keytab -p HTTP/$( hostname )
    chown apache /etc/http.keytab
    chmod 600 /etc/http.keytab
### Configure PAM



On the Spacewalk server, we define the `spacewalk-prod` PAM service and point it to sssd:


    # /etc/pam.d/spacewalk-prod content:
    auth    required   pam_sss.so
    account required   pam_sss.so
### Configure sssd to provide information about auto-populated users



When a user authenticates either via Kerberos ticket or via form-based PAM authentication, it might be the first time that user logged in to the Spacewalk server. Spacewalk needs to create record about that user in its internal database and for that, certain additional attributes are needed besides the obvious login name. The sssd-dbus package provides new *ifp* sssd service.
#### RHEL7.0/CentOS7.0 only

  If you're on RHEL7.0/CentOS7.0, you need to configure following repository:


   https://copr.fedorainfracloud.org/coprs/adelton/identity_demo/

  Retrieve the `.repo` file and put it to `/etc/yum.repos.d`:


    curl -o /etc/yum.repos.d/adelton-identity_demo.repo https://copr.fedorainfracloud.org/coprs/adelton/identity_demo/repo/epel-6-x86_64/

For other OSs (RHEL6 & Fedora) sssd-dbus and other needed packages are available from their repositories.

Install sssd-dbus:


    yum install sssd-dbus -y

The sssd needs to be configured to enable ifp and retrieval and caching of attributes needed by Spacewalk:


    # /etc/sssd/sssd.conf, the [domain/...] section, add:
    ldap_user_extra_attrs = mail, givenname, sn
    # /etc/sssd/sssd.conf, the [sssd] section, amend the services line to include ifp:
    services = nss, pam, ssh, ifp
    # /etc/sssd/sssd.conf, add new [ifp] section:
    [ifp]
    allowed_uids = apache, root
    user_attributes = +mail, +givenname, +sn

Restart sssd


    service sssd restart

Enable following selinux boolean:


    setsebool -P httpd_dbus_sssd on
## Configure Apache



The authentication against the external identity provider (FreeIPA) is achieved via a set of Apache modules. We need to install them:

(On RHEL7/CentOS7 the modules will be fetched from the same yum repo as the sssd-dbus package installed above.)


    yum install mod_auth_kerb mod_authnz_pam mod_lookup_identity mod_intercept_form_submit -y

We then need to configure the modules. Note that we configure mod_authnz_pam and mod_intercept_form_submit to use the *spacewalk-prod* PAM service we've configured above:


    # /etc/httpd/conf.d/auth_kerb.conf
    LoadModule auth_kerb_module modules/mod_auth_kerb.so
    LoadModule authnz_pam_module modules/mod_authnz_pam.so
    <Location /rhn/Login.do>
      AuthType Kerberos
      AuthName "Kerberos Login"
      KrbMethodNegotiate On
      KrbMethodK5Passwd Off
      KrbAuthRealms EXAMPLE.COM
      Krb5KeyTab /etc/http.keytab
      KrbLocalUserMapping On
      require pam-account spacewalk-prod
      ErrorDocument 401 '<html><meta http-equiv="refresh" content="0; URL=/rhn/Login2.do"><body>Kerberos authentication did not pass.</body></html>'
      # The following is needed as a workaround for https://bugzilla.redhat.com/show_bug.cgi?id=1020087
      ErrorDocument 500 '<html><meta http-equiv="refresh" content="0; URL=/rhn/Login2.do"><body>Kerberos authentication did not pass.</body></html>'
    </Location>


    # /etc/httpd/conf.d/intercept_form_submit.conf 
    LoadModule intercept_form_submit_module modules/mod_intercept_form_submit.so
    LoadModule authnz_pam_module modules/mod_authnz_pam.so
    <LocationMatch ^/rhn/(Re)?LoginSubmit\.do>
      InterceptFormPAMService spacewalk-prod
      InterceptFormLogin username
      InterceptFormPassword password
    </LocationMatch>


    # /etc/httpd/conf.d/lookup_identity.conf 
    LoadModule lookup_identity_module modules/mod_lookup_identity.so
    <LocationMatch /rhn/(Re)?Login(Submit)?\.do>
      LookupUserAttr mail AJP_REMOTE_USER_EMAIL " "
      LookupUserAttr givenname AJP_REMOTE_USER_FIRSTNAME
      LookupUserAttr sn AJP_REMOTE_USER_LASTNAME
      # LookupUserAttr ou AJP_REMOTE_USER_ORGUNIT
      # LookupUserGroupsIter AJP_REMOTE_USER_GROUP
    </LocationMatch>

You can certainly put all the configuration into one configuration file and not list the mod_authnz_pam twice.

Run:


    setsebool -P allow_httpd_mod_auth_pam 1

Restart Apache:


    service httpd restart
## Configure tomcat



For tomcat to observe the authentication done by the Apache server, its server.xml configuration file needs to be amended:


    # /etc/tomcat/server.xml or /etc/tomcat6/server.xml, depending on distribution:
    # Find the
    #   <Connector port="8009" protocol="AJP/1.3" redirectPort="8443" URIEncoding="UTF-8" ... />
    # elements and add attribute
    #   tomcatAuthentication="false"
    # They should look like this:
    <Connector port="8009" protocol="AJP/1.3" redirectPort="8443" URIEncoding="UTF-8" address="127.0.0.1" tomcatAuthentication="false" />
    <Connector port="8009" protocol="AJP/1.3" redirectPort="8443" URIEncoding="UTF-8" address="::1" tomcatAuthentication="false" />

Restart tomcat:


    service tomcat restart
    # or
    service tomcat6 restart

Wait for tomcat to be back up and test that you can log in to Spacewalk with your regular admin login and password.
## Host based access control prerequisites
 #hbac


To use the host based access control (HBAC) with the Spacewalk server, we want to create HBAC service and rule on the FreeIPA server, and set up PAM service of the same name on the Spacewalk server.

We need to start with disabling the default `allow_all` rule, otherwise any user will be allowed access. The page http://www.freeipa.org/page/Howto/HBAC_and_allow_all describes the process in detail, here we just show the commands:


    ipa hostgroup-add --desc 'Host group which will have allow_all_users_services HBAC enabled.' allow_all_hosts
    ipa host-find --raw --pkey-only --sizelimit=0 \
        | awk '$1 == "fqdn:" { print "--hosts=" $2 }' | xargs -n100 ipa hostgroup-add-member allow_all_hosts
    ipa hbacrule-add allow_all_users_services --usercat=all --servicecat=all --desc='Allow access to hosts in group allow_all_hosts to anybody from anywhere.'
    ipa hbacrule-add-host allow_all_users_services --hostgroups=allow_all_hosts
    ipa hbacrule-disable allow_all

We then create new user, *bob*, and new service, *spacewalk-prod*, and allow access to bob to this service on our Spacewalk server. In the example bellow, we assume that it is run on the Spacewalk server, and therefore the `$( hostname )` expands to the Spacewalk server hostname. If you run the commands on the FreeIPA server or other machine, put in the Spacewalk server hostname.


    # As admin user
    # Create user bob
    ipa user-add --random bob
    # Authenticate as bob and change your random password, shown by the previous command
    kinit bob
    # To continue working, kinit back as admin
    kinit admin
    # Create UBAC service spacewalk-prod and HBAC rule allow_spacewalk-prod
    ipa hbacsvc-add spacewalk-prod
    ipa hbacrule-add allow_spacewalk-prod
    # Add just bob and the Spacewalk server to this rule for this service
    ipa hbacrule-add-service allow_spacewalk-prod --hbacsvcs=spacewalk-prod
    ipa hbacrule-add-user allow_spacewalk-prod --user=bob
    ipa hbacrule-add-host allow_spacewalk-prod --hosts=$( hostname )
    # Remove the Spacewalk server from the host group we've created when we've disabled the allow_all rule
    ipa hostgroup-remove-member allow_all_hosts --hosts=$( hostname )
    # Test that just the allow_spacewalk-prod rule matches
    ipa hbactest --user=bob --host=$( hostname ) --service=spacewalk-prod
## Enable the use of the external authentication in Spacewalk



By following the configuration steps above, external authentication is enabled.

Existing Spacewalk users may login to Spacewalk externally authenticated by the FreeIPA server.

To allow login also to users, that currently do not have Spacewalk accounts you may:
* pre-create their accounts in desired organization via WebUI or API
* set the Org. Unit for every user on the FreeIPA server (that shall be able to login)

    ipa user-mod bob --orgunit="My Organization"
 and select "Use organization unit name passed from IPA" option on the *Admin* > *Users* > *External Authentication* page.
 If the orgunit matches to any organization name within Spacewalk, the appropriate Spacewalk account will automatically be created for the user in that "My Organization" at the very first login attempt.

 To make use out of the orgunit option, uncomment following line in /etc/httpd/conf.d/lookup_identity.conf:
 
      LookupUserAttr ou AJP_REMOTE_USER_ORGUNIT
     }}}
     and restart httpd service.
    
     To add !LookupUserAttr ou attribute to configuration file /etc/sssd/sssd.conf
     {{{
     ldap_user_extra_attrs = mail, givenname, sn, ou
     ...
     [ifp]
     allowed_uids = apache, root
     user_attributes = +mail, +givenname, +sn, +ou
     }}}
     and restart sssd service.
    
    * select "Default organization" on the *External Authentication* page and the Spacewalk account will automatically be created for the user at the very first login attempt in the "Default organization"
    
    Note that the automatically created accounts can be used for local logins only after setting a valid Spacewalk password.
## Try the form-based PAM authentication

    

    Purge your Kerberos ticket cache:
    
    {{{
    kdestroy -A

Log out from your admin session and attempt to log in as *bob* entering the password selected during the `kinit bob` step. You should be able to log in and if you check the user details, the name and email address is populated from the FreeIPA server. Log out.
## Try the Kerberos authentication



Kinit as bob and attempt to access the home page of your Spacewalk server:


    kinit bob

You should be authenticated directly, without any login form shown, based on your ticket granting ticket that you've obtained with the `kinit` command.
## Grant additional privileges according to the external group membership



To make use of external group membership, uncomment following line in /etc/httpd/conf.d/lookup_identity.conf:

     LookupUserGroupsIter AJP_REMOTE_USER_GROUP
and restart httpd service.
### Set additional Spacewalk roles



Let's create a separate group for bob.
Run on FreeIPA server:

    ipa group-add spacewalk-channel-admins
    ipa group-add-member spacewalk-channel-admins --users=bob

Log in to Spacewalk WebIU as Spacewalk administrator and navigate to *Admin* > *Users* > *External Authentication* > *Group Role Mapping*.

Create new External Group by continuing on *create new external group* and filling in the name of the external group we created on the FreeIPA server in the previous step - "spacewalk-channel-admins". Additionally select "Channel Administrator" or any other roles bob shall inherit.

Next time bob logs in to Spacewalk authenticated by the FreeIPA server, he'll additionally be granted the "Channel Administrator" role.
(Unless the "Keep temporary roles granted due to external authentication configuration" option is checked (on the *Admin* > *Users* > *External Authentication* > *Configuration* page), bob will lose the additional role(s), when he logs in to Spacewalk using his username and Spacewalk password.)
### Set additional Spacewalk System Group administrator rights (available in Spacewalk 2.2)



Let's create a new user and another new group on the FreeIPA server:

    ipa user-add --random cecile
    ipa group-add testers
    ipa group-add-member testers --users=cecile
Make sure user cecile does not have her Spacewalk account created before the following configuration:

Log in to Spacewalk WebUI as Organization administrator and make sure there's at least one System Group created within you Spacewalk organization. If not, create one, named "test_machines".

Now navigate to *Users* > *System Group Configuration* > *External Authentication* and create a new External Group by following *create new external group* link. Enter "testers" as External Group Name and select "test_machines" System Group.

Now, get cecile's kerberos ticket

    kinit cecile
and access the home page of your Spacewalk server.

As cecile's account gets created, she will be set as administrator of the "test_machines" System Group.
(Note, that System Group administrator assignment happens only on user creation. Changing of "External Group to System Groups Mapping" has no effect on already created user accounts.)

----
## Development notes



The solution proposal for Satellite / Spacewalk is based on four-module setup of WebUI authentication described at: http://www.freeipa.org/page/Web_App_Authentication. The proposal is based on the idea that the protocol-specific work is done in the Apache (modules) configuration and all that is left for the application to do is to consume the result of the Apache work, in the form of the REMOTE_USER* environment variables (in case of Java applications it's request.getAttribute, transferred from Apache to tomcat via mod_proxy_ajp). The change needed in the application was to respect the REMOTE_USER value when it is set, instead of attempting its own login-form-based authentication. When no external authentication has happened, the application falls-back to login-form-based authentication.

The changes are fairly localized because they mostly deal with setting up the user in the internal database and creating the correct session. After that, standard cookie-based session is used.

The work is now fully merged into Spacewalk, as of Spacewalk 2.2.
## Workstation setup



- Make sure you can access the IPA server, if not, add it to /etc/hosts
- Make sure you have <spacewalk_hostname> = EXAMPLE.COM in your /etc/krb5.conf within the [[domain_realm]] section if you use some nonstandard realm (like EXAMPLE.COM)
