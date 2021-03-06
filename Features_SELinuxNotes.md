
The work on adding SELinux support to Spacewalk was done on Spacewalk 0.3 relase and on Spacewalk 0.4 nightly builds. The OS targeted was RHEL 5, and the only targeted policy was targeted.

The changes and new packages were committed to git master.
## What works



Currently (Wed Jan 14 15:27:49 CET 2009), with *spacewalk-selinux-0.4.1-7.el5* and other current packages built from master, it is possible to

 * install Spacewalk
 * configure Spacewalk (`spacewalk-setup`)
 * run Spacewalk:
   * use its WebUI
   * restart it via WebUI
   * run `rhnpush` and `satellite-sync`
   * register clients to the Spacewalk server
   * use `yum` and `rhn_check` on the client, including kickstarting them
   * use jabberd and osa-dispatcher on the server, and osad on the client, and instant push works
     * note that client tools like osad are not confined

You are welcome to change Permissive to Enforcing and give Spacewalk with SELinux a try.
### Oracle XE



With the *oracle-xe-selinux-10.2-5.el5*, it is possible to run Oracle XE with SELinux targeted in Enforcing.
However, it is necessary to run


    # /usr/sbin/groupadd -r dba
    # /usr/sbin/useradd -r -M -g dba -d /usr/lib/oracle/xe -s /bin/bash oracle

*before* installing the `oracle-xe-univ` package to create the oracle user as system user (with uid < 500).

Both the `/etc/init.d/oracle-xe configure` and creating the database user via the web interface can be done under Enforcing.
### Non-standard settings



The SELinux policy modules do a couple of assumptions.

 * the Oracle XE's web interface is expected to be on port 9000
   * if you've configured another port, you need to do

    semanage port -a -t oracle_port_t -p tcp $your_port
 * the mount point is expected to be `/var/satellite`
   * if you've configured different path, you need to do

    semanage fcontext -a -t spacewalk_data_t '$your_path(/.*)?'
## What does not work



Features not specifically listed above were not tested. They might work just fine if they are only database + `/var/satellite` + some `/var/cache/rhn` type of operations because those are already covered by the existing SELinux rule in the `spacewalk-selinux` policy module.

Most notably, monitoring will not work because it did not work in Spacewalk 0.3, so there was no baseline to develop against, and any feature added during the 0.4 cycle was also not addressed.

In addition, Spacewalk Proxy did not get any SELinux work yet.
## What types are used



The `ps axuwZ` output might look like this on your Spacewalk server:


    root:system_r:oracle_tnslsnr_t  oracle    7998  0.0  0.5  22048  3032 ?        Ss   16:00   0:01 /usr/lib/oracle/xe/app/oracle/product/10.2.0/server/bin/tnslsnr
    root:system_r:oracle_db_t       oracle    8003  0.0  2.0 229116 10868 ?        Ss   16:00   0:02 xe_pmon_XE
    root:system_r:oracle_db_t       oracle    8005  0.0  1.5 228508  8296 ?        Ss   16:00   0:00 xe_psp0_XE
    root:system_r:oracle_db_t       oracle    8007  0.0  1.8 228504 10084 ?        Ss   16:00   0:00 xe_mman_XE
    root:system_r:oracle_db_t       oracle    8009  0.1  4.5 239792 24108 ?        Ss   16:00   0:31 xe_dbw0_XE
    root:system_r:oracle_db_t       oracle    8011  0.1  4.6 244060 24988 ?        Ss   16:00   0:32 xe_lgwr_XE
    root:system_r:oracle_db_t       oracle    8013  0.0  2.9 229024 15588 ?        Ss   16:00   0:04 xe_ckpt_XE
    root:system_r:oracle_db_t       oracle    8015  0.0  6.4 229056 34456 ?        Ss   16:00   0:07 xe_smon_XE
    root:system_r:oracle_db_t       oracle    8017  0.0  3.8 229016 20720 ?        Ss   16:00   0:00 xe_reco_XE
    root:system_r:oracle_db_t       oracle    8019  0.0  6.3 230112 33840 ?        Ss   16:00   0:07 xe_cjq0_XE
    root:system_r:oracle_db_t       oracle    8021  0.0  8.6 231292 46412 ?        Ss   16:00   0:10 xe_mmon_XE
    root:system_r:oracle_db_t       oracle    8023  0.0  4.3 228508 23060 ?        Ss   16:00   0:04 xe_mmnl_XE
    root:system_r:oracle_db_t       oracle    8025  0.0  1.4 229168  7880 ?        Ss   16:00   0:00 xe_d000_XE
    root:system_r:oracle_db_t       oracle    8027  0.0  1.4 229112  7812 ?        Ss   16:00   0:00 xe_s000_XE
    root:system_r:oracle_db_t       oracle    8039  0.0  2.4 228504 13164 ?        Ss   16:00   0:00 xe_qmnc_XE
    
    root:system_r:oracle_db_t       oracle    8047  0.0  1.5 228504  8540 ?        Ss   16:00   0:00 xe_q000_XE
    root:system_r:initrc_t          root      8083  0.0  0.0  12216   436 ?        Sl   16:00   0:00 /usr/bin/taskomaticd /etc/rhn/default/rhn_taskomatic_daemon.con
    root:system_r:java_t            root      8085  0.1 10.6 331324 56972 ?        Sl   16:00   0:40 /usr/bin/java -Dibm.dst.compatibility=true -Xms32m -Xmx192m -Dj
    root:system_r:java_t            tomcat    8643  0.3 13.8 411764 74120 ?        Sl   16:01   1:31 /usr/lib/jvm/java/bin/java -ea -Xms256m -Xmx256m -Djava.awt.hea
    root:system_r:oracle_db_t       oracle    8672  0.0  5.3 229184 28788 ?        Ss   16:01   0:01 oracleXE (LOCAL=NO)
    root:system_r:oracle_db_t       oracle    8674  0.0  6.0 230236 32580 ?        Ss   16:01   0:02 oracleXE (LOCAL=NO)
    root:system_r:oracle_db_t       oracle    8676  0.0  5.2 229184 27888 ?        Ss   16:01   0:01 oracleXE (LOCAL=NO)
    root:system_r:oracle_db_t       oracle    8678  0.0  6.2 229224 33512 ?        Ss   16:01   0:02 oracleXE (LOCAL=NO)
    root:system_r:oracle_db_t       oracle    8680  0.0  5.8 230252 31376 ?        Ss   16:01   0:01 oracleXE (LOCAL=NO)
    root:system_r:initrc_t          root      8708  0.0  0.0  12216   432 ?        Sl   16:01   0:01 /usr/bin/rhnsearchd /etc/rhn/search/rhn_search_daemon.conf wrap
    root:system_r:java_t            root      8711  0.4  2.8 668408 15252 ?        Sl   16:01   1:50 /usr/bin/java -Djava.library.path=/usr/lib:/usr/lib64 -classpat
    root:system_r:httpd_t           root     11574  0.0  0.8  34792  4608 ?        Ss   21:19   0:00 /usr/sbin/httpd -f /etc/rhn/satellite-httpd/conf/httpd.conf
    root:system_r:httpd_sys_script_t root    11576  0.0  0.2   5408  1216 ?        S    21:19   0:00 /usr/bin/perl /etc/rhn/satellite-httpd/conf/satidmap.pl
    root:system_r:httpd_t           apache   11577  0.1  3.3 159572 18160 ?        S    21:19   0:10 /usr/sbin/httpd -f /etc/rhn/satellite-httpd/conf/httpd.conf
    root:system_r:httpd_t           apache   11578  0.1  2.3 157892 12768 ?        S    21:19   0:09 /usr/sbin/httpd -f /etc/rhn/satellite-httpd/conf/httpd.conf
    root:system_r:httpd_t           apache   11579  0.1  2.1 145784 11484 ?        S    21:19   0:07 /usr/sbin/httpd -f /etc/rhn/satellite-httpd/conf/httpd.conf
    root:system_r:httpd_t           apache   11580  3.3  1.9 167856 10684 ?        S    21:19   3:23 /usr/sbin/httpd -f /etc/rhn/satellite-httpd/conf/httpd.conf
    root:system_r:httpd_t           apache   11581  0.1  2.4 159036 12864 ?        S    21:19   0:09 /usr/sbin/httpd -f /etc/rhn/satellite-httpd/conf/httpd.conf
    root:system_r:httpd_t           apache   11582  0.1  2.1 145176 11524 ?        S    21:19   0:09 /usr/sbin/httpd -f /etc/rhn/satellite-httpd/conf/httpd.conf
    root:system_r:httpd_t           apache   11583  0.1  2.8 145864 15488 ?        S    21:19   0:10 /usr/sbin/httpd -f /etc/rhn/satellite-httpd/conf/httpd.conf
    root:system_r:httpd_t           apache   11584  0.1  1.9 145740 10404 ?        S    21:19   0:08 /usr/sbin/httpd -f /etc/rhn/satellite-httpd/conf/httpd.conf
    root:system_r:oracle_db_t       oracle   11595  0.0  9.0 230264 48496 ?        Ss   21:20   0:05 oracleXE (LOCAL=NO)
    root:system_r:oracle_db_t       oracle   11597  0.0  5.7 233296 30748 ?        Ss   21:20   0:01 oracleXE (LOCAL=NO)
    root:system_r:oracle_db_t       oracle   11601  0.0  5.7 233312 30628 ?        Ss   21:21   0:01 oracleXE (LOCAL=NO)
    root:system_r:oracle_db_t       oracle   11603  2.2  5.2 230220 28012 ?        Ss   21:21   2:14 oracleXE (LOCAL=NO)
    root:system_r:oracle_db_t       oracle   11671  0.0  4.5 230224 24596 ?        Ss   21:30   0:00 oracleXE (LOCAL=NO)
    root:system_r:oracle_db_t       oracle   11673 24.1  6.4 230220 34440 ?        Ss   21:30  22:05 oracleXE (LOCAL=NO)
    root:system_r:oracle_db_t       oracle   11675  0.0  5.8 233280 31208 ?        Ss   21:30   0:01 oracleXE (LOCAL=NO)

So most notably, we use RHEL's *httpd_t* for Apache and *java_t* for tomcat, taskomatic, and search server.