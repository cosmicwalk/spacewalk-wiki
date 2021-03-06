# PostgreSQL and blobs and Hibernate



This issue seems to have been resolved by Justin in spacewalk-java-1.1.33.

----

There is a problem with declarations like


    <property name="certBlob" column="CERT" not-null="true" type="blob" />

as reported in https://www.redhat.com/archives/spacewalk-devel/2010-June/msg00037.html -- it does not map to bytea that we use as the PostgreSQL columns in place of Oracle's blobs.

For now, I have removed that column from my local repo


    diff --git a/java/code/src/com/redhat/rhn/domain/satellite/SatelliteCertificate.hbm.xml b/java/code/src/com/redhat/rhn/domain/satellite/SatelliteCertificate.hbm.
    index 4363d48..83b09cf 100644
    --- a/java/code/src/com/redhat/rhn/domain/satellite/SatelliteCertificate.hbm.xml
    +++ b/java/code/src/com/redhat/rhn/domain/satellite/SatelliteCertificate.hbm.xml
    @@ -9,7 +9,6 @@ PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
                 <meta attribute="scope-set">protected</meta>
             </id>
             <property name="label" column="LABEL" not-null="true" type="string" length="64" />
    -        <property name="certBlob" column="CERT" not-null="true" type="blob" />
             <property name="issued" column="ISSUED"  type="timestamp" insert="false" update="false"/>
             <property name="expires" column="EXPIRES"  type="timestamp" insert="false" update="false"/>
             <property name="created" column="CREATED" not-null="true" type="timestamp" insert="false" update="false"/>

but obviously better solution is needed.

 * I've tried changing the type to `binary` but that breaks Oracle. But maybe with some more changes to the application code `binary` is the way to go for both database backends?
 * Another possibility would be to tweak the JDBC driver or the dialect of that driver not to try the oid approach for blobs.
 * Maybe we should go with oids longs instead of byteas?