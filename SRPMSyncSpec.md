
# **DEPRECATED, NO LONGER USED**

# Specifications for SRPM Sync

## Goal:




The idea here is to be able to sync down the source RPM content via satellite-sync. This includes to be able to export the content through the exporter as well. We already have some functionality in place, so we might as well leverage that and be able to hanlde the source rpm data on our spacewalk servers. 
## Requirements:



 * Support for spacewalk sync to be able to sync down the source rpm data.

 * Support for exporter to be able to extract the SRPM data from the spacewalk database
## Specifications :

### Changes to Satellite Sync:




 * We need to add the necessary flags to turn on the srpm support on satellite-syncs, possibly  --no-srpm.

     ` $ satellite-sync -c rhel-i386-as-4 --no-srpms `

 * Include the srpm processing in the step list to be able to control the stepping of the data.

     ` $ satellite-sync -c rhel-i386-as-4 --step=srpms `

 * We already have a call in the step sequence to fetch the source package metadata
    
         def _step_download_source_packages(self):
            return self.syncer.download_source_package_metadata()
        }}}
     * As said above we will need a _step_srpm() call doing something like this:
        {{{
         def _step_srpms(self):
            return self.syncer.download_srpms()
        }}}
    and syncer.download_srpms will be fetching the src.rpm rpms from the server on to the cache.
    
     * Need to also include a _diff_source_packages call so that we don't have to re fetch the source rpm metadata each time and compare the existing data on sat with what we'll be fetching. similar to _diff_packages, but for source rpms.
        {{{
        def _diff_source_packages():
            """
             Compare the source package metadata and skip already existing ones
            """
            return
        }}}
    
     * We already have _compute_not_cached_source_packages and _compute _uniq_source_packages calls that would be useful for the diff call above.
    
     * SourcePackageContainer objects (SIngleton) to create the necessary collection objects to be fed to the importer are already present for Source packages in sync_handler.SourcePackageCollection.
    
     * Dumper classes for Source rpms need to be added to create objects necessary for caching and dumping the file on to the file system.
    {{{
        class SourceRPMDumper(Dumper):
            _loader_class = xmlSource.SourceRPMContainer
    
            def _getMethod(self):
                return self.dump.source_rpms
or similar.

 * Add a srpm container to xmlSource and xmlDiskSource to support above calls

 * Also need to add a container class  for srpms to diskImportLib.

        class SourcePackageContainer(xmlSource.SourcePackageContainer):
            def endContainerCallback(self):
                importer = SourcePackageImport(self.batch, get_backend())
                importer.setIgnoreUploaded(1)
                importer.run()
                self.batch = []
 * Add a loader method to load a chunk of objects for given srpm ids

       def loadSourcePackages(mountPoint, srcpackageIds, handler, sources=0):
           """
            return a list of src pkg dumps for requested src pkg ids.
           """
 * Exporter changes are already in place to be able to be able to fetch the source rpms as ChannelSOurcePackagesDumper and SourcePackagesDumper
### Database Support



We already have the necessary tables to support SRPMS:
 rhnPackageSource and rhnSourceRPM are probably what we need here.
### rhn-satellite-Exporter Changes



 * We need to add the necessary flags to turn off the srpm data fetch on rhn-satellite-exporter possibly  --no-srpm.
{{{ 
    $ rhn-satellite-exporter -c rhel-i386-as-4 --no-srpms 
}}}

 * Include the srpm processing in the step list to be able to control the steppin
g of the data.

         $ rhn-satellite-exporter -c rhel-i386-as-4 --step=srpm

 * By default exporter should export the srpm content, with and without date ranges

 * We already have support to dump the source packages via the XML_dumper classes in dumper.py. So not much changes required here. Although some supporting calls might be needed.

 * Changes to iss_ui.py will be necessary to support the srpm flags and step sequence functionality.

= Test Plans =
