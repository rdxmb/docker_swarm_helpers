docker swarm helpers
====================

Description
===========

workarounds for missing features in docker swarm
  * find the nodes and containers where your service is actually running
  * `exec` into a container running that service

Tested on
=========
  * Ubuntu 16.04 LTS

Requirements
============
  * ssh-key-forwarding on your docker nodes
  * the docker nodes listed in `docker node ls` must be reachable via their names through `ssh`, so e.g. define them in
    * `/etc/hosts` or
    * `.ssh/config`

Content
=======

dockernodeps
------------
Returns the docker node-id and the container-id in format NODE-ID:CONTAINER-ID running the given service as parameter

==== Examples ====

```bash
manager1# dockernodeps monitoring_influxdb
worker2:8bca5bbc65c0
  
manager1# dockernodeps -a base_dnsmasq
manager1:b83fa2105cbf
worker1:29ff6539d824
worker2:489d1cb42f1e
 ```

dockerserviceexec 
-----------------

Easily exec into a random task of your service running on any container in your swarm. (uses `dockernodeps` in PATH as a dependency)

==== Examples ====

```bash
manager1# dockerserviceexec monitoring_influxdb influx
+ ssh -t worker2 docker exec -it 8bca5bbc65c0 influx
Connected to http://localhost:8086 version 1.4.2
InfluxDB shell version: 1.4.2
> 
```

```
manager1# dockerserviceexec base_reverseproxy openresty -s reload
+ ssh -t worker2 docker exec -it 8d52e578543a openresty -s reload
Connection to worker2 closed.
```

Recommends
==========
copy the files to /usr/local/bin on any manager in your swarm

=== KnownIssues and Limitations ===
  * Only tested for containers with status 'Running'. Maybe there are other states like 'healthy' which have to be added.

=== Contribute ===
Feel free to contribute: pull requests are very welcome!

