# 4. 수동 Failover

## Replication 확인  

pg-master 

```
select pg_is_in_recovery();

--결과값  false 
```

pg-slave

```text
select pg_is_in_recovery();

--결과값  true
```

pg-master 

```text
select pid, usesysid, usename, application_name, client_addr, backend_start, state, sent_lsn, write_lsn, flush_lsn, replay_lsn, sync_state 
from pg_stat_replication;

-- 결과값 
10588	"16384"	"usr_repl"	"walreceiver"	"10.1.0.5"	"2021-08-08 21:46:39.827198+09"	"streaming"	"0/D000140"	"0/D000140"	"0/D000140"	"0/D000140"	"async"
```

pg-slave 

```text
select pid, sender_host, status, receive_start_lsn, received_lsn, last_msg_send_time, last_msg_receipt_time, latest_end_lsn, latest_end_time 
from pg_stat_wal_receiver;

-- 결과값 
6862	"10.1.0.4"	"streaming"	"0/D000000"	"0/D000140"	"2021-08-08 21:54:40.89394+09"	"2021-08-08 21:54:40.894328+09"	"0/D000140"	"2021-08-08 21:46:39.830443+09"
```



