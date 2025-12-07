## 연결

```
docker compose up -d
redis-cli -h localhost -p 26379
```

## Sentinel에서 잘 연결되었는지 확인
```
sentinel redis-cli : sentinel masters
```
- "num-slaves"
- "num-other-sentinels"
등의 정보를 확인할 수 있다.

## master노드가 죽는다면
```
cmd : docker stop redis-master
```

잠시 후, 다른 노드가 master가 된 것을 확인할 수 있다.

## master 노드가 다시 살아난다면
```
cmd : docker start redis-master
```

master 권한을 돌려주지 않는다. redis-master는 slave노드가 된다.

## node가 죽는 것이 아니라 중간 network가 문제가 발생하는 경우
**split-brain 현상이 발생할 수 있다.**