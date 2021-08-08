# PostgreSQL Active Standby HA

## PostgreSQL 11 Active Standby 구성 

PostgreSQL은 Replication 구성은 WAL 로그 전달 방식인 log-shipping 방식과 streaming 방식으로 나뉨 

log-shipping은 WAL 파일을 지정된 사이즈를 채워야 전달되므로 비동기 방식 \(async\)

streaming은 WAL 파일을 거의 실시간으로 동작하며 비동기와 동기 방식이 있음 \(async, sync\) 

async : 비동기 방식으로 데이터 전달   
sync: slave까지 복제 완료 후 commit , 따라서 commit 대기 시간이 길어짐 

### PostgreSQL 11 Streaming Replication 구성 

OS : linux centOS 7.9 based   
PostgreSQL : 11 ver

pg-master : 10.1.0.4  
pg-slave : 10.1.0.5



 



