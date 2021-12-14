# redis-sentinel-ha


### To build the sentinel image:
```
cd /sentinel/docker
docker build -t <image name> .
```

### Creating the sentinel cluster:
Each of the sentinel instances need a persistent volume attached at "/sentinel-conf-file/" in order to store the data in case a pod goes down.
```
kubectl apply -f redis/redis-sentinel-1.yml
kubectl apply -f redis/redis-sentinel-2.yml
kubectl apply -f redis/redis-sentinel-3.yml
```

### Testing out the sentinel cluster:
Identify the master in the redis cluster using these commands.
```
kubectl exec -it deployment/deployment-redis-instance-1 -- redis-cli -c info replication | grep role
kubectl exec -it deployment/deployment-redis-instance-2 -- redis-cli -c info replication | grep role
kubectl exec -it deployment/deployment-redis-instance-3 -- redis-cli -c info replication | grep role
```

Verify if each of the sentinels also identify the same master.
```
kubectl exec -it deployment/deployment-redis-sentinel-instance-1 -- redis-cli -p 26379 -c SENTINEL get-master-addr-by-name mymaster
kubectl exec -it deployment/deployment-redis-sentinel-instance-2 -- redis-cli -p 26379 -c SENTINEL get-master-addr-by-name mymaster
kubectl exec -it deployment/deployment-redis-sentinel-instance-3 -- redis-cli -p 26379 -c SENTINEL get-master-addr-by-name mymaster
```
