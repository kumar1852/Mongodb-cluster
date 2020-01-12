# MongoDB
These ansible playbooks can be used to configure replica and sharded MongoDB clusters.

### Replica Cluster

Use the replica-example.inv file as a template to create your own inventory file. 

```
[all:vars]
mongo_root_dir=/var/lib/mongo
data_device_name=xvdc
replica_set_name=rs0
cluster_mode=replica
port=27017

[replicas]
10.2.3.6 dns=test-mongo-node0.us-west-2a.dev-charter.net
10.2.3.160 dns=test-mongodb-node1.us-west-2b.dev-charter.net
10.2.4.55 dns=test-mongodb-node2.us-west-2c.dev-charter.net
```

Make sure the data_device_name is set properly and edit your IPs and hostnames.

Once your inventory file is setup you can execute the replica-cluster.yml playbook to build the replica cluster.

```ansible-playbook -i <your inv file> replica-cluster.yml```

### Sharded Cluster

Use the shard-example.inv file as a template to create your own inventory file.

```
config_rep_set_name=configrs0
config_port=27019
shard_rep_set_name=shardrs0
shard_port=27018
router_port=27017
cluster_mode=shard

[config]
172.28.160.161
172.28.162.13
172.28.164.141

[replicas]
172.28.160.161
172.28.162.13
172.28.164.141

[replicas:vars]
replica_set_name={{shard_rep_set_name}}
port={{shard_port}}

[routers]
172.28.160.161
172.28.162.13
172.28.164.141
```

There's 3 main components to a sharded cluster. Replicas, routers, and config nodes. 
The way the playbooks are setup you can use the same nodes for all 3 roles.

#### Replicas
These nodes hold the data shards.
#### Config
These nodes hold meta data and config data for the shards
#### Routers
These nodes are queried against by clients and use the config nodes to determine which replicas contain the data being queried.

Once your inventory file is setup. You can setup the sharded cluster by executing the sharded-cluster.yml playbook

``` ansible-playbook -i <your inv file> sharded-cluster.yml```