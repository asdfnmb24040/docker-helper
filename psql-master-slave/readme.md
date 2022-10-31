參考資料
https://medium.com/swlh/postgresql-replication-with-docker-c6a904becf77

步驟
- 執行master
- 複製 postgres.conf 和 pg_hba.conf 到 ./data/psdata
- 執行sql
```
CREATE USER replicator WITH REPLICATION ENCRYPTED PASSWORD '123123';
SELECT * FROM pg_create_physical_replication_slot('replication_slot_slave1');
SELECT * FROM pg_replication_slots
```
- 進入容器執行指令
```
docker exec -it pg_master_db bash
pg_basebackup -D /tmp/postgresslave -S replication_slot_slave1 -X stream -P -U replicator -Fp -R
```
- 複製tmp中的./postgresslave 到 slave的data資料夾內，
- 取代postgresql.auto.conf
- 刪除pg_wal內的資料
- 執行slave

測試
```
drop table accounts

CREATE TABLE accounts (
	user_id serial PRIMARY KEY,
	username VARCHAR ( 50 ) NOT NULL
);

INSERT INTO accounts( username)
VALUES ('armand')

select * from accounts
```