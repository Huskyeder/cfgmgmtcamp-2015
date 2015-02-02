# Kubernetes Demo

## Setup

```
fleetctl list-machines
```

## Create the nodes

```
kubecfg list nodes
```

```
kubecfg -c nodes/node0.json create nodes
kubecfg -c nodes/node1.json create nodes
kubecfg -c nodes/node2.json create nodes
```

```
kubecfg list nodes
```

## Create the postgres pod

```
kubecfg -c pods/postgres.json create pods
```

```
kubecfg list pods
```

```
kubecfg get pods/postgres
```

```
kubecfg -yaml get pods/postgres
```

### Create the postgres service

```
kubecfg -c services/postgres.json create services
```

```
kubecfg list services
```
```
kubecfg get services/postgres
```
```
kubecfg -yaml get services/postgres
```

```
psql -h POSTGRES_PORTAL_IP -U postgres
```

## Create the pgview replication controller

```
kubecfg -c replication-controllers/pgview-stable.json create replicationControllers
```

```
kubecfg list replicationControllers
```

```
kubecfg list pods
```

### Create the pgview service

```
kubecfg -c services/pgview.json create services
```

```
curl http://PGVIEW_PORTAL_IP -d @rpc/version.json
```

```
curl http://PGVIEW_PORTAL_IP -d @rpc/sqlfeatures.json
```

Run it again, request should be served from memcache

```
curl http://PGVIEW_PORTAL_IP -d @rpc/sqlfeatures.json
```

### Terminal 2

```
while true; do curl http://PGVIEW_PORTAL_IP -d @rpc/version.json; sleep 1; done
```

## Scaling pgview 

```
kubecfg resize pgviewStableController 3
```

```
kubecfg list pods
```

## Upgrade pgview using the canary pattern

### Deploy the canary pod

```
kubecfg -c replication-controllers/pgview-canary.json create replicationControllers
```

```
kubecfg list pods
```

```
kubecfg -l "name=pgview,environment=production" list pods
```

### Rolling upgrade

```
kubecfg -u 5s -image "pgview:2.0.0" rollingupdate pgviewStableController
```

```
kubecfg list pods
```

### Rolling back

```
kubecfg -u 5s -image "pgview:1.0.0" rollingupdate pgviewStableController
```

```
kubecfg list pods
```
