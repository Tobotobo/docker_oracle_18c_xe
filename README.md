# docker_oracle_18c_xe

## 超絶途中

## 概要


## 前提
* `oracle/database:18.4.0-xe` のイメージがビルド済みであること

  ※まだの場合は以下を参照  
  　docker_oracle_exmple1  
  　https://github.com/Tobotobo/docker_oracle_example1  



## 詳細

```
docker compose exec oracle-db bash
```

### sqlplus
```
# CDB に接続する場合
sqlplus sys/password@XE as sysdba

# PDB に接続する場合
sqlplus sys/password@XEPDB1 as sysoper
sqlplus system/password@XEPDB1
sqlplus pdbadmin/password@XEPDB1
```

### rman
```
rman target sys/password@XE
```
```
BACKUP DATABASE PLUS ARCHIVELOG;
```
```
RMAN> BACKUP DATABASE PLUS ARCHIVELOG;


Starting backup at 26-OCT-24
Oracle error from target database: 
ORA-00258: manual archiving in NOARCHIVELOG mode must identify log

using target database control file instead of recovery catalog
allocated channel: ORA_DISK_1
channel ORA_DISK_1: SID=738 device type=DISK
specification does not match any archived log in the repository
backup cancelled because there are no files to backup
Finished backup at 26-OCT-24

Starting backup at 26-OCT-24
using channel ORA_DISK_1
RMAN-00571: ===========================================================
RMAN-00569: =============== ERROR MESSAGE STACK FOLLOWS ===============
RMAN-00571: ===========================================================
RMAN-03002: failure of backup plus archivelog command at 10/26/2024 17:39:16
RMAN-06149: cannot BACKUP DATABASE in NOARCHIVELOG mode
```

### オフラインバックアップ
```
sqlplus sys/password@XE as sysdba
```
#### データベースの停止
`SHUTDOWN IMMEDIATE;` 実行
```
SQL> SHUTDOWN IMMEDIATE;
Database closed.
Database dismounted.
ORACLE instance shut down.
ERROR:
ORA-12514: TNS:listener does not currently know of service requested in connect
descriptor


Warning: You are no longer connected to ORACLE.
```

```
rman target sys/password@XE
```

```
su - oracle
export PATH=$PATH:/opt/oracle/product/18c/dbhomeXE/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
sqlplus / as sysdba
```

```
su -p oracle
STARTUP MOUNT;
```

```
SQL> STARTUP MOUNT;
ORACLE instance started.

Total System Global Area 1610610568 bytes
Fixed Size                  8898440 bytes
Variable Size            1023410176 bytes
Database Buffers          570425344 bytes
Redo Buffers                7876608 bytes
Database mounted.
```

```
rman target /
```
BACKUP DATABASE;

```
RMAN> BACKUP DATABASE;

Starting backup at 26-OCT-24
using target database control file instead of recovery catalog
allocated channel: ORA_DISK_1
channel ORA_DISK_1: SID=857 device type=DISK
channel ORA_DISK_1: starting full datafile backup set
channel ORA_DISK_1: specifying datafile(s) in backup set
input datafile file number=00001 name=/opt/oracle/oradata/XE/system01.dbf
input datafile file number=00003 name=/opt/oracle/oradata/XE/sysaux01.dbf
input datafile file number=00004 name=/opt/oracle/oradata/XE/undotbs01.dbf
input datafile file number=00007 name=/opt/oracle/oradata/XE/users01.dbf
channel ORA_DISK_1: starting piece 1 at 26-OCT-24
channel ORA_DISK_1: finished piece 1 at 26-OCT-24
piece handle=/opt/oracle/product/18c/dbhomeXE/dbs/0138ifdg_1_1 tag=TAG20241026T180224 comment=NONE
channel ORA_DISK_1: backup set complete, elapsed time: 00:00:03
channel ORA_DISK_1: starting full datafile backup set
channel ORA_DISK_1: specifying datafile(s) in backup set
input datafile file number=00010 name=/opt/oracle/oradata/XE/XEPDB1/sysaux01.dbf
input datafile file number=00009 name=/opt/oracle/oradata/XE/XEPDB1/system01.dbf
input datafile file number=00011 name=/opt/oracle/oradata/XE/XEPDB1/undotbs01.dbf
input datafile file number=00012 name=/opt/oracle/oradata/XE/XEPDB1/users01.dbf
channel ORA_DISK_1: starting piece 1 at 26-OCT-24
channel ORA_DISK_1: finished piece 1 at 26-OCT-24
piece handle=/opt/oracle/product/18c/dbhomeXE/dbs/0238ifdj_1_1 tag=TAG20241026T180224 comment=NONE
channel ORA_DISK_1: backup set complete, elapsed time: 00:00:01
channel ORA_DISK_1: starting full datafile backup set
channel ORA_DISK_1: specifying datafile(s) in backup set
input datafile file number=00006 name=/opt/oracle/oradata/XE/pdbseed/sysaux01.dbf
input datafile file number=00005 name=/opt/oracle/oradata/XE/pdbseed/system01.dbf
input datafile file number=00008 name=/opt/oracle/oradata/XE/pdbseed/undotbs01.dbf
channel ORA_DISK_1: starting piece 1 at 26-OCT-24
channel ORA_DISK_1: finished piece 1 at 26-OCT-24
piece handle=/opt/oracle/product/18c/dbhomeXE/dbs/0338ifdk_1_1 tag=TAG20241026T180224 comment=NONE
channel ORA_DISK_1: backup set complete, elapsed time: 00:00:01
Finished backup at 26-OCT-24

Starting Control File and SPFILE Autobackup at 26-OCT-24
piece handle=/opt/oracle/product/18c/dbhomeXE/dbs/c-3064517771-20241026-00 comment=NONE
Finished Control File and SPFILE Autobackup at 26-OCT-24
```

```
RMAN> BACKUP CURRENT CONTROLFILE;

Starting backup at 26-OCT-24
using channel ORA_DISK_1
channel ORA_DISK_1: starting full datafile backup set
channel ORA_DISK_1: specifying datafile(s) in backup set
including current control file in backup set
channel ORA_DISK_1: starting piece 1 at 26-OCT-24
channel ORA_DISK_1: finished piece 1 at 26-OCT-24
piece handle=/opt/oracle/product/18c/dbhomeXE/dbs/0538iffh_1_1 tag=TAG20241026T180329 comment=NONE
channel ORA_DISK_1: backup set complete, elapsed time: 00:00:01
Finished backup at 26-OCT-24

Starting Control File and SPFILE Autobackup at 26-OCT-24
piece handle=/opt/oracle/product/18c/dbhomeXE/dbs/c-3064517771-20241026-01 comment=NONE
Finished Control File and SPFILE Autobackup at 26-OCT-24
```

STARTUP;
```
```

```
RMAN> REPORT SCHEMA;

using target database control file instead of recovery catalog
Report of database schema for database with db_unique_name XE

List of Permanent Datafiles
===========================
File Size(MB) Tablespace           RB segs Datafile Name
---- -------- -------------------- ------- ------------------------
1    830      SYSTEM               ***     /opt/oracle/oradata/XE/system01.dbf
3    500      SYSAUX               ***     /opt/oracle/oradata/XE/sysaux01.dbf
4    65       UNDOTBS1             ***     /opt/oracle/oradata/XE/undotbs01.dbf
5    250      PDB$SEED:SYSTEM      ***     /opt/oracle/oradata/XE/pdbseed/system01.dbf
6    370      PDB$SEED:SYSAUX      ***     /opt/oracle/oradata/XE/pdbseed/sysaux01.dbf
7    5        USERS                ***     /opt/oracle/oradata/XE/users01.dbf
8    100      PDB$SEED:UNDOTBS1    ***     /opt/oracle/oradata/XE/pdbseed/undotbs01.dbf
9    250      XEPDB1:SYSTEM        ***     /opt/oracle/oradata/XE/XEPDB1/system01.dbf
10   370      XEPDB1:SYSAUX        ***     /opt/oracle/oradata/XE/XEPDB1/sysaux01.dbf
11   100      XEPDB1:UNDOTBS1      ***     /opt/oracle/oradata/XE/XEPDB1/undotbs01.dbf
12   5        XEPDB1:USERS         ***     /opt/oracle/oradata/XE/XEPDB1/users01.dbf

List of Temporary Files
=======================
File Size(MB) Tablespace           Maxsize(MB) Tempfile Name
---- -------- -------------------- ----------- --------------------
1    33       TEMP                 32767       /opt/oracle/oradata/XE/temp01.dbf
2    62       PDB$SEED:TEMP        32767       /opt/oracle/oradata/XE/pdbseed/temp012024-10-26_17-24-53-681-PM.dbf
3    62       XEPDB1:TEMP          32767       /opt/oracle/oradata/XE/XEPDB1/temp01.dbf
```

```
su -p oracle
rman target /
sqlplus / as sysdba
```

SHUTDOWN IMMEDIATE;

STARTUP MOUNT;

LIST BACKUP;
LIST BACKUP SUMMARY;

RESTORE DATABASE;
RESTORE DATABASE FROM TAG='TAG20241026T180224';


RECOVER DATABASE;
ALTER DATABASE OPEN RESETLOGS;



### 文字化け
SELECT userenv('language') FROM dual;

INSERT INTO employees (id, name, age) VALUES (2, '鈴木 花子', 29);

↓設定したらいけた
export NLS_LANG=AMERICAN_AMERICA.AL32UTF8

bash-4.2# locale
LANG=
LC_CTYPE="POSIX"
LC_NUMERIC="POSIX"
LC_TIME="POSIX"
LC_COLLATE="POSIX"
LC_MONETARY="POSIX"
LC_MESSAGES="POSIX"
LC_PAPER="POSIX"
LC_NAME="POSIX"
LC_ADDRESS="POSIX"
LC_TELEPHONE="POSIX"
LC_MEASUREMENT="POSIX"
LC_IDENTIFICATION="POSIX"
LC_ALL=


```
sudo curl -O https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
sudo yum install -y epel-release-latest-7.noarch.rpm

```

```
oraclelinux:7-slim locale glibc-langpack-ja

oraclelinux:7-slim glibc-langpack-ja
```

```
rman target /
sqlplus / as sysdba
sqlplus system@

sqlplus system/password@XE
sqlplus system/password@XEPDB1

sqlplus pdbadmin/password@XEPDB1


CREATE TABLE employees (id NUMBER PRIMARY KEY,name VARCHAR2(50),age NUMBER);
SELECT * FROM employees;
INSERT INTO employees (id, name, age) VALUES (1, '山田 太郎', 30);
INSERT INTO employees (id, name, age) VALUES (2, '鈴木 花子', 29);
INSERT INTO employees (id, name, age) VALUES (3, '田中 次郎', 28);
```