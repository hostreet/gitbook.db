# 3. Config 설정

## pg-master configure 설정 

Postgres 계정 비밀번호 설정 

```text

[root@pg-master clooadmin]# su - postgres
-bash-4.2$ psql
psql (11.12)
Type "help" for help.

postgres=# \password postgres
Enter new password: 패스워드 
Enter it again: 패스워드 
postgres=#

```

Replication 유저 생성 

```text
postgres=# create role usr_repl with replication password 'replica' login;
CREATE ROLE
```

postgresql.conf 파일 수정 

```text
vi /var/lib/pgsql/11/data/postgresql.conf 

linten_addresses = '*' 
wal_level = hot_standby # 대기 서버에서 읽기 전용 작업 가능 
max_wal_senders = 2 # WAL 파일을 전송할 수 있는 최대 서버 수 
wal_keep_segments = 32 # 마스터 서버 디렉토리에 저장할 최대 WAL 파일 수  
```

pg\_hba.conf 파일 수정 

```text
vi /var/lib/pgsql/11/data/pg_hba.conf
#아래 replciation 유저 추가 
host  replication   usr_repl    10.1.0.5/32   md5 
```



