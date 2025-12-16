---
date: '2025-11-13'
draft: false
title: '[MySQL 8.4] Error Log Enhanced'
description: Error Log Enhanced
categories: databases
tags:
  - mysql
  - mysql8.4
---

{{< lead >}}
這是 [What Is New in MySQL 8.4 since MySQL 8.0](https://dev.mysql.com/doc/refman/8.4/en/mysql-nutshell.html) 的[系列文](tags/mysql8.4/)，<br>
會針對部分我覺得跟日常維運有關的更動特別研究，<br>
其餘的就當故事書，看過知道即可
{{< /lead >}}

## Introduction

Logging of the shutdown process was enhanced, with the addition of startup and shutdown messages for the MySQL server, plugins, and components. Such messages are now also logged for closing connections. These additions should facilitate troubleshooting and debugging problems, particularly in the event that the server takes an excessively long time to shut down.

For more information, see Section 7.4.2, "[The Error Log](https://dev.mysql.com/doc/refman/8.4/en/error-log.html)".

---

**Additions to server startup and shutdown messages.**

Added the following types of messages to the server startup and shutdown processes as noted in this list:

* Start and end messages for server initialization when the server is started with `--initialize` or `--initialize-insecure`; these are in addition to and distinct from those shown during normal server startup and shutdown.
* Start and end messages for InnoDB initialization.
* Start and end messages for init file execution during server initialization.
* Start and end messages for for execution of compiled-in statements during server initialization.
* Start and end mesages for crash recovery during server startup (if crash recovery occurs).
* Start and end messages for initialization of dynamic plugins during server startup.
* Start and end messages for compoenents initialization step (apparent during server startup).
* Messages for shutdown of replica threads, as well as graceful and forceful shutdown of connection threads, during server shutdown.
* Start and end messages for shutdown of plugins and components during server shutdown.
* Exit code (return value) information with shutdown messages during initialization or server shutdown and end)

In addition, if the server was built using `WITH_SYSTEMD`, the server now includes every systemd message in the error log.

> 以上引用至官方原文

## Summary

看完的感想只有一句，<br>
**有用，但不多** :joy:<br>

主要是加強了 Error Log 的詳細程度，<br>
在 `Startup` 以及 `Shutdown` 等階段有紀錄更詳細的資訊<br>

請參考以下差異

### Startup

#### MySQL 8.4

```
2025-11-10T11:46:28.906838+08:00 0 [Note] [MY-013930] [Server] systemd notify: STATUS=Server startup in progress
2025-11-10T11:46:28.907406+08:00 0 [System] [MY-015015] [Server] MySQL Server - start.
2025-11-10T11:46:29.132294+08:00 0 [Warning] [MY-010097] [Server] Insecure configuration for --secure-log-path: Current value does not restrict location of generated files. Consider setting it to a valid, non-empty path.
2025-11-10T11:46:29.132341+08:00 0 [Note] [MY-013932] [Server] BuildID[sha1]=862ccd8e790deea6de418ff7f6cd35f12b7bcb65
2025-11-10T11:46:29.132346+08:00 0 [Note] [MY-010949] [Server] Basedir set to /usr/.
2025-11-10T11:46:29.132356+08:00 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.4.6-6) starting as process 14556
2025-11-10T11:46:29.140143+08:00 0 [Note] [MY-012366] [InnoDB] Using Linux native AIO
2025-11-10T11:46:29.140429+08:00 0 [Note] [MY-010747] [Server] Plugin 'FEDERATED' is disabled.
2025-11-10T11:46:29.140514+08:00 0 [Note] [MY-010747] [Server] Plugin 'ndbcluster' is disabled.
2025-11-10T11:46:29.140538+08:00 0 [Note] [MY-010747] [Server] Plugin 'ndbinfo' is disabled.
2025-11-10T11:46:29.140555+08:00 0 [Note] [MY-010747] [Server] Plugin 'ndb_transid_mysql_connection_map' is disabled.
2025-11-10T11:46:29.140636+08:00 0 [Note] [MY-010747] [Server] Plugin 'mysql_native_password' is disabled.
2025-11-10T11:46:29.141921+08:00 1 [Note] [MY-013930] [Server] systemd notify: STATUS=InnoDB initialization in progress
2025-11-10T11:46:29.141982+08:00 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2025-11-10T11:46:29.142017+08:00 1 [Note] [MY-013546] [InnoDB] Atomic write enabled
2025-11-10T11:46:29.142051+08:00 1 [Note] [MY-012932] [InnoDB] PUNCH HOLE support available
2025-11-10T11:46:29.142074+08:00 1 [Note] [MY-012944] [InnoDB] Uses event mutexes
2025-11-10T11:46:29.142091+08:00 1 [Note] [MY-012945] [InnoDB] GCC builtin __atomic_thread_fence() is used for memory barrier
2025-11-10T11:46:29.142108+08:00 1 [Note] [MY-012948] [InnoDB] Compressed tables use zlib 1.3.1
2025-11-10T11:46:29.145247+08:00 1 [Note] [MY-012951] [InnoDB] Using hardware accelerated crc32 and polynomial multiplication.
2025-11-10T11:46:29.145710+08:00 1 [Note] [MY-012203] [InnoDB] Directories to scan './'
2025-11-10T11:46:29.145779+08:00 1 [Note] [MY-012204] [InnoDB] Scanning './'
2025-11-10T11:46:29.369955+08:00 1 [Note] [MY-012208] [InnoDB] Completed space ID check of 6 files.
2025-11-10T11:46:29.371608+08:00 1 [Note] [MY-012955] [InnoDB] Initializing buffer pool, total size = 5.250000G, instances = 1, chunk size =128.000000M
2025-11-10T11:46:29.371656+08:00 1 [Note] [MY-011872] [InnoDB] Setting NUMA memory policy to MPOL_INTERLEAVE
2025-11-10T11:46:31.289344+08:00 1 [Note] [MY-011874] [InnoDB] Setting NUMA memory policy to MPOL_DEFAULT
2025-11-10T11:46:31.289467+08:00 1 [Note] [MY-012957] [InnoDB] Completed initialization of buffer pool
2025-11-10T11:46:31.325996+08:00 0 [Note] [MY-011952] [InnoDB] If the mysqld execution user is authorized, page cleaner thread priority can be changed. See the man page of setpriority().
2025-11-10T11:46:31.358951+08:00 1 [Note] [MY-013532] [InnoDB] Using './#ib_16384_0.dblwr' for doublewrite
2025-11-10T11:46:31.401994+08:00 1 [Note] [MY-013532] [InnoDB] Using './#ib_16384_1.dblwr' for doublewrite
2025-11-10T11:46:31.426588+08:00 1 [Note] [MY-013566] [InnoDB] Double write buffer files: 2, --innodb-doublewrite-files=2, segments_per_file=2
2025-11-10T11:46:31.426665+08:00 1 [Note] [MY-013565] [InnoDB] Double write buffer pages per instance: 128
2025-11-10T11:46:31.426704+08:00 1 [Note] [MY-013532] [InnoDB] Using './#ib_16384_0.dblwr' for doublewrite
2025-11-10T11:46:31.426737+08:00 1 [Note] [MY-013532] [InnoDB] Using './#ib_16384_1.dblwr' for doublewrite
2025-11-10T11:46:31.501928+08:00 1 [Note] [MY-013883] [InnoDB] The latest found checkpoint is at lsn = 30303787 in redo log file ./#innodb_redo/#ib_redo0.
2025-11-10T11:46:31.502072+08:00 1 [Note] [MY-013086] [InnoDB] Starting to parse redo log at lsn = 30303787, whereas checkpoint_lsn = 30303787 and start_lsn = 30303744
2025-11-10T11:46:31.515397+08:00 1 [Note] [MY-013083] [InnoDB] Log background threads are being started...
2025-11-10T11:46:31.558635+08:00 1 [Note] [MY-012532] [InnoDB] Applying a batch of 0 redo log records ...
2025-11-10T11:46:31.558720+08:00 1 [Note] [MY-012535] [InnoDB] Apply batch completed!
2025-11-10T11:46:31.560227+08:00 1 [Note] [MY-013252] [InnoDB] Using undo tablespace './undo_001'.
2025-11-10T11:46:31.581489+08:00 1 [Note] [MY-013252] [InnoDB] Using undo tablespace './undo_002'.
2025-11-10T11:46:31.588080+08:00 1 [Note] [MY-012910] [InnoDB] Opened 2 existing undo tablespaces.
2025-11-10T11:46:31.588224+08:00 1 [Note] [MY-011980] [InnoDB] GTID recovery trx_no: 3601
2025-11-10T11:46:31.809137+08:00 1 [Note] [MY-013776] [InnoDB] Parallel initialization of rseg complete
2025-11-10T11:46:31.809229+08:00 1 [Note] [MY-013777] [InnoDB] Time taken to initialize rseg using 2 thread: 221 ms.
2025-11-10T11:46:31.809334+08:00 1 [Note] [MY-012923] [InnoDB] Creating shared tablespace for temporary tables
2025-11-10T11:46:31.810230+08:00 1 [Note] [MY-012265] [InnoDB] Setting file '/var/lib/mysql/ibtmp1' size to 12 MB. Physically writing the file full; Please wait ...
2025-11-10T11:46:31.845956+08:00 1 [Note] [MY-012266] [InnoDB] File '/var/lib/mysql/ibtmp1' size is now 12 MB.
2025-11-10T11:46:31.846143+08:00 1 [Note] [MY-013627] [InnoDB] Scanning temp tablespace dir:'./#innodb_temp/'
2025-11-10T11:46:31.865616+08:00 1 [Note] [MY-013018] [InnoDB] Created 128 and tracked 128 new rollback segment(s) in the temporary tablespace. 128 are now active.
2025-11-10T11:46:31.875582+08:00 1 [Note] [MY-012976] [InnoDB] Percona XtraDB (http://www.percona.com) 8.4.6-6 started; log sequence number 30303797
2025-11-10T11:46:31.876880+08:00 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2025-11-10T11:46:31.876936+08:00 1 [Note] [MY-013930] [Server] systemd notify: STATUS=InnoDB initialization successful
2025-11-10T11:46:31.958653+08:00 1 [Note] [MY-011089] [Server] Data dictionary restarting version '80300'.
2025-11-10T11:46:32.076265+08:00 1 [Note] [MY-013930] [Server] systemd notify: STATUS=InnoDB crash recovery in progress
2025-11-10T11:46:32.102885+08:00 1 [Note] [MY-012357] [InnoDB] Reading DD tablespace files
2025-11-10T11:46:32.103635+08:00 1 [Note] [MY-012356] [InnoDB] Scanned 8 tablespaces. Validated 8.
2025-11-10T11:46:32.117174+08:00 1 [Note] [MY-013930] [Server] systemd notify: STATUS=InnoDB crash recovery successful
2025-11-10T11:46:32.130646+08:00 1 [Note] [MY-010006] [Server] Using data dictionary with version '80300'.
2025-11-10T11:46:32.131943+08:00 0 [Note] [MY-013930] [Server] systemd notify: STATUS=Initialization of dynamic plugins in progress
2025-11-10T11:46:32.140199+08:00 0 [Note] [MY-011332] [Server] Plugin mysqlx reported: 'IPv6 is available'
2025-11-10T11:46:32.141815+08:00 0 [Note] [MY-011323] [Server] Plugin mysqlx reported: 'X Plugin ready for connections. bind-address: '::' port: 33060'
2025-11-10T11:46:32.141868+08:00 0 [Note] [MY-011323] [Server] Plugin mysqlx reported: 'X Plugin ready for connections. socket: '/var/lib/mysql/mysqlx.sock''
2025-11-10T11:46:32.142041+08:00 0 [Note] [MY-013930] [Server] systemd notify: STATUS=Initialization of dynamic plugins successful
2025-11-10T11:46:32.191009+08:00 0 [Note] [MY-010902] [Server] Thread priority attribute setting in Resource Group SQL shall be ignored due to unsupported platform or insufficient privilege.
2025-11-10T11:46:32.194618+08:00 0 [Note] [MY-010856] [Server] Failed to open the crashed binlog file when source server is recovering it.
2025-11-10T11:46:32.203368+08:00 0 [Note] [MY-013911] [Server] Crash recovery finished in binlog engine. No attempts to commit, rollback or prepare any transactions.
2025-11-10T11:46:32.203423+08:00 0 [Note] [MY-013911] [Server] Crash recovery finished in InnoDB engine. No attempts to commit, rollback or prepare any transactions.
2025-11-10T11:46:32.205909+08:00 0 [Note] [MY-012487] [InnoDB] DDL log recovery : begin
2025-11-10T11:46:32.206890+08:00 0 [Note] [MY-012488] [InnoDB] DDL log recovery : end
2025-11-10T11:46:32.211934+08:00 0 [Note] [MY-011946] [InnoDB] Loading buffer pool(s) from /var/lib/mysql/ib_buffer_pool
2025-11-10T11:46:32.223853+08:00 0 [Note] [MY-012922] [InnoDB] Waiting for purge to start
2025-11-10T11:46:32.234168+08:00 0 [Note] [MY-011946] [InnoDB] Buffer pool(s) load completed at 251110 11:46:32
2025-11-10T11:46:32.288420+08:00 0 [Note] [MY-010303] [Server] Skipping generation of SSL certificates as options related to SSL are specified.
2025-11-10T11:46:32.289195+08:00 0 [Warning] [MY-010068] [Server] CA certificate /etc/mysql/ssl/ca-cert.pem is self signed.
2025-11-10T11:46:32.289253+08:00 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2025-11-10T11:46:32.289486+08:00 0 [Note] [MY-010308] [Server] Skipping generation of RSA key pair through --sha256_password_auto_generate_rsa_keys as key files are present in data directory.
2025-11-10T11:46:32.289681+08:00 0 [Note] [MY-010307] [Server] Skipping generation of RSA key pair through --caching_sha2_password_auto_generate_rsa_keys as options related to RSA keys are specified.
2025-11-10T11:46:32.289844+08:00 0 [Note] [MY-010252] [Server] Server hostname (bind-address): '*'; port: 3306
2025-11-10T11:46:32.289911+08:00 0 [Note] [MY-010253] [Server] IPv6 is available.
2025-11-10T11:46:32.289931+08:00 0 [Note] [MY-010264] [Server]   - '::' resolves to '::';
2025-11-10T11:46:32.289955+08:00 0 [Note] [MY-010251] [Server] Server socket created on IP: '::'.
2025-11-10T11:46:32.291909+08:00 0 [Note] [MY-013930] [Server] systemd notify: STATUS=Components initialization in progress
2025-11-10T11:46:32.292734+08:00 0 [Note] [MY-013930] [Server] systemd notify: STATUS=Components initialization successful
2025-11-10T11:46:32.324976+08:00 0 [Note] [MY-011025] [Repl] Failed to start replica threads for channel ''.
2025-11-10T11:46:32.328585+08:00 6 [Note] [MY-010051] [Server] Event Scheduler: scheduler thread started with id 6
2025-11-10T11:46:32.328860+08:00 0 [Note] [MY-011240] [Server] Plugin mysqlx reported: 'Using SSL configuration from MySQL Server'
2025-11-10T11:46:32.329288+08:00 0 [Note] [MY-011243] [Server] Plugin mysqlx reported: 'Using OpenSSL for TLS connections'
2025-11-10T11:46:32.329498+08:00 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.4.6-6'  socket: '/var/lib/mysql/mysql.sock'  port: 3306  Percona Server (GPL), Release 6, Revision 9927a2fb.
2025-11-10T11:46:32.329500+08:00 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060, socket: /var/lib/mysql/mysqlx.sock
2025-11-10T11:46:32.330079+08:00 0 [Note] [MY-013930] [Server] systemd notify: READY=1 STATUS=Server is operational MAIN_PID=14556
```

#### MySQL 8.0

```
2025-11-10T13:59:10.992215+08:00 0 [Warning] [MY-010097] [Server] Insecure configuration for --secure-log-path: Current value does not restrict location of generated files. Consider setting it to a valid, non-empty path.
2025-11-10T13:59:10.992267+08:00 0 [Warning] [MY-010918] [Server] 'default_authentication_plugin' is deprecated and will be removed in a future release. Please use authentication_policy instead.
2025-11-10T13:59:10.992270+08:00 0 [Note] [MY-013932] [Server] BuildID[sha1]=828db7db80124cd9a47f09b8a4a6dc29ead7ca38
2025-11-10T13:59:10.992277+08:00 0 [Note] [MY-010949] [Server] Basedir set to /usr/.
2025-11-10T13:59:10.992284+08:00 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.33-25) starting as process 5191
2025-11-10T13:59:10.996222+08:00 0 [Note] [MY-012366] [InnoDB] Using Linux native AIO
2025-11-10T13:59:10.996308+08:00 0 [Warning] [MY-012367] [InnoDB] Option innodb_dedicated_server is ignored for innodb_flush_methodbecause innodb_flush_method=O_DIRECT is specified explicitly.
2025-11-10T13:59:10.996328+08:00 0 [Warning] [MY-012358] [InnoDB] Option innodb_dedicated_server is ignored for innodb_buffer_pool_size because innodb_buffer_pool_size=3221225472 is specified explicitly.
2025-11-10T13:59:10.996463+08:00 0 [Note] [MY-010747] [Server] Plugin 'FEDERATED' is disabled.
2025-11-10T13:59:10.996528+08:00 0 [Note] [MY-010747] [Server] Plugin 'ndbcluster' is disabled.
2025-11-10T13:59:10.996545+08:00 0 [Note] [MY-010747] [Server] Plugin 'ndbinfo' is disabled.
2025-11-10T13:59:10.996553+08:00 0 [Note] [MY-010747] [Server] Plugin 'ndb_transid_mysql_connection_map' is disabled.
2025-11-10T13:59:10.997768+08:00 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2025-11-10T13:59:10.997827+08:00 1 [Note] [MY-013547] [InnoDB] Atomic write disabled
2025-11-10T13:59:10.997860+08:00 1 [Note] [MY-012932] [InnoDB] PUNCH HOLE support available
2025-11-10T13:59:10.997879+08:00 1 [Note] [MY-012944] [InnoDB] Uses event mutexes
2025-11-10T13:59:10.997886+08:00 1 [Note] [MY-012945] [InnoDB] GCC builtin __atomic_thread_fence() is used for memory barrier
2025-11-10T13:59:10.997900+08:00 1 [Note] [MY-012948] [InnoDB] Compressed tables use zlib 1.2.13
2025-11-10T13:59:11.000847+08:00 1 [Note] [MY-012951] [InnoDB] Using hardware accelerated crc32 and polynomial multiplication.
2025-11-10T13:59:11.001216+08:00 1 [Note] [MY-012203] [InnoDB] Directories to scan './'
2025-11-10T13:59:11.001259+08:00 1 [Note] [MY-012204] [InnoDB] Scanning './'
2025-11-10T13:59:11.041387+08:00 1 [Note] [MY-012208] [InnoDB] Completed space ID check of 5 files.
2025-11-10T13:59:11.042588+08:00 1 [Note] [MY-012955] [InnoDB] Initializing buffer pool, total size = 3.000000G, instances = 2, chunk size =128.000000M
2025-11-10T13:59:11.128997+08:00 1 [Note] [MY-012957] [InnoDB] Completed initialization of buffer pool
2025-11-10T13:59:11.164980+08:00 0 [Note] [MY-011952] [InnoDB] If the mysqld execution user is authorized, page cleaner and LRU manager thread priority can be changed. See the man page of setpriority().
2025-11-10T13:59:11.279900+08:00 1 [Note] [MY-013883] [InnoDB] The latest found checkpoint is at lsn = 32109818 in redo log file ./#innodb_redo/#ib_redo0.
2025-11-10T13:59:11.279993+08:00 1 [Note] [MY-013086] [InnoDB] Starting to parse redo log at lsn = 32109600, whereas checkpoint_lsn = 32109818 and start_lsn = 32109568
2025-11-10T13:59:11.311059+08:00 1 [Note] [MY-013083] [InnoDB] Log background threads are being started...
2025-11-10T13:59:11.416434+08:00 1 [Note] [MY-012532] [InnoDB] Applying a batch of 0 redo log records ...
2025-11-10T13:59:11.416495+08:00 1 [Note] [MY-012535] [InnoDB] Apply batch completed!
2025-11-10T13:59:11.417728+08:00 1 [Note] [MY-013252] [InnoDB] Using undo tablespace './undo_001'.
2025-11-10T13:59:11.444628+08:00 1 [Note] [MY-013252] [InnoDB] Using undo tablespace './undo_002'.
2025-11-10T13:59:11.450696+08:00 1 [Note] [MY-012910] [InnoDB] Opened 2 existing undo tablespaces.
2025-11-10T13:59:11.450745+08:00 1 [Note] [MY-011980] [InnoDB] GTID recovery trx_no: 2065
2025-11-10T13:59:12.022381+08:00 1 [Note] [MY-013776] [InnoDB] Parallel initialization of rseg complete
2025-11-10T13:59:12.022448+08:00 1 [Note] [MY-013777] [InnoDB] Time taken to initialize rseg using 2 thread: 571710 ms.
2025-11-10T13:59:12.022538+08:00 1 [Note] [MY-012923] [InnoDB] Creating shared tablespace for temporary tables
2025-11-10T13:59:12.022640+08:00 1 [Note] [MY-012265] [InnoDB] Setting file '/var/lib/mysql/ibtmp1' size to 12 MB. Physically writing the file full; Please wait ...
2025-11-10T13:59:12.057665+08:00 1 [Note] [MY-012266] [InnoDB] File '/var/lib/mysql/ibtmp1' size is now 12 MB.
2025-11-10T13:59:12.057770+08:00 1 [Note] [MY-013627] [InnoDB] Scanning temp tablespace dir:'./#innodb_temp/'
2025-11-10T13:59:12.069806+08:00 1 [Note] [MY-013018] [InnoDB] Created 128 and tracked 128 new rollback segment(s) in the temporary tablespace. 128 are now active.
2025-11-10T13:59:12.077433+08:00 1 [Note] [MY-012976] [InnoDB] Percona XtraDB (http://www.percona.com) 8.0.33-25 started; log sequence number 32109828
2025-11-10T13:59:12.078046+08:00 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2025-11-10T13:59:12.086557+08:00 1 [Note] [MY-011089] [Server] Data dictionary restarting version '80023'.
2025-11-10T13:59:12.281069+08:00 1 [Note] [MY-012357] [InnoDB] Reading DD tablespace files
2025-11-10T13:59:12.281617+08:00 1 [Note] [MY-012356] [InnoDB] Scanned 7 tablespaces. Validated 7.
2025-11-10T13:59:12.302157+08:00 1 [Note] [MY-010006] [Server] Using data dictionary with version '80023'.
2025-11-10T13:59:12.308733+08:00 0 [Note] [MY-011332] [Server] Plugin mysqlx reported: 'IPv6 is available'
2025-11-10T13:59:12.309917+08:00 0 [Note] [MY-011323] [Server] Plugin mysqlx reported: 'X Plugin ready for connections. bind-address: '::' port: 33060'
2025-11-10T13:59:12.309944+08:00 0 [Note] [MY-011323] [Server] Plugin mysqlx reported: 'X Plugin ready for connections. socket: '/var/lib/mysql/mysqlx.sock''
2025-11-10T13:59:12.330315+08:00 0 [Note] [MY-010902] [Server] Thread priority attribute setting in Resource Group SQL shall be ignored due to unsupported platform or insufficient privilege.
2025-11-10T13:59:12.334603+08:00 0 [Note] [MY-010856] [Server] Failed to open the crashed binlog file when source server is recovering it.
2025-11-10T13:59:12.343184+08:00 0 [Note] [MY-013911] [Server] Crash recovery finished in binlog engine. No attempts to commit, rollback or prepare any transactions.
2025-11-10T13:59:12.343214+08:00 0 [Note] [MY-013911] [Server] Crash recovery finished in InnoDB engine. No attempts to commit, rollback or prepare any transactions.
2025-11-10T13:59:12.345475+08:00 0 [Note] [MY-012487] [InnoDB] DDL log recovery : begin
2025-11-10T13:59:12.346228+08:00 0 [Note] [MY-012488] [InnoDB] DDL log recovery : end
2025-11-10T13:59:12.350893+08:00 0 [Note] [MY-011946] [InnoDB] Loading buffer pool(s) from /var/lib/mysql/ib_buffer_pool
2025-11-10T13:59:12.351226+08:00 0 [Note] [MY-011946] [InnoDB] Buffer pool(s) load completed at 251110 13:59:12
2025-11-10T13:59:12.375527+08:00 0 [Note] [MY-010303] [Server] Skipping generation of SSL certificates as options related to SSL are specified.
2025-11-10T13:59:12.376332+08:00 0 [Warning] [MY-010068] [Server] CA certificate /etc/mysql/ssl/ca-cert.pem is self signed.
2025-11-10T13:59:12.376367+08:00 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2025-11-10T13:59:12.376552+08:00 0 [Note] [MY-010308] [Server] Skipping generation of RSA key pair through --sha256_password_auto_generate_rsa_keys as key files are present in data directory.
2025-11-10T13:59:12.376710+08:00 0 [Note] [MY-010308] [Server] Skipping generation of RSA key pair through --caching_sha2_password_auto_generate_rsa_keys as key files are present in data directory.
2025-11-10T13:59:12.376832+08:00 0 [Note] [MY-010252] [Server] Server hostname (bind-address): '*'; port: 3306
2025-11-10T13:59:12.376879+08:00 0 [Note] [MY-010253] [Server] IPv6 is available.
2025-11-10T13:59:12.376894+08:00 0 [Note] [MY-010264] [Server]   - '::' resolves to '::';
2025-11-10T13:59:12.376909+08:00 0 [Note] [MY-010251] [Server] Server socket created on IP: '::'.
2025-11-10T13:59:12.411001+08:00 0 [Note] [MY-011025] [Repl] Failed to start replica threads for channel ''.
2025-11-10T13:59:12.414065+08:00 5 [Note] [MY-010051] [Server] Event Scheduler: scheduler thread started with id 5
2025-11-10T13:59:12.414293+08:00 0 [Note] [MY-011240] [Server] Plugin mysqlx reported: 'Using SSL configuration from MySQL Server'
2025-11-10T13:59:12.414852+08:00 0 [Note] [MY-011243] [Server] Plugin mysqlx reported: 'Using OpenSSL for TLS connections'
2025-11-10T13:59:12.415018+08:00 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.33-25'  socket: '/var/lib/mysql/mysql.sock'  port: 3306  Percona Server (GPL), Release 25, Revision 60c9e2c5.
2025-11-10T13:59:12.418069+08:00 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060, socket: /var/lib/mysql/mysqlx.sock
```

#### 差異點

![vimdiff-startup](vimdiff-startup.png)

```
# MySQL 8.4
[Server] systemd notify: STATUS=InnoDB initialization successful
[Server] Data dictionary restarting version '80300'.
[Server] systemd notify: STATUS=InnoDB crash recovery in progress
[InnoDB] Reading DD tablespace files
[InnoDB] Scanned 8 tablespaces. Validated 8.
[Server] systemd notify: STATUS=InnoDB crash recovery successful

# MySQL 8.0
[Server] Data dictionary restarting version '80023'.
[InnoDB] Reading DD tablespace files
[InnoDB] Scanned 7 tablespaces. Validated 7.

```

類似上述這樣，多了一些 `[Server] Status`<br>
在出現問題時，可以更加明確的知道是在哪一步驟出現錯誤

### Shutdown

#### MySQL 8.4

```
2025-11-10T11:46:24.969675+08:00 0 [System] [MY-013172] [Server] Received SHUTDOWN from user <via user signal>. Shutting down mysqld (Version: 8.4.6-6).
2025-11-10T11:46:24.970821+08:00 0 [Note] [MY-013930] [Server] systemd notify: STOPPING=1 STATUS=Server shutdown in progress
2025-11-10T11:46:24.975792+08:00 0 [Note] [MY-015023] [Server] MySQL Server: Closing Connections - start.
2025-11-10T11:46:24.975924+08:00 0 [Note] [MY-013930] [Server] systemd notify: STATUS=Graceful shutdown of connections in progress
2025-11-10T11:46:24.976468+08:00 0 [Note] [MY-010067] [Server] Giving 3 client threads a chance to die gracefully
2025-11-10T11:46:24.976524+08:00 0 [Note] [MY-010117] [Server] Shutting down replica threads
2025-11-10T11:46:24.976551+08:00 0 [Note] [MY-013930] [Server] systemd notify: STATUS=Shutdown of replica threads in progress
2025-11-10T11:46:24.976579+08:00 0 [Note] [MY-010054] [Server] Event Scheduler: Killing the scheduler thread, thread id 6
2025-11-10T11:46:24.976603+08:00 0 [Note] [MY-010050] [Server] Event Scheduler: Waiting for the scheduler thread to reply
2025-11-10T11:46:24.976929+08:00 0 [Note] [MY-010048] [Server] Event Scheduler: Stopped
2025-11-10T11:46:26.977108+08:00 0 [Note] [MY-010118] [Server] Forcefully disconnecting 1 remaining clients
2025-11-10T11:46:26.977241+08:00 0 [Note] [MY-013930] [Server] systemd notify: STATUS=Forceful shutdown of connections in progress
2025-11-10T11:46:26.977279+08:00 0 [Warning] [MY-010909] [Server] /usr/sbin/mysqld: Forcing close of thread 927  user: 'xxxxx'.
2025-11-10T11:46:26.977406+08:00 0 [Note] [MY-010043] [Server] Event Scheduler: Purging the queue. 0 events
2025-11-10T11:46:26.977441+08:00 0 [Note] [MY-015026] [Server] Waiting for forceful disconnection of 1 thread(s) to end.
2025-11-10T11:46:26.977469+08:00 0 [Note] [MY-014072] [Server] Waiting for THDs in partition 7 to be closed, 1 still left. 1 THDs left in total for all partitions.
2025-11-10T11:46:26.978294+08:00 0 [Note] [MY-015024] [Server] MySQL Server: Closing Connections - end.
2025-11-10T11:46:26.978349+08:00 0 [Note] [MY-013930] [Server] systemd notify: STATUS=Connection shutdown complete
2025-11-10T11:46:26.978491+08:00 0 [Note] [MY-013930] [Server] systemd notify: STATUS=Pre DD shutdown of MySQL SE plugin InnoDB in progress
2025-11-10T11:46:26.979665+08:00 0 [Note] [MY-012330] [InnoDB] FTS optimize thread exiting.
2025-11-10T11:46:27.829786+08:00 0 [Note] [MY-013930] [Server] systemd notify: STATUS=Pre DD shutdown of MySQL SE plugin InnoDB completed
2025-11-10T11:46:27.831654+08:00 0 [Note] [MY-010120] [Server] Binlog end
2025-11-10T11:46:27.834907+08:00 0 [Note] [MY-015019] [Server] MySQL Server: Plugins Shutdown - start.
2025-11-10T11:46:27.834976+08:00 0 [Note] [MY-013930] [Server] systemd notify: STATUS=Shutdown of plugins in progress
2025-11-10T11:46:27.835010+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'mysqlx'
2025-11-10T11:46:27.836708+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'mysqlx_cache_cleaner'
2025-11-10T11:46:27.836771+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'ngram'
2025-11-10T11:46:27.836796+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'BLACKHOLE'
2025-11-10T11:46:27.836839+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'ARCHIVE'
2025-11-10T11:46:27.836857+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'TempTable'
2025-11-10T11:46:27.836874+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'MRG_MYISAM'
2025-11-10T11:46:27.836892+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'MyISAM'
2025-11-10T11:46:27.836919+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_SESSION_TEMP_TABLESPACES'
2025-11-10T11:46:27.836938+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_CACHED_INDEXES'
2025-11-10T11:46:27.836955+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_VIRTUAL'
2025-11-10T11:46:27.836974+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_COLUMNS'
2025-11-10T11:46:27.836992+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_TABLESPACES'
2025-11-10T11:46:27.837012+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_INDEXES'
2025-11-10T11:46:27.837030+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_TABLESTATS'
2025-11-10T11:46:27.837052+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_TABLES'
2025-11-10T11:46:27.837070+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_FT_INDEX_TABLE'
2025-11-10T11:46:27.837086+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_FT_INDEX_CACHE'
2025-11-10T11:46:27.837103+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_FT_CONFIG'
2025-11-10T11:46:27.837120+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_FT_BEING_DELETED'
2025-11-10T11:46:27.837137+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_FT_DELETED'
2025-11-10T11:46:27.837154+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_FT_DEFAULT_STOPWORD'
2025-11-10T11:46:27.837170+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_METRICS'
2025-11-10T11:46:27.837189+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_TEMP_TABLE_INFO'
2025-11-10T11:46:27.837207+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_BUFFER_POOL_STATS'
2025-11-10T11:46:27.837224+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_BUFFER_PAGE_LRU'
2025-11-10T11:46:27.837241+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_BUFFER_PAGE'
2025-11-10T11:46:27.837260+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_CMP_PER_INDEX_RESET'
2025-11-10T11:46:27.837278+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_CMP_PER_INDEX'
2025-11-10T11:46:27.837295+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_CMPMEM_RESET'
2025-11-10T11:46:27.837312+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_CMPMEM'
2025-11-10T11:46:27.837329+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_CMP_RESET'
2025-11-10T11:46:27.837345+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_CMP'
2025-11-10T11:46:27.837362+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_TRX'
2025-11-10T11:46:27.837383+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'InnoDB'
2025-11-10T11:46:27.837426+08:00 0 [Note] [MY-013072] [InnoDB] Starting shutdown...
2025-11-10T11:46:27.837574+08:00 0 [Note] [MY-011944] [InnoDB] Dumping buffer pool(s) to /var/lib/mysql/ib_buffer_pool
2025-11-10T11:46:27.837859+08:00 0 [Note] [MY-011944] [InnoDB] Buffer pool(s) dump completed at 251110 11:46:27
2025-11-10T11:46:27.841758+08:00 0 [Note] [MY-013084] [InnoDB] Log background threads are being closed...
2025-11-10T11:46:28.766453+08:00 0 [Note] [MY-013854] [InnoDB] Bytes written to disk by DBLWR (ON): 819200
2025-11-10T11:46:28.767553+08:00 0 [Note] [MY-012980] [InnoDB] Shutdown completed; log sequence number 30303787
2025-11-10T11:46:28.768904+08:00 0 [Note] [MY-012255] [InnoDB] Removed temporary tablespace data file: "ibtmp1"
2025-11-10T11:46:28.768969+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'MEMORY'
2025-11-10T11:46:28.768999+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'CSV'
2025-11-10T11:46:28.769022+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'PERFORMANCE_SCHEMA'
2025-11-10T11:46:28.769062+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'daemon_keyring_proxy_plugin'
2025-11-10T11:46:28.769092+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'sha2_cache_cleaner'
2025-11-10T11:46:28.769114+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'caching_sha2_password'
2025-11-10T11:46:28.769135+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'sha256_password'
2025-11-10T11:46:28.769285+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'binlog'
2025-11-10T11:46:28.769312+08:00 0 [Note] [MY-015020] [Server] MySQL Server: Plugins Shutdown - end.
2025-11-10T11:46:28.769337+08:00 0 [Note] [MY-013930] [Server] systemd notify: STATUS=Shutdown of plugins complete
2025-11-10T11:46:28.773283+08:00 0 [Note] [MY-015021] [Server] MySQL Server: Components Shutdown - start.
2025-11-10T11:46:28.773313+08:00 0 [Note] [MY-013930] [Server] systemd notify: Shutdown of components in progress
2025-11-10T11:46:28.773922+08:00 0 [Note] [MY-015022] [Server] MySQL Server: Components Shutdown - end (with return value = 0).
2025-11-10T11:46:28.773944+08:00 0 [Note] [MY-013930] [Server] systemd notify: Shutdown of components successful
2025-11-10T11:46:28.774059+08:00 0 [Note] [MY-013930] [Server] systemd notify: STATUS=Server shutdown complete (with return value = 0)
2025-11-10T11:46:28.774075+08:00 0 [System] [MY-010910] [Server] /usr/sbin/mysqld: Shutdown complete (mysqld 8.4.6-6)  Percona Server (GPL), Release 6, Revision 9927a2fb.
2025-11-10T11:46:28.774082+08:00 0 [System] [MY-015016] [Server] MySQL Server - end.
```

#### MySQL 8.0

```
2025-11-10T13:59:07.831656+08:00 0 [System] [MY-013172] [Server] Received SHUTDOWN from user <via user signal>. Shutting down mysqld (Version: 8.0.33-25).
2025-11-10T13:59:07.834106+08:00 0 [Note] [MY-010067] [Server] Giving 2 client threads a chance to die gracefully
2025-11-10T13:59:07.834134+08:00 0 [Note] [MY-010117] [Server] Shutting down replica threads
2025-11-10T13:59:07.834147+08:00 0 [Note] [MY-010054] [Server] Event Scheduler: Killing the scheduler thread, thread id 5
2025-11-10T13:59:07.834161+08:00 0 [Note] [MY-010050] [Server] Event Scheduler: Waiting for the scheduler thread to reply
2025-11-10T13:59:07.834235+08:00 0 [Note] [MY-010048] [Server] Event Scheduler: Stopped
2025-11-10T13:59:09.834325+08:00 0 [Note] [MY-010118] [Server] Forcefully disconnecting 0 remaining clients
2025-11-10T13:59:09.834389+08:00 0 [Note] [MY-010043] [Server] Event Scheduler: Purging the queue. 0 events
2025-11-10T13:59:09.835670+08:00 0 [Note] [MY-012330] [InnoDB] FTS optimize thread exiting.
2025-11-10T13:59:09.939724+08:00 0 [Note] [MY-010120] [Server] Binlog end
2025-11-10T13:59:09.941776+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'mysqlx'
2025-11-10T13:59:09.942705+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'mysqlx_cache_cleaner'
2025-11-10T13:59:09.942734+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'ngram'
2025-11-10T13:59:09.942743+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'BLACKHOLE'
2025-11-10T13:59:09.942750+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'ARCHIVE'
2025-11-10T13:59:09.942757+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'TempTable'
2025-11-10T13:59:09.942762+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'MRG_MYISAM'
2025-11-10T13:59:09.942769+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'MyISAM'
2025-11-10T13:59:09.942787+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_SESSION_TEMP_TABLESPACES'
2025-11-10T13:59:09.942836+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_CACHED_INDEXES'
2025-11-10T13:59:09.942846+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_VIRTUAL'
2025-11-10T13:59:09.942853+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_COLUMNS'
2025-11-10T13:59:09.942859+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_TABLESPACES'
2025-11-10T13:59:09.942865+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_INDEXES'
2025-11-10T13:59:09.942871+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_TABLESTATS'
2025-11-10T13:59:09.942877+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_TABLES'
2025-11-10T13:59:09.942890+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_FT_INDEX_TABLE'
2025-11-10T13:59:09.942897+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_FT_INDEX_CACHE'
2025-11-10T13:59:09.942904+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_FT_CONFIG'
2025-11-10T13:59:09.942910+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_FT_BEING_DELETED'
2025-11-10T13:59:09.942916+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_FT_DELETED'
2025-11-10T13:59:09.942924+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_FT_DEFAULT_STOPWORD'
2025-11-10T13:59:09.942938+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_METRICS'
2025-11-10T13:59:09.942945+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_TEMP_TABLE_INFO'
2025-11-10T13:59:09.942951+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_BUFFER_POOL_STATS'
2025-11-10T13:59:09.942957+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_BUFFER_PAGE_LRU'
2025-11-10T13:59:09.942962+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_BUFFER_PAGE'
2025-11-10T13:59:09.942969+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_CMP_PER_INDEX_RESET'
2025-11-10T13:59:09.942975+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_CMP_PER_INDEX'
2025-11-10T13:59:09.942981+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_CMPMEM_RESET'
2025-11-10T13:59:09.942990+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_CMPMEM'
2025-11-10T13:59:09.942998+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_CMP_RESET'
2025-11-10T13:59:09.943005+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_CMP'
2025-11-10T13:59:09.943011+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'INNODB_TRX'
2025-11-10T13:59:09.943017+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'InnoDB'
2025-11-10T13:59:09.943044+08:00 0 [Note] [MY-013072] [InnoDB] Starting shutdown...
2025-11-10T13:59:09.943151+08:00 0 [Note] [MY-011944] [InnoDB] Dumping buffer pool(s) to /var/lib/mysql/ib_buffer_pool
2025-11-10T13:59:09.943401+08:00 0 [Note] [MY-011944] [InnoDB] Buffer pool(s) dump completed at 251110 13:59:09
2025-11-10T13:59:10.149053+08:00 0 [Note] [MY-013084] [InnoDB] Log background threads are being closed...
2025-11-10T13:59:10.666371+08:00 0 [Note] [MY-012980] [InnoDB] Shutdown completed; log sequence number 32109818
2025-11-10T13:59:10.666498+08:00 0 [Note] [MY-012255] [InnoDB] Removed temporary tablespace data file: "ibtmp1"
2025-11-10T13:59:10.666528+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'MEMORY'
2025-11-10T13:59:10.666541+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'CSV'
2025-11-10T13:59:10.666551+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'PERFORMANCE_SCHEMA'
2025-11-10T13:59:10.666577+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'daemon_keyring_proxy_plugin'
2025-11-10T13:59:10.666592+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'sha2_cache_cleaner'
2025-11-10T13:59:10.666602+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'caching_sha2_password'
2025-11-10T13:59:10.666612+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'sha256_password'
2025-11-10T13:59:10.666618+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'mysql_native_password'
2025-11-10T13:59:10.666748+08:00 0 [Note] [MY-010733] [Server] Shutting down plugin 'binlog'
2025-11-10T13:59:10.667428+08:00 0 [System] [MY-010910] [Server] /usr/sbin/mysqld: Shutdown complete (mysqld 8.0.33-25)  Percona Server (GPL), Release 25, Revision 60c9e2c5.
```

#### 差異點

![vimdiff-shutdown](vimdiff-shutdown.png)

```

# MySQL 8.4
[Warning] [MY-010909] [Server] /usr/sbin/mysqld: Forcing close of thread 927  user: 'xxxxx'.

# MySQL 8.0
None

```

跟 Startup 類似，也是多了一些 `[Server Status]`<br>
比較特別且有用的地方在於，Shutdown 有多了連線被關閉的資訊，<br>
這在排查一些問題或許可以帶來一些幫助。<br>
比如說有 Client 反應連線出現錯誤，但明明已經從 Connection Pool 中移除了，<br>
當下也只有把機器重啟，這時就可以看看 Error Log 中是不是有連線沒有移除乾淨，<br>
導致連線還是被送到這台機器而影響到

## Afterword

當初標記為 :star: 的原因是想了解 `filter` 以及 `sink` 的功能，<br>
後來發現這其實在 MySQL 8.0 就已經有了<br>
[WL #9323 -> Logging services: Improved error logging in 8.0](https://dev.mysql.com/worklog/task/?id=9323)<br>
[MySQL 8.0 Release Note: Error logging was rewritten to use the MySQL component architecture](https://dev.mysql.com/doc/refman/8.0/en/mysql-nutshell.html)<br>
當初 MySQL 8.0 時沒有留意到這功能，<br>
到了 MySQL 8.4 時才來熟悉它，<br>
也因為這功能跟 8.4 沒有太大關係，<br>
所以看完後就默默地把它移除 :star: 囉。<br>

感想雖然是 **有用，但不多**<br>
主要的原因是因為我們太晚知道這功能，<br>
我們目前是使用 [Loki](https://grafana.com/docs/loki/latest/) 的方式來收 Error Log，<br>
如果早點知道這功能的話，<br>
當初的決策就會是使用 `log_sink_json` 的方式來儲存 Error Log，<br>
這對後面的系統串接可以帶來很大的便利性。<br>

目前是使用 LogQL 字串比對的方式來偵測並發送告警，<br>
告警的種類目前有以下幾種

* Too Many Connections
* Dead Lock
* ERROR Log

### Too Many Connection
>
> Loki expr: count_over_time({type="mysql/errorlog"} |= "Too many connections" [1m])

假如使用 `log_sink_json` 的話，Error Log 的資訊如下<br>

```JSON
{
  "prio": 2,
  "source_line": 282,
  "source_file": "connection_handler_manager.cc",
  "msg": "Too many connections",
  "time": "2025-11-10T09:39:22.188365+08:00",
  "ts": 1762738762188,
  "subsystem": "Server",
  "label": "Warning"
}
```

### Dead Lock
>
> Loki expr: count_over_time({type="mysql/errorlog"} |= "deadlock" [1m])

`log_sink_json` 格式

```JSON
{
  "log_type": 1,
  "prio": 3,
  "err_code": 12468,
  "subsystem": "InnoDB",
  "msg": "Transactions deadlock detected, dumping detailed information.",
  "time": "2025-11-10T08:52:25.728726+08:00",
  "ts": 1762735945728,
  "err_symbol": "ER_IB_MSG_643",
  "SQL_state": "HY000",
  "label": "Note"
}
{
  "log_type": 1,
  "prio": 3,
  "err_code": 12469,
  "subsystem": "InnoDB",
  "msg": "\n*** (1) TRANSACTION:\n",
  "time": "2025-11-10T08:52:25.728850+08:00",
  "ts": 1762735945728,
  "err_symbol": "ER_IB_MSG_644",
  "SQL_state": "HY000",
  "label": "Note"
}
{
  "log_type": 1,
  "prio": 3,
  "err_code": 12469,
  "subsystem": "InnoDB",
  "msg": "\n*** (1) HOLDS THE LOCK(S):\n",
  "time": "2025-11-10T08:52:25.728929+08:00",
  "ts": 1762735945728,
  "err_symbol": "ER_IB_MSG_644",
  "SQL_state": "HY000",
  "label": "Note"
}
{
  "log_type": 1,
  "prio": 3,
  "err_code": 12469,
  "subsystem": "InnoDB",
  "msg": "\n*** (1) WAITING FOR THIS LOCK TO BE GRANTED:\n",
  "time": "2025-11-10T08:52:25.729280+08:00",
  "ts": 1762735945729,
  "err_symbol": "ER_IB_MSG_644",
  "SQL_state": "HY000",
  "label": "Note"
}
{
  "log_type": 1,
  "prio": 3,
  "err_code": 12469,
  "subsystem": "InnoDB",
  "msg": "\n*** (2) TRANSACTION:\n",
  "time": "2025-11-10T08:52:25.729593+08:00",
  "ts": 1762735945729,
  "err_symbol": "ER_IB_MSG_644",
  "SQL_state": "HY000",
  "label": "Note"
}
{
  "log_type": 1,
  "prio": 3,
  "err_code": 12469,
  "subsystem": "InnoDB",
  "msg": "\n*** (2) HOLDS THE LOCK(S):\n",
  "time": "2025-11-10T08:52:25.729658+08:00",
  "ts": 1762735945729,
  "err_symbol": "ER_IB_MSG_644",
  "SQL_state": "HY000",
  "label": "Note"
}
{
  "log_type": 1,
  "prio": 3,
  "err_code": 12469,
  "subsystem": "InnoDB",
  "msg": "\n*** (2) WAITING FOR THIS LOCK TO BE GRANTED:\n",
  "time": "2025-11-10T08:52:25.729972+08:00",
  "ts": 1762735945729,
  "err_symbol": "ER_IB_MSG_644",
  "SQL_state": "HY000",
  "label": "Note"
}
{
  "log_type": 1,
  "prio": 3,
  "err_code": 12469,
  "subsystem": "InnoDB",
  "msg": "*** WE ROLL BACK TRANSACTION (2)\n",
  "time": "2025-11-10T08:52:25.730272+08:00",
  "ts": 1762735945730,
  "err_symbol": "ER_IB_MSG_644",
  "SQL_state": "HY000",
  "label": "Note"
}
```

`log_sink_internal` 格式

```
2025-11-10T08:52:25.728726+08:00 0 [Note] [MY-012468] [InnoDB] Transactions deadlock detected, dumping detailed information.
2025-11-10T08:52:25.728850+08:00 0 [Note] [MY-012469] [InnoDB]  *** (1) TRANSACTION:
TRANSACTION 3109, ACTIVE 25 sec starting index read
mysql tables in use 1, locked 1
LOCK WAIT 3 lock struct(s), heap size 1128, 2 row lock(s), undo log entries 1
MySQL thread id 2243, OS thread handle 140566786410240, query id 16117 localhost hilife_db updating
update t set name = 'YangPlus' where id = 1
2025-11-10T08:52:25.728929+08:00 0 [Note] [MY-012469] [InnoDB]  *** (1) HOLDS THE LOCK(S):
RECORD LOCKS space id 3 page no 4 n bits 72 index PRIMARY of table `Yang`.`t` trx id 3109 lock_mode X locks rec but not gap
Record lock, heap no 5 PHYSICAL RECORD: n_fields 4; compact format; info bits 0
 0: len 4; hex 00000002; asc     ;;
 1: len 6; hex 000000000c25; asc      %;;
 2: len 7; hex 0100000121024c; asc     ! L;;
 3: len 8; hex 4265636b506c7573; asc BeckPlus;;

2025-11-10T08:52:25.729280+08:00 0 [Note] [MY-012469] [InnoDB]  *** (1) WAITING FOR THIS LOCK TO BE GRANTED:
RECORD LOCKS space id 3 page no 4 n bits 72 index PRIMARY of table `Yang`.`t` trx id 3109 lock_mode X locks rec but not gap waiting
Record lock, heap no 4 PHYSICAL RECORD: n_fields 4; compact format; info bits 0
 0: len 4; hex 00000001; asc     ;;
 1: len 6; hex 000000000c24; asc      $;;
 2: len 7; hex 02000001230151; asc     # Q;;
 3: len 8; hex 59616e67506c7573; asc YangPlus;;

2025-11-10T08:52:25.729593+08:00 0 [Note] [MY-012469] [InnoDB]  *** (2) TRANSACTION:
TRANSACTION 3108, ACTIVE 30 sec starting index read
mysql tables in use 1, locked 1
LOCK WAIT 3 lock struct(s), heap size 1128, 2 row lock(s), undo log entries 1
MySQL thread id 2276, OS thread handle 140566770149120, query id 16191 localhost hilife_db updating
update t set name = 'BeckPlus' where id = 2
2025-11-10T08:52:25.729658+08:00 0 [Note] [MY-012469] [InnoDB]  *** (2) HOLDS THE LOCK(S):
RECORD LOCKS space id 3 page no 4 n bits 72 index PRIMARY of table `Yang`.`t` trx id 3108 lock_mode X locks rec but not gap
Record lock, heap no 4 PHYSICAL RECORD: n_fields 4; compact format; info bits 0
 0: len 4; hex 00000001; asc     ;;
 1: len 6; hex 000000000c24; asc      $;;
 2: len 7; hex 02000001230151; asc     # Q;;
 3: len 8; hex 59616e67506c7573; asc YangPlus;;

2025-11-10T08:52:25.729972+08:00 0 [Note] [MY-012469] [InnoDB]  *** (2) WAITING FOR THIS LOCK TO BE GRANTED:
RECORD LOCKS space id 3 page no 4 n bits 72 index PRIMARY of table `Yang`.`t` trx id 3108 lock_mode X locks rec but not gap waiting
Record lock, heap no 5 PHYSICAL RECORD: n_fields 4; compact format; info bits 0
 0: len 4; hex 00000002; asc     ;;
 1: len 6; hex 000000000c25; asc      %;;
 2: len 7; hex 0100000121024c; asc     ! L;;
 3: len 8; hex 4265636b506c7573; asc BeckPlus;;

2025-11-10T08:52:25.730272+08:00 0 [Note] [MY-012469] [InnoDB] *** WE ROLL BACK TRANSACTION (2)
```

Dead Lock 的話，<br>
JSON 反而沒有詳細的資訊，<br>
只能單純做到有 Dead Lock 發生的通知，<br>
但 Table、Syntax 等等資訊還是需要回 Log 查看，<br>
在這點上，`log_sink_internal` 倒是比 `log_sink_json` 好多了

### ERROR Log
>
> Loki expr: count_over_time({type="mysql/errorlog"} |= "ERROR" [1m])

`log_sink_json` 格式

```JSON
{
  "prio": 1,
  "err_code": 10045,
  "source_line": 150,
  "source_file": "event_scheduler.cc",
  "function": "print_warnings",
  "msg": "Event Scheduler: [xxxxx@localhost][DB.EVENT] Urgent ERROR",
  "time": "2025-11-10T15:50:00.903136+08:00",
  "ts": 1762761000903,
  "thread": 781,
  "err_symbol": "ER_EVENT_MESSAGE_STACK",
  "SQL_state": "HY000",
  "subsystem": "Server",
  "label": "Error"
}
{
  "prio": 3,
  "err_code": 10046,
  "source_line": 429,
  "source_file": "event_scheduler.cc",
  "function": "run",
  "msg": "Event Scheduler: [xxxxx@localhost].[DB.EVENT] event execution failed.",
  "time": "2025-11-10T15:50:00.903217+08:00",
  "ts": 1762761000903,
  "thread": 781,
  "err_symbol": "ER_EVENT_EXECUTION_FAILED",
  "SQL_state": "HY000",
  "subsystem": "Server",
  "label": "Note"
}
```

`log_sink_internal` 格式

```
2025-11-10T15:50:00.903136+08:00 781 [ERROR] [MY-010045] [Server] Event Scheduler: [xxxxx@localhost][DB.EVENT] Urgent ERROR
2025-11-10T15:50:00.903217+08:00 781 [Note] [MY-010046] [Server] Event Scheduler: [xxxxx@localhost].[DB.EVENT] event execution failed.
```

除了 MySQL Eroor Log 真的出現的錯誤以外，<br>
這還有用來我們內部日常維運使用，<br>
為了確保當 `Event Scheduler` 出現錯誤時可以即時發現，<br>
在 `Event` 中有使用

```MySQL
SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Urgent ERROR';
```

這方式來寫入 Error Log，<br>
再搭配 Loki 來達到即時告警

---

後來 MySQL 8.4 內部設定檔公版修改為

```
log_error_services                = 'log_sink_internal; log_filter_dragnet; log_sink_json;'
dragnet.log_error_filter_rules    = 'IF err_code == ER_ABORTING_USER_CONNECTION THEN drop.'
```

`ER_ABORTING_USER_CONNECTION` 是因為我們架構是建置在 GCE 上，<br>
前面會掛一層 [Cloud Load Balancing](https://docs.cloud.google.com/load-balancing/docs/load-balancing-overview) 來提供服務，<br>
而它的 [Health checks](https://docs.cloud.google.com/load-balancing/docs/health-check-concepts?hl=en) 會造成 MySQL Error Log 的增長，<br>
類似這樣

```
2025-11-13T15:37:50.697889+08:00 59287548 [Note] [MY-010914] [Server] Got an error reading communication packets
2025-11-13T15:37:51.492153+08:00 59287550 [Note] [MY-010914] [Server] Got an error reading communication packets
```

這是因為 Health checks 是使用 `telnet` 等的方式來偵測 TCP port 是否 alive，<br>
在 MySQL 會被視為連線沒有正常建立，導致 Error Log 出現這資訊，<br>
又因為我們內部設定檔公版有設定 [log_error_verbosity](https://dev.mysql.com/doc/refman/8.4/en/server-system-variables.html#sysvar_log_error_verbosity) 為 `3`，<br>
導致我們 Error Log 會有大量的無效資訊產生。<br>

所以我們在 `filter` 中新增<br>
`'IF err_code == ER_ABORTING_USER_CONNECTION THEN drop.'`<br>
既可以保留原始的 Error Log (`log_sink_internal`)，<br>
又可以減少雜訊後再存入 JSON Format 的 Error Log (`log_sink_json`)<br>

---

## Reference

* [MySQL Documentation : 7.4.2 The Error Log](https://dev.mysql.com/doc/refman/8.4/en/error-log.html)
