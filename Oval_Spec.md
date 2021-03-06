# OVAL Specification

## Oval




  * http://oval.mitre.org
  * http://www.redhat.com/security/data/oval/
  * http://www.redhat.com/security/transparent/oval/
  * Open Vulnerability and Assessment Language
  * Standard to promote open and publicly available security content and to
  promote standarized vulnerability and configuration assesment. Standard to
  standardize transfer of this information.
  * In the context of Red Hat Network, we can look at OVAL as a structured
  information (XML) attached to (not every!) errata.
## Red Hat Network (hosted)



  * Oval information is pushed to RHN by Errata Tool
    * bugzilla_errata.submit_errata() XML-RPC call (not available in Spacewalk / Satellite)
    * Oval information is being saved as an XML file on a filer: com.redhat.<advisory-name>.xml
  * record in rhnErrataFile table for every single oval file
  * 'OVAL' type in rhnErratFileType table
  * XML-RPC call: errata.getOval(string sessionKey, string identifier)
  * oval data is currently not exported for satellite-sync
  * oval xml files available at http://rhn.redhat.com/rhn/oval?errata=<...>&format=<zip|xml>
    * https://rhn.redhat.com/rhn/oval?errata=10006
    * errata parameter can be either, errata id (rhnErrata.id), errata advisory (rhnErrata.advisory), CVE name (rhnCVE.name)
    * java application, no authentication required
    * linked from rhn/errata/details/Details.do (authenticated) pages
## Requirements



  * *(User story 1)* Ability to consume OVAL data from errata details page in Satellite's webui

  * *(User story 2)* Ability to transfer OVAL data during satellite-sync, more specifically:
    * live sync from RHN
    * satellite-sync -m. (from a channel dump)
    * inter satellite sync 
    * rhn-satellite-exporter needs to be able to export OVAL data into a dump

  * *(User story 3)* Ability to access OVAL data via XML-RPC API
    * equivalent errata.getOval() call from RHN

  * *(User story 4)* Ability to clone errata with associated oval data
    * oval data needs to be available in the cloned errata
## Specification



Most important part of the task is contained in user story 2: backend 
and satellite-sync need to be extended to support synchronization of
OVAL content of hosted.

Some other parts of the functionality is already present in current spacewalk
code, the code is either not used or disabled (spacewalk-java).
### User story 2



RHN changes

Based on talks with Stephen Herr, RHN hosted will make changes in backend code:

  * when exporting erratas for sat-sync, erratas with oval data will put
  the oval file into file list for that particular errata, file type
  set to oval

  * new handler / function which satellite-sync will call to download oval
  file from hosted

  * the new functionality will most likely be tied to a new sync protocol version
  although older satellites should be able to deal with this change quite
  nicely

Changes in Spacewalk code:

*spacewalk-schema:*

New row in rhnErrataFileType:


    insert into rhnErrataFileType ( id, label )
        values ( rhn_erratafile_type_id_seq.nextval, 'OVAL' );

*spacewalk-backend:*

  * modify satellite_tools.xmlWireSource to read oval data from RHN

  * satellite_tools.xmlDiskSource will be modified to read oval data from
  a channel dump

  * steps _download-errata_ & _errata_ in satellite-sync will be modified
  to process oval data downloaded from RHN / channel dump. Alternatively, new step _download-oval_ could be introduced (dependent on _download-errata_ step).
 
  * in case a particular errata has a file in its filelist with a file_type
  'OVAL', this file will be downloaded from RHN (either plain XML or compressed;
  depending on how RHN will implement this funtionality) and written out to
  a filer (for example /var/satellite/rhn/oval)

  * modify satellite_tools.xmlDiskDumper to support writing out oval data
  to a channel dump
### User story 1:



All required functionality is already present in current spacewalk-java.
As long as a particular errata has oval data associated, Spacewalk webui
will be able to display download link to the actual xml file.

There are some little differences between Spacewalk & RHN Hosted code
which are worth investigating and including in our code (if needed)
(classes: _OvalServlet.java_, _ErrataDetailsSetupAction.java_, _ErrataHandler.java_)

Branding issue in _OvalFileAggregator.java_: Spacewalk -> Red Hat Network Satellite
### User story 3:



getOval xmlrpc call is present in current spacewalk-java code, but is being
commented out (bug #504054): _package com.redhat.rhn.frontend.xmlrpc.errata_

This functionality needs to be restored, compared to RHN Hosted code and tested.
### User story 4:



_package com.redhat.rhn.domain.errata_, _createClone_ & _copyDetails_ need to be
modified to copy the oval details over to the cloned errata (ovalFile attribute).