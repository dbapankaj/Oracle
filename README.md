# DATABASE  RESTOR FROM BACKUP

DATABASE  RESTORATION FROM BACKUP
===============================================================
1) Check DB Size and Diskgroup free space.
2) Create new environment file
3) Create PFILE and take a necessary changes.
4) After that ,start database in nomount state using pfile.
5) Check database state.
6) Before excute below step ,check wallet directory location is present or not in SQLNET.ORA FILE.(if not create directory structure).
7) Create rman restoration script.

vi db_recovery_rman_steps.txt

connect auxiliary /
RUN
{
allocate auxiliary channel c1 device type disk;
allocate auxiliary channel c2 device type disk;
allocate auxiliary channel c3 device type disk;
allocate auxiliary channel c4 device type disk;
allocate auxiliary channel c5 device type disk;
allocate auxiliary channel c6 device type disk;
allocate auxiliary channel c7 device type disk;
allocate auxiliary channel c8 device type disk;
allocate auxiliary channel c9 device type disk;
allocate auxiliary channel c10 device type disk;
SET NEWNAME FOR DATABASE TO '+DATA1'; -- diskgrop name
DUPLICATE DATABASE TO NEW_DB_NAME
BACKUP LOCATION '/backup/BACKUP_LOCATION_PATH/rman/'
NOFILENAMECHECK;
release channel c1;
release channel c2;
release channel c3;
release channel c4;
release channel c5;
release channel c6;
release channel c7;
release channel c8;
release channel c9;
release channel c10;
}

8)Then, Use below command in nohupnohup rman cmdfile=restore_embcdbpp.cmd > restore_DB_name.log &

9) And monitor the restore_DB_name.log logfile.

10) Once retoration is done,Verify the post checks.(DB state,DB Size,invalid objetcs,schemas etc...).
verify Invalid objects
select count(*) from dba_objects where status='INVALID';
If any objects are invalid then execute utlrp script.
@/u01/app/oremb/product/12.0.0.0/dbhome_3/rdbms/admin/utlrp.sql

11) Verify the components.
col COMP_ID for a10
col COMP_NAME for a40
col VERSION for a15
set lines 180
set pages 999
select COMP_ID,COMP_NAME,VERSION,STATUS from dba_registry;

12) Then Gather stats on database
exec dbms_Stats.gather_dictionary_Stats(estimate_percent=>100,method_opt=>'for all columns size skewonly',granularity=>'all',options=>'gather',degree => 16);
EXEC dbms_stats.gather_database_stats(gather_sys=>TRUE,degree => 16);
EXEC dbms_stats.gather_schema_stats('SYS',degree => 16,estimate_percent=>100);
EXEC DBMS_STATS.GATHER_DICTIONARY_STATS (degree => 16,estimate_percent=>100);
EXEC DBMS_STATS.GATHER_SYSTEM_STATS;
EXEC DBMS_STATS.GATHER_FIXED_OBJECTS_STATS (null);


13) Check externa, and internal connectivity (using CMAN and SCAN IP)
14) Check wallet status is open
15) Check the List of account status should be Open 
