# nomi benchmark of fleet cluster

A set of benchmark results done in a multi-node [fleet][coreos-fleet] cluster via [nomi][giantswarm-nomi].

## Benchmark environment

* 1 main node : etcd1 (8 vCPUs, 12GB RAM), CentOS 7
* 3 main nodes : fleet1, fleet2, and fleet3 (each 2 vCPUs, 4GB RAM, CoreOS 1122)
* etcd v2.3.7 or v3.0.9
* fleet master (v0.13.0-234-g774ac312, 2016-11-23) or particular branches
* gRPC enabled

## How to run nomi benchmarks

Set up multiple nodes in a cluster. Let's assume that we have 4 nodes.

Build fleet and copy binaries to each cluster. You can use [copyfleet][copyfleet] script.

```
$ cd $GOPATH/src/github.com/coreos/fleet
$ git checkout master
$ copyfleet master
```

Restart etcd and fleetd on each node, using [stopall][stopall] and [startall][startall].

```
$ stopall
... # make sure it was cleanly shut down, etc.
$ startall
```

Prepare nomi configs based on [examples provided by nomi][nomi-examples], and put them into nomi-config directory on the main node.

Run nomi benchmarks on the main node, using [run-nomi][run-nomi].

```
$ ssh etcd1
# ./run-nomi
```

## Configuration of nomi

The config [benchmarkDef3000Units.yaml][benchmarkDef3000Units.yaml] is like below:

```
instancegroup-size: 1
instructions:
  - sleep: 1
  - start:
     max: 3000
     interval: 100
  - sleep: 600
  - stop: stop-all
```

Note that you might need to change the `sleep` parameter after the start command according to the value of `max`. To increase `max`, you should increase also `sleep`. For example, to test 10000 units, you should set `max` to 10000, as well as increase `sleep` from 600 to 2000. Otherwise nomi could not retrieve all results.

[coreos-fleet]: https://github.com/coreos/fleet
[giantswarm-nomi]: https://github.com/giantswarm/nomi
[nomi-examples]: https://github.com/giantswarm/nomi/tree/master/examples
[copyfleet]: bin/copyfleet
[stopall]: bin/stopall
[startall]: bin/startall
[run-nomi]: bin/run-nomi
[benchmarkDef3000Units.yaml]: nomi-config/benchmarkDef3000Units.yaml
