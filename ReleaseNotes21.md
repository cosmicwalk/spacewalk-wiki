# __Spacewalk 2.1 Release Notes__



Hello everyone,

We are proudly announcing the release 2.1 of Spacewalk, a systems management solution.

The download locations are

  * http://yum.spacewalkproject.org/2.1/RHEL/5/
  * http://yum.spacewalkproject.org/2.1/RHEL/6/
  * http://yum.spacewalkproject.org/2.1/Fedora/19/
  * http://yum.spacewalkproject.org/2.1/Fedora/20/

with client repositories under

  * http://yum.spacewalkproject.org/2.1-client/RHEL/5/
  * http://yum.spacewalkproject.org/2.1-client/RHEL/6/
  * http://yum.spacewalkproject.org/2.1-client/Fedora/19/
  * http://yum.spacewalkproject.org/2.1-client/Fedora/20/


SUSE Linux client packages can be found here

   * http://download.opensuse.org/repositories/systemsmanagement:/spacewalk:/2.1/openSUSE_12.3/
   * http://download.opensuse.org/repositories/systemsmanagement:/spacewalk:/2.1/openSUSE_13.1/
   * http://download.opensuse.org/repositories/systemsmanagement:/spacewalk:/2.1/openSUSE_Factory/

For fresh installations, please use steps from

  * https://fedorahosted.org/spacewalk/wiki/HowToInstall

If you plan to upgrade from older release, search no more -- the following page will guide you:

  * http://fedorahosted.org/spacewalk/wiki/HowToUpgrade
## Features & Enhancements in Spacewalk 2.1



  * Spacewalk runs on Fedora 20, support for Fedora 18 has been dropped
  * New UI look and feel (big thanks to SUSE upstream contributors)
    * Twitter Bootstrap 3.0 integration.
    * Responsive design works on desktops, tablets and phones.
    * New date-picker will make scheduling events joyful.
    * Password strength meter.
  * Support for using SSL for connections to external/managed PostgreSQL
    * https://fedorahosted.org/spacewalk/wiki/HowToPostgreSQLoverSSL
  * SCAP improvements
    * Added tailoring support to the Spacewalk (OpenSCAP enabled) client
    * Enabled easy comparison between various SCAP scans
  * Identity management (IPA) integration
    * https://fedorahosted.org/spacewalk/wiki/SpacewalkAndIPA
  * Plenty of small enhancements and fixes
  * This is the last release of Spacewalk for RHEL5.
  * New API calls:
    * `channel.software.listErrataNeedingSync()`
    * `channel.software.syncErrata()`
    * `channel.software.updateRepoLabel()`
    * `kickstart.profile.orderScripts()`
    * `kickstart.profile.software.setSoftwareList()`
    * `org.getPolicyForScapFileUpload()`
    * `org.getPolicyForScapResultDeletion()`
    * `org.setPolicyForScapFileUpload()`
    * `org.setPolicyForScapResultDeletion()`
    * `system.scap.deleteXccdfScan()`

The up-to-date API documentation can be found at http://www.spacewalkproject.org/documentation/api/
## Contributors



Our thanks go to the community members who contributed to this release:

  * Aron Parsons
  * Avi Miller
  * Bo Maryniuk
  * Christopher Duryee
  * Cynthia Sanchez
  * David Juran
  * Dimitar Yordanov
  * Duncan Mac-Vicar P
  * Flavio Castelli
  * Gregor Gruener
  * Jan Pazdziora
  * Jiri Mikulka
  * Johannes Renner
  * Mark Huth
  * Maximilian Meister
  * Michael Calmer
  * Miroslav Suchý
  * Paresh Mutha
  * Paul Robert Marino
  * Peter Gervase
  * Shannon Hughes
  * Silvio Moioli
  * Simon Lukasik
  * Tasos Papaioannou

https://fedorahosted.org/spacewalk/wiki/ContributorList
## Some statistics



In Spacewalk 2.1, we've seen

    * 201 bugs fixed
    * 1715 changesets committed
    * 2357 commits done
## User community, reporting issues



To reach the user community with questions and ideas, please use
mailing list spacewalk-list@redhat.com. On this list, you can of
course also discuss issues you might find when installing or using
Spacewalk, but please do not be surprised if we ask you to file a bug
at https://bugzilla.redhat.com/enter_bug.cgi?product=Spacewalk with more
details or full logs.

Thank you for using Spacewalk.
