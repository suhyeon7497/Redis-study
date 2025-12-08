## 연결

```
docker compose up -d
docker exec -it redis-node1 redis-cli --cluster create redis-node1:6379 redis-node2:6379 redis-node3:6379 redis-node4:6379 redis-node5:6379 redis-node6:6379 --cluster-replicas 1

yes
```

[OK] All 16384 slots covered. 가 출력되면 cluster 연결이 된것입니다. master와 slave, hash slot 정보를 확인하면 됩니다.