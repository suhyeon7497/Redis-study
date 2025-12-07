## 연결

```
docker compose up -d
redis-cli -h localhost -p 6379
redis-cli -h localhost -p 6380
redis-cli -h localhost -p 6381
```

## 연결 잘 되었는지 확인

```
redis-master-cli : info replication
redis-slave1-cli : info replication
redis-slave2-cli : info replication
```

**각각에서 role이 master/slave로 나와야 하고, master-replid가 같아야 함.**

## master는 write 가능, slave는 write 불가 (readOnly)

```
redis-master-cli : set hello world
redis-slave1-cli : get hello
redis-slave2-cli : set hello world
```

**redis master에서 set하면 slave들이 읽어감. slave는 readonly이기 때문에 set이 안됨.**
**offset을 통해 데이터 동기화 정도를 알 수 있음**
- master_repl_offset
- slave_repl_offset

더 큰 값을 가질 경우 더 최신 정보를 가지고 있음.

## Partial Sync
중간에 slave의 연결이 끊겼을 경우, 재연결될 때 master의 백로그 버퍼에서 데이터를 부분 동기화한다.

```
redis-master-cli : set x 1
redis-master-cli : incr x
redis-master-cli : incr x
redis-master-cli : incr x
cmd : docker stop redis-slave1
redis-master-cli : incr x
redis-master-cli : incr x
redis-master-cli : incr x
cmd : docker start redis-slave1
cmd : docker logs redis-slave1
```

**Master accepted a Partial Resynchronization 라는 문구가 보이면 partial sync를 한 것을 확인할 수 있다.**

**만약 backlog의 수용치보다 더 많은 offset 차이가 날 경우 full sync를 진행한다.**

## master가 죽는 경우
master가 죽을 경우 slave들은 master를 읽게 된다.

``` redis
cmd : docker stop redis-master
redis-slave1-cli : info replication //master_link_down_since_seconds 이라는 것이 생긴다.
redis-slave1-cli : set a 1 // readOnly이기 때문에 안됨.

// slave1을 master로 승격해주자.
redis-slave1-cli : replicaof no one
redis-slave1-cli : info replication //master_replid2 가 생긴다. role이 master가 되고, 이전의 master_repl_id를 가지고 있다.

// slave2도 연결해주자.
redis-slave2-cli : replicaof redis-slave1 6380

// master가 다시 살아나면?
cmd : docker start redis-master
```

**마지막에는 master가 2개가 되는 현상이 발생한다. 이를 split-brain이라고 한다.**
