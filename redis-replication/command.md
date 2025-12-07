## 연결

redis-cli -h localhost -p 6379

redis-cli -h localhost -p 6380

redis-cli -h localhost -p 6381

## 연결 잘 되었는지 확인

redis-master-cli : info replication

redis-slave1-cli : info replication

redis-slave2-cli : info replication

**각각에서 role이 master/slave로 나와야 하고, master-replid가 같아야 함.**

## master는 write 가능, slave는 write 불가 (readOnly)

redis-master-cli : set hello world

redis-slave1-cli : get hello

redis-slave2-cli : set hello world

**redis master에서 set하면 slave들이 읽어감. slave는 readonly이기 때문에 set이 안됨.**
**offset을 통해 데이터 동기화 정도를 알 수 있음**
- master_repl_offset
- slave_repl_offset

더 큰 값을 가질 경우 더 최신 정보를 가지고 있음.