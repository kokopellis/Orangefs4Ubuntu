# Redis Cluster Deployment

This is a brief instruction for deploy redis cluster on Ubuntu 16.04 for test.  
I used two virtual machine which ip address is 192.168.56.101, 192.168.56.102.  
There are at least 3 nodes in redis cluster. So I started two redis process on each virtual machine.

### Prerequistie Installation
Finish this on all the virtual machine
    apt install make gcc libc6-dev tcl

### Compile Source Code
Finish this on all the virtual machine
```bash
mkdir /redis-shared
cd /redis-shared
wget http://download.redis.io/redis-stable.tar.gz
tar xzf redis-stable
cd redis-stable
make && make install
```

### Create Configuration File and Start Services
On 192.168.56.101
```bash
mkdir /redis-shared/{7001,7002}
vim /redis-shard/7001/node.conf
cat !$
    # bind 127.0.0.1
    protected-mode no
    port 7001
    pidfile /var/run/redis_7001.pid
    cluster-enabled yes
    cluster-config-file nodes-7001.conf
    cluster-node-timeout 15000
vim /redis-shard/7001/node.conf
cat !$
    # bind 127.0.0.1
    protected-mode no
    port 7002
    pidfile /var/run/redis_7002.pid
    cluster-enabled yes
    cluster-config-file nodes-7002.conf
    cluster-node-timeout 15000
nohup /redis-shared/redis-stable/src/redis-server /redis-shared/7001/node.conf &
nohup /redis-shared/redis-stable/src/redis-server /redis-shared/7002/node.conf &
```

On 192.168.56.102
```bash
mkdir /redis-shared/{7003,7004}
vim /redis-shard/7003/node.conf
cat !$
    # bind 127.0.0.1
    protected-mode no
    port 7003
    pidfile /var/run/redis_7003.pid
    cluster-enabled yes
    cluster-config-file nodes-7003.conf
    cluster-node-timeout 15000
vim /redis-shard/7004/node.conf
cat !$
    # bind 127.0.0.1
    protected-mode no
    port 7004
    pidfile /var/run/redis_7004.pid
    cluster-enabled yes
    cluster-config-file nodes-7004.conf
    cluster-node-timeout 15000
nohup /redis-shared/redis-stable/src/redis-server /redis-shared/7004/node.conf &
nohup /redis-shared/redis-stable/src/redis-server /redis-shared/7004/node.conf &
```

### Create Cluster
Create and start cluster on one of the virtual machine
```bash
/redis-shard/redis-5.0.0/src/redis-cli --cluster create 192.168.56.101:7001 192.168.56.101:7002 192.168.56.102:7003 192.168.56.102:7004
```

### Do some test
    redis-cli -c -h 192.168.56.102 -p 7003

