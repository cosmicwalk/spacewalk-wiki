
    #!html
    <div style="background-color:#2b299e; color:white; background-image:url('/spacewalk/attachment/wiki/WikiSnippetsAndTemplates/workinprogress.png?format=raw'); background-repeat:no-repeat; padding-left:7em;background-position:1em;" >
       <h2>Work in Progress</h2>
       <div style="padding-bottom:0.5em;" >
           <p>This page is a 'work in progress' and will continue to go through modifications as solutions are finalized.</p>
       </div>
    </div>
## Problem Statement:



Although Spacewalk supports clients (eg. x86_64) that utilize and support packages of multiple architectures (eg. x86_64, i386...), there are several scenarios that it does properly handle for those clients.  This document is intended to high-light those scenarios and document proposed solutions.

Below are scenarios where issues have been identified.  For each scenario listed, a Spacewalk Bugzilla has been generated and included for reference.  Although attempting to be complete, there is a possibility that there are other issues not yet identified.  If you are aware of an issue that has not been listed, please create a detailed Bugzilla for Spacewalk.
## References:


## Estimates: High-Level Total:



The following is a summation of the estimates to address all identified
issues:

 * Schema  - 0.5 days
 * UI      - 33 days
 * Backend - 3 days
 * *Total   - 36.5 days*

For more details on the breakdown, refer to the sections below.
## Issues / Modifications / Estimates Details:

### 1. Systems -> System


#### Installing packages (systems->system->software->packages->install)




 a. (bz 456450) Users are unable to install i386 packages on an x86_64 client from the server when there exist both an x86_64 and i386 version of the package on the server.  Basically, the UI does not allow the user to select the architecture of the package to be installed and the package architecture is not communicated to the client during update; therefore, the client will choose what it determines to be the "best" selection, which will always be the x86_64 package on an x86_64 client.

 *__Proposed Modifications:__*
 * UI
   * query - update to obtain list of all packages available to the client
   * update list to display all packages returned by the query
   * add a column to the package list displayed to include package architecture
   * update underlying logic to save the package architecture (in rhnActionPackage.package_arch_id) when scheduling the 'install' action, so that it can be passed to the client during update
 * Backend - updates to ensure that always retrieve the package architecture from the database and send it to the client.

 *__Estimate:__*
 * UI - 2 days 
 * Backend - 4 hours
#### Listing packages (systems->system->software->packages->list)



 a. (bz 456451) Selecting a package from the package list associated with an x86_64 client displays the details of an x86_64 package, even if the client has the i386 package installed. This is due to the fact that the package data in rhnServerPackage for x86_64 clients does not include the package architecture.  

 *__Proposed Modifications:__*
 * Backend - Updates to populate the rhnServerPackage.package_arch_id for all supported architectures.

 *__Estimate:__*
 * Backend - 2 hours

 b. (bz 456452) If the client has multiple instances of a package installed for different architectures (e.g. i386 & x86_64), the package is only listed once.  Observation: Although there are multiple versions of the package installed on the client, the rhnServerPackage only has a single record.

 *__Proposed Modifications:__*
 * Backend - Update to enable populating multiple records in rhnServerPackage for a given (server_id,name_id,evr_id).  

 *__Estimate:__*
 * Backend - 4 hours
#### Removing packages (systems->system->software->packages->remove)



 a. (bz 456455) If there are multiple instances of a package installed on a client, selecting to remove an individual package results in all instances (e.g. i386 & x86_64) being removed from the client. In this scenario, although a package associated with a specific architecture is chosen from the UI, the package architecture is not communicated to the client.

 *__Proposed Modifications:__*
 * UI - update underlying logic to save the package architecture when scheduling the 'remove' action, so that it can be passed to the client during update
 * Backend - updates to ensure that always retrieve the package architecture from the database and send it to the client.
        
 *__Estimate:__*
 * UI - 1 day
 * Backend - 6 hours
#### Verifying packages (systems->system->software->packages->verify)



 a. (bz 456539) - Scheduling a package verification from Spacewalk on a client that has files that belong to 2 packages with same NEVR but different
architectures, the scheduled verification action never completes and each attempt generates an Internal Server Error.  (This issue is currently being worked on; therefore, details on resolution will be omitted from the wiki.)
 b. If user selects the package, it shows the details for the x86_64, even if the pkg installed in i386.  Note: Solving the "Listing packages" issue will also solve this one.  The reason being, both are using the same query from PackageManager for generating the package list.

 Note: If user schedules a verify of an package, where only the i386 version of the package is installed, the verify succeeds.

 *__Proposed Modifications:__*
 * None
### 2. Systems -> System Set Manager

#### Installing packages (systems->ssm->packages->install)




 a. (bz 456463) Users are unable to install i386 packages on an x86_64 client from the server when there exist both an x86_64 and i386 version of the package on the server. Although the UI for SSM allows the user to select to install a package associated with a specific architecture, when the package is installed for an x86_64 server the package installed is always the x86_64.  The reason being, the package architecture associated with the action is neither stored on the server nor communicated to the client during update.

 *__Proposed Modifications:__*
 * UI - 
   * port page from perl to java
   * enhance ported logic to save the package architecture (in rhnActionPackage.package_arch_id) when scheduling the 'install' action, so that it can be passed to the client during update

 *__Estimate:__*
 * UI - 5 days
#### Removing packages (systems->ssm->packages->remove)



 a. (bz 456467) The SSM allows user to select a package to remove; however, it does not allow them to select the architecture of the package being removed. As a result, all instances of the package (e.g. i386 & x86_64) are removed during client update.

 *Do we want to provide the capability to perform removal of packages by architecture using SSM?*

 *__Proposed Modifications:__* (if want to schedule per-arch removal)
 * UI - 
   * port page from perl to java
   * query - update to obtain list of all packages available to the clients in SSM
   * update list to display all packages returned by the query
   * add a column to the package list displayed to include package architecture
   * update logic to save the package architecture (in rhnActionPackage.package_arch_id) when scheduling the 'remove' action, so that it can be passed to the client during update

 *__Estimate:__*
 * UI - 5 days
#### Verifying packages (systems->ssm->packages->verify)



 a. (bz 456543) The SSM allows the user to select a package to verify; however, it does not allow them to select the architecture of the package being verified.  

 Observation:
 * If x86_64 client has only i386 package (e.g. for libjpeg) installed, the verify succeeds.
 * If x86_64 client has i386 and x86_64 package (e.g. for libjpeg) installed, the verify fails.  It seems the issue is related to trying to compare an i386 version of the package with an x86_64 version of the package.  

 *Do we want to provide the capability to perform verification of packages by architecture using SSM?*

 If not, we should update the client so that it verifies each instance (e.g. i386 & x86_64) of the package installed (since the package arch is not being communicated during update).

 *__Proposed Modifications:__* (if we want to schedule per-arch verification)
 * UI
   * port page from perl to java
   * query - update to obtain list of all packages available to the clients in SSM
   * update list to display all packages returned by the query
   * add a column to the package list displayed to include package architecture
   * update logic to save the package architecture in rhnActionPackage.package_arch_id) when scheduling the 'verify' action, so that it can be passed to the client during update

 *__Estimate:__*

 * UI - 5 days
#### Upgrading packages (systems->ssm->packages->upgrade)



 a. The SSM allows users to select a package to upgrade; however, it does not include package architecture.  This should be Ok since the upgrades are based on packages installed

 *__Proposed Modifications: __*
 * none
### 3. Systems -> Activation Keys:



 (bz 456472) Users do not have the ability to specify the architecture of packages to associate with an Activiation Key.  When packages are associated with an Activation Key via the UI, only the package name is supported/used (e.g. libjpeg).  Since package names do not reflect the architecture, the user cannot control the specific package that will be installed on the client.  If the user attempts to associate an architecture in the package name (e.g. libjpeg.i386), the package is added to the list; however, it is ignored when the activation key is used because a package by that name does not exist.

 *__Proposed Modifications:__*
 * Schema
   * Update the schema to include package_arch_id in rhnRegTokenPackages
 * UI
   * Convert packages.pxt to java.
   * Update UI to support allowing a user to enter the package architecture. This could be by supporting the <packagename>.<arch> syntax using the existing form or could be a change in form design.  This should be discussed with UI design team.
 * API
   * Update the APIs to support specifying package architecture (e.g. addPackageNames, removePackageNames)
 * Backend
   * Update so that the scheduled actions, associated with the activation key package installs, include the package architecture (i.e. set package_arch_id_.

 *__Estimate:__*
 * Schema - 0.25 days
 * UI - 5 days
 * APIs - 1 day
 * Backend - 8 hours
### 4. Systems -> Stored Profile:
 

#### systems->stored profiles->packages,   system->software->profile



a. (bz 456532) Package profiles do not include the package architecture information.  As a result, if an i386 package is removed from an x86_64 client and the users performs a profile comparison, they will see that the package is missing; however, performing a sync from the profile to reinstall it on the client results in the x86_64 package being installed.  (Note: package arch information is not received from the server during rhn_check)

*__Proposed Modifications:__*

Creating a profile:
 * Schema
   * Update to include package_arch_id in rhnServerProfilePackage.
 * UI
   * Update package_queries.insert_package_profile to copy the package_arch_id from the rhnServerPackage to rhnServerProfilePackage. This requires that A.2.a above is completed (which is to populate rhnServerPackage.package_arch_id)

Comparing a system against a profile & syncing ("Compare to Stored Profile"):
 * UI
   * Update the UI (CompareProfiles) to display package architecture differences.  This may be a matter of including the architecture in the "This System" and "Profile" columns based where the evr is currently displayed.  This approach should be confirmed with the UI design team.
   * Update package_queries.profile_canonical_package_list to retrieve package_arch_id (which needs to be added as part of "Creating a profile").
   * Update package_queries.system_canonical_package_list to retrieve package_arch_id (which requires that it be populated (ref A.2.a))
   * Update the package comparison logic to incorporate the architecture. (This is likely to be messy due to the current logic... Might be worthwhile to see if it is possible to use NEVRA vs NEVR, instead of comparing the arch separately... which could be a change to the schema further)

Viewing a stored profile:
 * UI
   * Convert package-list.pxt to java.
     * For completeness, recommend also converting profiles/details.pxt and profiles.list.pxt to java also. Reference:
       * https://spacewalk.com/network/profiles/details.pxt?prid=102 
         * provides name/description of a stored profile (systems->stored profiles->profile->details)
       * https://spacewalk.com/network/profiles/list.pxt
         * lists the stored profiles (systems->stored profiles)
   * Update UI (for package-list) to include a column specifying the architecture of each package as retrieved from the profile.

Comparing a system against another system ("Compare to System"):
 * UI
   * Update the UI (CompareSystems) to display package architecture differences. This may be a matter of including the architecture in the "This System" and "Profile" columns based where the evr is currently displayed.  This approach should be confirmed with the UI design team.
   * Note: addressing the package comparision logic for the "Compare to Stored Profile" should also address the needs of this scenario.
          
*__Estimate:__*
 * Schema - 0.25 days
 * UI - 10 days
## Other Considerations:



 * Upgrades - for those scenarios involving modifications to the schema, upgrades need to be handled.  Is this typically handled on a per feature basis?  Or is there a single release-level task for upgrades?

 * Since several of the enhancements require populating the existing package_arch_id for x86_64, do we need to implement tools that would fix this "missing" data on existing deployments?  If we don't, users will not be able to leverage these enhancements on existing clients.  Is there already a mechanism available that will audit the packages on a client and update the database appropriately?

 * Existing stored profiles will not have package architecture information;therefore, those profiles would have to be manually re-created to retrieve architecture information, if desired.
