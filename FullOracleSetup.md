### Client



You will also need the Oracle Instant Client: [i386](http://www.oracle.com/technetwork/topics/linuxsoft-082809.html) or [x86_64](http://www.oracle.com/technetwork/topics/linuxx86-64soft-092277.html) version. Download the following two rpms (where ARCH is either i386 or x86_64):

 * oracle-instantclient11.2-basic-11.2.0.4.0.ARCH.rpm
 * oracle-instantclient11.2-sqlplus-11.2.0.4.0.ARCH.rpm

Do *NOT* use oracle-xe-client.
### Oracle InstantClient




    # yum -y localinstall --nogpgcheck oracle-instantclient11.2-basic*.rpm oracle-instantclient11.2-sqlplus*.rpm

  * *WARNING:* Don't run `oracle_env.sh` (or `oracle_env.csh`) in your root environment settings. Instantclient stuff works fine without it. It's usefull only for commandline server management under `oracle` user. 

Add the Oracle Instant Client lib path to LD_LIBRARY_PATH


    # echo 'export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/lib/oracle/11.2/client/lib' > /etc/profile.d/ld_oracle.sh
    # chmod +x /etc/profile.d/ld_oracle.sh

Replace client with client64 if you installed the x86_64 version of Oracle Instant Client.
### Server Data



you should get from your DBA for example this, I will refer to these values in the underneath configs:
 * db_host=oraclehost
 * db_user=spacewalkdbuser
 * db_password=spacewalkdbpassword
 * db_name=spacewalkdb
 * db_port=1521

Note that in addition to the resource and connect roles, the Oracle Spacewalk user must be granted alter session, create synonym, create table and create trigger.  (alter session and create synonym roles can be safely removed after running spacewalk-setup.)

Make sure that you connect to it from your spacewalk machine (open the correct ports on the firewall in between for example!)
simple test:

    telnet oraclehost 1521 

should at least work.

Next create tnsnames.ora.  The correct folder for it does not get created by the Instant Client install, so create it:


    # mkdir -p /usr/lib/oracle/11.2/client/lib/network/admin
Replace client with client64 if you installed the x86_64 version of Instant Client.

Now create the tnsnames.ora file in the network/admin folder with the following contents:

    ORACLEHOST=(DESCRIPTION=(ADDRESS_LIST=(ADDRESS=(PROTOCOL=TCP)(HOST=oraclehost)(PORT=1521)))(CONNECT_DATA=(SID=spacewalkdb)(SERVER=DEDICATED)))

tnsnames.ora is something similar to a hosts file for your oracle, it maps the name 'spacewalkdb' to the specified oraclehost+port so your oracle can use the 'spacewalkdb' to refer to the database server and correct database.

now you can test the connection:


    # sqlplus spacewalkdbuser/spacewalkdbpassword@spacewalkdb

if this works you are ready to go configure your spacewalk server.