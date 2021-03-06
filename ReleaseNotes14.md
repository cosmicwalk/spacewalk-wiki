# __Spacewalk 1.4 Release Notes__



Hello world, 

Spacewalk 1.4 is now available for download from 

  * http://spacewalk.redhat.com/yum/1.4/RHEL/5/$basearch/ 
  * http://spacewalk.redhat.com/yum/1.4/RHEL/6/$basearch/ 
  * http://spacewalk.redhat.com/yum/1.4/Fedora/13/$basearch/ 
  * http://spacewalk.redhat.com/yum/1.4/Fedora/14/$basearch/ 

depending on your operating system, with client repositories under 

  * http://spacewalk.redhat.com/yum/1.4-client/Fedora/13
  * http://spacewalk.redhat.com/yum/1.4-client/Fedora/14
  * http://spacewalk.redhat.com/yum/1.4-client/RHEL/5
  * http://spacewalk.redhat.com/yum/1.4-client/RHEL/6
  * http://download.opensuse.org/repositories/systemsmanagement:/spacewalk:/1.4/openSUSE_11.4/
  * http://download.opensuse.org/repositories/systemsmanagement:/spacewalk:/1.4/openSUSE_Factory/
  * http://miroslav.suchy.cz/spacewalk/debian

Check the installation steps at 

  * https://fedorahosted.org/spacewalk/wiki/HowToInstall 

or if you will upgrade from older release, consult 

  * http://fedorahosted.org/spacewalk/wiki/HowToUpgrade 
## Features & Enhancements in Spacewalk 1.4



        * client packages for Debian
        * client packages for OpenSuse
        * support for [IDN](http://en.wikipedia.org/wiki/Internationalized_domain_name) (but RHN Tools part)
                 - you can now have Spacewalk server and clients, whose
                  hostnames contains non latin domain name.
        * issues in PostgreSQL backend, reported by user, were fixed. [PostgreSQL status](PostgreSQL)
        * system history reports were added in spacewalk-reports
        * spacewalk-repo-sync now automatically create errata from updateinfo


Spacewalk 1.4 contains support for apt-get plug-in. 
An apt-get plug-in client package is available at 
 * http://miroslav.suchy.cz/spacewalk/debian/ 
Feel free to try it and report any issues. 
This repo contains apt with spacewalk patches.

For more information check: 
https://fedorahosted.org/spacewalk/wiki/RegisteringClients#Debian 
## Community contributors



We thank the community members who contributed to this release: 

 * Aron Parsons
 * Dale Bewley
 * Jerome Fenal
 * Johannes Renner
 * John van Zantvoort
 * Luc de Louw
 * Marcelo Moreira de Mello
 * mareklaane
 * Michael Calmer
 * Paresh Mutha
 * Šimon Lukašík
 * Trent Johnson
 * Uwe Gansert
 * ypoyarko

https://fedorahosted.org/spacewalk/wiki/ContributorList 
## Bug fixes and commits



In Spacewalk 1.4 there were: 

    * 68  bugs solved 
    * 634 changesets committed 
    * 899 commits done 


Enjoy using Spacewalk! 