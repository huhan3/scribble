---
layout: post
title: Z/OS WebSphere Message Broker V7 Setup
date: 2014-05-26 16:40:31
disqus: y
---

```
The information in this data set applies to:
5655-V60 WebSphere Message Broker V7.0.0
```

**FMID HWMB700 WebSphere Message Broker**

1. USS (OMVS) customization in USER.PARMLIB(BPXPRM01).
Set the UNIX System Services parameters as below:
  A. MAXCORESIZE(2147483647)
  B. MAXCPUTIME(2147483647)
  C. MAXASSIZE >1073741824. A minimum value of 393216000 bytes
     is required.
  D. MAXTHREADS and MAXTHREADTASKS.
     The value of MAXTHREADS and MAXTHREADTASKS depends on your
     application.
     To calculate the value needed for each message flow:
     Multiply the number of input nodes by the number of instances
     (additional threads +1).
     Sum the values of all message flows, then add 10 to the sum.
     Add to the sum the number of threads used for each HTTP listener.
  E. IPCSEMNIDS
     You must set IPCSEMNIDS to a value four times the number of
     potential execution group address spaces on a system.
  F. IPCSHMNIDS
     You must set IPCSEMNIDS to a value that exceeds the number of
     potential execution group address spaces on a system.
  G. IPCSHMNSEGS
     You must set IPCSHMNSEGS to a value that exceeds the potential
     number of execution groups for each broker.

2. Allow at least 50 MB of space of /tmp, default,or TMPDIR defined.

3. APF the following data set in USER.PARMLIB(PROG01).
APF ADD DSNAME(BIP.V7R0M0.SBIPAUTH)                      VOLUME(PP0G2P)
and activate the setting in console by /set prog=01.

4. (Option) Shared-Library Programs.
If you plan to deploy to more than one execution group on z/OS, the
amount of storage required by the execution group address spaces can be
reduced by setting the shared-library extended attribute on the
following files:
     /usr/lpp/mqsi/bin/*
     /usr/lpp/mqsi/lil/*
     /usr/lpp/mqsi/lib/*
     /usr/lpp/mqsi/lib/wbirf/*
     /usr/lpp/mqsi/lib/wbimb/*
To set the shared-library attribute, use the extattr command with
+1 option.
To find out if the shared-library extended attribute has been set,
use the ls -E command.

5. MQ Planning.
  A. Your queue manager must have a dead-letter queue.
     Check the dead-letter queue: /+M71A DIS QMGR DEADQ;
     Check the queue exist:  /+M71A DIS QL(M71A.DEAD.QUEUE) STGCLASS
     Check the STGCLASS: /+M71A DIS STGCLASS(DEFAULT)
  B. Mount MQ JMS feature on /usr/lpp/mqm/V7R0M0.
     In USER.PARMLIB(BPXPRM01),
     MOUNT FILESYSTEM('CSQ.V7R0M1.SCSQHFS')
           TYPE(HFS) MODE(READ)
           MOUNTPOINT('/usr/lpp/mqm/V7R0M0')

6. (Option) RRS
  Ensure it is configured and active on your system, because
  your broker cannot connect to DB2 unless RRS is active.

7. Java
Java version 1.6 (64-bit) is required and is mounted by default via
SYS1.PARMLIB(BPXPRM00).
  MOUNT FILESYSTEM('AJV.V6R0M064.HFS')
        TYPE(HFS)
        MODE(READ)
        MOUNTPOINT('/usr/lpp/java/J6.0_64')

8. Add WMB User
  Use job COMMON.JCL(ADDUSER) to add userid MvxiBRK where Mvxi
  is the MQ queue manager name.

9. Create working directory.
  Create directory /var/wmqi/MvxiBRK
  where Mvxi is the MQ queue manager name.  Change the
  permission bits to 775 and owner:group to MvxiBRK:OMVS for
  directory MvxiBRK.

10. RACF
Edit and run job BIP.V7R0M0.#INFO(RACF)

11. Update and submit BIP.V7R0M0.#INFO(ALLOCATE) to allocate
    the working data sets.

12. Mount File System
  MOUNT FILESYSTEM('BIP.V7R0M0.HFS')
        TYPE(HFS)
        MODE(READ)
        MOUNTPOINT('/usr/lpp/mqsi/V7R0M0')

  MOUNT FILESYSTEM('WMQI.MvxiBRK.HFS')
        TYPE(HFS)
        MODE(RDWR)
        MOUNTPOINT('/var/wmqi/MvxiBRK')

13. lil directory
  Create directory /var/wmqi/MvxiBRK/lil.  Change permission bits to
  775 and owner:group to MvxiBRK:OMVS.

14. Customize the installation JCL in WMQI.MvxiBRK.CNTL.
  A. Enter the following:
     TSO ALTLIB ACTIVATE APPLICATION(EXEC) DA('WMQI.MvxiBRK.CNTL')
     This must be done in the ISPF session/split screen where you will
     be modifying the JCL.

  B. Update WMQI.MvxiBRK.CNTL(MvxiEDIT).
  /*********************************************************************
  "change ++INSTALL++ install_value                        all"
  "change ++COMPONENTDIRECTORY++ compdir_value             all"
  "change ++COMPONENTNAME++ MQ01BRK                        all"
  "change ++HOME++ /u/mq01brk                              all"
  "change ++OPTIONS++ options_value                        all"
  "change ++LOCALE++ C                                     all"
  "change ++TIMEZONE++ GMT0BST                             all"
  "change ++JAVA++ /usr/lpp/java/IBM/J1.6                  all"
  "change ++WMQHLQ++ MQM.V701                              all"
  "change ++LANGLETTER++ E                                 all"
  "change ++QUEUEMANAGER++ MQ01                            all"
  "change ++COMPONENTDATASET++ componentdataset_value      all"
  "change ++STARTEDTASKNAME++ MQ01BRK                      all"
  "change ++MQPATH++ /usr/lpp/mqm                          all"
  /*********************************************************************
  /* Database nodes and mqsimigratecomponents (BIPMGCMP) require DB2
  /* in order to connect to a datasource.
  /*********************************************************************
  /*"change ++DB2HLQ++ SYS2.DB2.V910                         all"
  /*"change ++DB2RUNLIB++ runlib_value                       all"
  /*********************************************************************

  C. Edit each member in WMQI.MvxiBRK.CNTL, except MvxiEDIT
     and enter MvxiEDIT on the command line to customize the
     the installation JCL.

15. Define the environment file.
  A. Edit WMQI.MvxiBRK.CNTL(BIPBPROF).  Change
       export MQSI_LILPATH=$MQSI_FILEPATH/lil
     to
       export MQSI_LILPATH=$MQSI_FILEPATH/lil:/var/wmqi/MvxiBRK/lil

  B. Edit and run WMQI.MvxiBRK.CNTL(BIPGEN).  Update the job card
     MSGCLASS=R,USER=MvxiBRK,PASSWORD=TIVMVS

  Refer to BIP.V7R0M0.SBIPSAMP(BIPBPROF) in detail.

16. Create the Broker component.
  MQ must be running. Job WMQI.MvxiBRK.CNTL(BIPCRBK) must be run 2
  times as beow:
  1. First run of BIPCRBK:
     - Update the JOB card: MSGCLASS=R,USER=MvxiBRK,PASSWORD=TIVMVS
     - change ++OPTIONS++ to -1

  2. Second run of BIPCRBK:
     - Update the JOB card: MSGCLASS=R,USER=IBMUSER,PASSWORD=TIVMVS
     - change -1 to -2

17. DB2. Option for WMB V7R0M0.

18. Copy WMQI.MvxiBRK.CNTL(MvxiBRK) to USER.PROCLIB.

19. Start the WebSphere Message Broker
  S MvxiBRK

20. Validation.
  Use command mqsilist to display brokers defined.

21. Check Operation Mode.
  Use command mqsimode,
  e.g.: mqsimode -i localhost -p 1414 -q M71QMGR
