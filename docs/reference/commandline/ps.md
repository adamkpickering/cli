---
title: "ps"
description: "The ps command description and usage"
keywords: "container, running, list"
---

# ps

```markdown
Usage: docker ps [OPTIONS]

List containers

Aliases:
  docker container ls, docker container list, docker container ps, docker ps

Options:
  -a, --all             Show all containers (default shows just running)
  -f, --filter value    Filter output based on conditions provided (default [])
                        - ancestor=(<image-name>[:tag]|<image-id>|<image@digest>)
                          containers created from an image or a descendant.
                        - before=(<container-name>|<container-id>)
                        - expose=(<port>[/<proto>]|<startport-endport>/[<proto>])
                        - exited=<int> an exit code of <int>
                        - health=(starting|healthy|unhealthy|none)
                        - id=<ID> a container's ID
                        - isolation=(`default`|`process`|`hyperv`) (Windows daemon only)
                        - is-task=(true|false)
                        - label=<key> or label=<key>=<value>
                        - name=<string> a container's name
                        - network=(<network-id>|<network-name>)
                        - publish=(<port>[/<proto>]|<startport-endport>/[<proto>])
                        - since=(<container-name>|<container-id>)
                        - status=(created|restarting|removing|running|paused|exited)
                        - volume=(<volume name>|<mount point destination>)
      --format string   Format output using a custom template:
                        'table':            Print output in table format with column headers (default)
                        'table TEMPLATE':   Print output in table format using the given Go template
                        'json':             Print in JSON format
                        'TEMPLATE':         Print output using the given Go template.
                        Refer to https://docs.docker.com/go/formatting/ for more information about formatting output with templates
      --help            Print usage
  -n, --last int        Show n last created containers (includes all states) (default -1)
  -l, --latest          Show the latest created container (includes all states)
      --no-trunc        Don't truncate output
  -q, --quiet           Only display numeric IDs
  -s, --size            Display total file sizes
```

## Examples

### <a name=no-trunc></a> Do not truncate output (--no-trunc)

Running `docker ps --no-trunc` showing 2 linked containers.

```console
$ docker ps --no-trunc

CONTAINER ID        IMAGE                        COMMAND                CREATED              STATUS              PORTS               NAMES
4c01db0b339c        ubuntu:12.04                 bash                   17 seconds ago       Up 16 seconds       3300-3310/tcp       webapp
d7886598dbe2        crosbymichael/redis:latest   /redis-server --dir    33 minutes ago       Up 33 minutes       6379/tcp            redis,webapp/db
```

### <a name=all></a> Show both running and stopped containers (-a, --all)

The `docker ps` command only shows running containers by default. To see all
containers, use the `--all` (or `-a`) flag:

```console
$ docker ps -a
```

`docker ps` groups exposed ports into a single range if possible. E.g., a
container that exposes TCP ports `100, 101, 102` displays `100-102/tcp` in
the `PORTS` column.

### <a name=size></a> Show disk usage by container (--size)

The `docker ps --size` (or `-s`) command displays two different on-disk-sizes for each container:

```console
$ docker ps --size

CONTAINER ID   IMAGE          COMMAND                  CREATED        STATUS       PORTS   NAMES        SIZE                                                                                      SIZE
e90b8831a4b8   nginx          "/bin/bash -c 'mkdir "   11 weeks ago   Up 4 hours           my_nginx     35.58 kB (virtual 109.2 MB)
00c6131c5e30   telegraf:1.5   "/entrypoint.sh"         11 weeks ago   Up 11 weeks          my_telegraf  0 B (virtual 209.5 MB)
```
  * The "size" information shows the amount of data (on disk) that is used for the _writable_ layer of each container
  * The "virtual size" is the total amount of disk-space used for the read-only _image_ data used by the container and the writable layer.

For more information, refer to the [container size on disk](https://docs.docker.com/storage/storagedriver/#container-size-on-disk) section.


### <a name=filter></a> Filtering (--filter)

The `--filter` (or `-f`) flag format is a `key=value` pair. If there is more
than one filter, then pass multiple flags (e.g. `--filter "foo=bar" --filter "bif=baz"`)

The currently supported filters are:

| Filter                | Description                                                                                                                          |
|:----------------------|:-------------------------------------------------------------------------------------------------------------------------------------|
| `id`                  | Container's ID                                                                                                                       |
| `name`                | Container's name                                                                                                                     |
| `label`               | An arbitrary string representing either a key or a key-value pair. Expressed as `<key>` or `<key>=<value>`                           |
| `exited`              | An integer representing the container's exit code. Only useful with `--all`.                                                         |
| `status`              | One of `created`, `restarting`, `running`, `removing`, `paused`, `exited`, or `dead`                                                 |
| `ancestor`            | Filters containers which share a given image as an ancestor. Expressed as `<image-name>[:<tag>]`,  `<image id>`, or `<image@digest>` |
| `before` or `since`   | Filters containers created before or after a given container ID or name                                                              |
| `volume`              | Filters running containers which have mounted a given volume or bind mount.                                                          |
| `network`             | Filters running containers connected to a given network.                                                                             |
| `publish` or `expose` | Filters containers which publish or expose a given port. Expressed as `<port>[/<proto>]` or `<startport-endport>/[<proto>]`          |
| `health`              | Filters containers based on their healthcheck status. One of `starting`, `healthy`, `unhealthy` or `none`.                           |
| `isolation`           | Windows daemon only. One of `default`, `process`, or `hyperv`.                                                                       |
| `is-task`             | Filters containers that are a "task" for a service. Boolean option (`true` or `false`)                                               |


#### label

The `label` filter matches containers based on the presence of a `label` alone or a `label` and a
value.

The following filter matches containers with the `color` label regardless of its value.

```console
$ docker ps --filter "label=color"

CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
673394ef1d4c        busybox             "top"               47 seconds ago      Up 45 seconds                           nostalgic_shockley
d85756f57265        busybox             "top"               52 seconds ago      Up 51 seconds                           high_albattani
```

The following filter matches containers with the `color` label with the `blue` value.

```console
$ docker ps --filter "label=color=blue"

CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS              PORTS               NAMES
d85756f57265        busybox             "top"               About a minute ago   Up About a minute                       high_albattani
```

#### name

The `name` filter matches on all or part of a container's name.

The following filter matches all containers with a name containing the `nostalgic_stallman` string.

```console
$ docker ps --filter "name=nostalgic_stallman"

CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
9b6247364a03        busybox             "top"               2 minutes ago       Up 2 minutes                            nostalgic_stallman
```

You can also filter for a substring in a name as this shows:

```console
$ docker ps --filter "name=nostalgic"

CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
715ebfcee040        busybox             "top"               3 seconds ago       Up 1 second                             i_am_nostalgic
9b6247364a03        busybox             "top"               7 minutes ago       Up 7 minutes                            nostalgic_stallman
673394ef1d4c        busybox             "top"               38 minutes ago      Up 38 minutes                           nostalgic_shockley
```

#### exited

The `exited` filter matches containers by exist status code. For example, to
filter for containers that have exited successfully:

```console
$ docker ps -a --filter 'exited=0'

CONTAINER ID        IMAGE             COMMAND                CREATED             STATUS                   PORTS                      NAMES
ea09c3c82f6e        registry:latest   /srv/run.sh            2 weeks ago         Exited (0) 2 weeks ago   127.0.0.1:5000->5000/tcp   desperate_leakey
106ea823fe4e        fedora:latest     /bin/sh -c 'bash -l'   2 weeks ago         Exited (0) 2 weeks ago                              determined_albattani
48ee228c9464        fedora:20         bash                   2 weeks ago         Exited (0) 2 weeks ago                              tender_torvalds
```

#### Filter by exit signal

You can use a filter to locate containers that exited with status of `137`
meaning a `SIGKILL(9)` killed them.

```console
$ docker ps -a --filter 'exited=137'

CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS                       PORTS               NAMES
b3e1c0ed5bfe        ubuntu:latest       "sleep 1000"           12 seconds ago      Exited (137) 5 seconds ago                       grave_kowalevski
a2eb5558d669        redis:latest        "/entrypoint.sh redi   2 hours ago         Exited (137) 2 hours ago                         sharp_lalande
```

Any of these events result in a `137` status:

* the `init` process of the container is killed manually
* `docker kill` kills the container
* Docker daemon restarts which kills all running containers

#### status

The `status` filter matches containers by status. You can filter using
`created`, `restarting`, `running`, `removing`, `paused`, `exited` and `dead`. For example,
to filter for `running` containers:

```console
$ docker ps --filter status=running

CONTAINER ID        IMAGE                  COMMAND             CREATED             STATUS              PORTS               NAMES
715ebfcee040        busybox                "top"               16 minutes ago      Up 16 minutes                           i_am_nostalgic
d5c976d3c462        busybox                "top"               23 minutes ago      Up 23 minutes                           top
9b6247364a03        busybox                "top"               24 minutes ago      Up 24 minutes                           nostalgic_stallman
```

To filter for `paused` containers:

```console
$ docker ps --filter status=paused

CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
673394ef1d4c        busybox             "top"               About an hour ago   Up About an hour (Paused)                       nostalgic_shockley
```

#### ancestor

The `ancestor` filter matches containers based on its image or a descendant of
it. The filter supports the following image representation:

- `image`
- `image:tag`
- `image:tag@digest`
- `short-id`
- `full-id`

If you don't specify a `tag`, the `latest` tag is used. For example, to filter
for containers that use the latest `ubuntu` image:

```console
$ docker ps --filter ancestor=ubuntu

CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS              PORTS               NAMES
919e1179bdb8        ubuntu-c1           "top"               About a minute ago   Up About a minute                       admiring_lovelace
5d1e4a540723        ubuntu-c2           "top"               About a minute ago   Up About a minute                       admiring_sammet
82a598284012        ubuntu              "top"               3 minutes ago        Up 3 minutes                            sleepy_bose
bab2a34ba363        ubuntu              "top"               3 minutes ago        Up 3 minutes                            focused_yonath
```

Match containers based on the `ubuntu-c1` image which, in this case, is a child
of `ubuntu`:

```console
$ docker ps --filter ancestor=ubuntu-c1

CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS              PORTS               NAMES
919e1179bdb8        ubuntu-c1           "top"               About a minute ago   Up About a minute                       admiring_lovelace
```

Match containers based on the `ubuntu` version `12.04.5` image:

```console
$ docker ps --filter ancestor=ubuntu:12.04.5

CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS              PORTS               NAMES
82a598284012        ubuntu:12.04.5      "top"               3 minutes ago        Up 3 minutes                            sleepy_bose
```

The following matches containers based on the layer `d0e008c6cf02` or an image
that have this layer in its layer stack.

```console
$ docker ps --filter ancestor=d0e008c6cf02

CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS              PORTS               NAMES
82a598284012        ubuntu:12.04.5      "top"               3 minutes ago        Up 3 minutes                            sleepy_bose
```

#### Create time

##### before

The `before` filter shows only containers created before the container with
given id or name. For example, having these containers created:

```console
$ docker ps

CONTAINER ID        IMAGE       COMMAND       CREATED              STATUS              PORTS              NAMES
9c3527ed70ce        busybox     "top"         14 seconds ago       Up 15 seconds                          desperate_dubinsky
4aace5031105        busybox     "top"         48 seconds ago       Up 49 seconds                          focused_hamilton
6e63f6ff38b0        busybox     "top"         About a minute ago   Up About a minute                      distracted_fermat
```

Filtering with `before` would give:

```console
$ docker ps -f before=9c3527ed70ce

CONTAINER ID        IMAGE       COMMAND       CREATED              STATUS              PORTS              NAMES
4aace5031105        busybox     "top"         About a minute ago   Up About a minute                      focused_hamilton
6e63f6ff38b0        busybox     "top"         About a minute ago   Up About a minute                      distracted_fermat
```

##### since

The `since` filter shows only containers created since the container with given
id or name. For example, with the same containers as in `before` filter:

```console
$ docker ps -f since=6e63f6ff38b0

CONTAINER ID        IMAGE       COMMAND       CREATED             STATUS              PORTS               NAMES
9c3527ed70ce        busybox     "top"         10 minutes ago      Up 10 minutes                           desperate_dubinsky
4aace5031105        busybox     "top"         10 minutes ago      Up 10 minutes                           focused_hamilton
```

#### volume

The `volume` filter shows only containers that mount a specific volume or have
a volume mounted in a specific path:

```console
$ docker ps --filter volume=remote-volume --format "table {{.ID}}\t{{.Mounts}}"

CONTAINER ID        MOUNTS
9c3527ed70ce        remote-volume

$ docker ps --filter volume=/data --format "table {{.ID}}\t{{.Mounts}}"

CONTAINER ID        MOUNTS
9c3527ed70ce        remote-volume
```

#### network

The `network` filter shows only containers that are connected to a network with
a given name or id.

The following filter matches all containers that are connected to a network
with a name containing `net1`.

```console
$ docker run -d --net=net1 --name=test1 ubuntu top
$ docker run -d --net=net2 --name=test2 ubuntu top

$ docker ps --filter network=net1

CONTAINER ID        IMAGE       COMMAND       CREATED             STATUS              PORTS               NAMES
9d4893ed80fe        ubuntu      "top"         10 minutes ago      Up 10 minutes                           test1
```

The network filter matches on both the network's name and id. The following
example shows all containers that are attached to the `net1` network, using
the network id as a filter;

```console
$ docker network inspect --format "{{.ID}}" net1

8c0b4110ae930dbe26b258de9bc34a03f98056ed6f27f991d32919bfe401d7c5

$ docker ps --filter network=8c0b4110ae930dbe26b258de9bc34a03f98056ed6f27f991d32919bfe401d7c5

CONTAINER ID        IMAGE       COMMAND       CREATED             STATUS              PORTS               NAMES
9d4893ed80fe        ubuntu      "top"         10 minutes ago      Up 10 minutes                           test1
```

#### publish and expose

The `publish` and `expose` filters show only containers that have published or exposed port with a given port
number, port range, and/or protocol. The default protocol is `tcp` when not specified.

The following filter matches all containers that have published port of 80:

```console
$ docker run -d --publish=80 busybox top
$ docker run -d --expose=8080 busybox top

$ docker ps -a

CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                   NAMES
9833437217a5        busybox             "top"               5 seconds ago       Up 4 seconds        8080/tcp                dreamy_mccarthy
fc7e477723b7        busybox             "top"               50 seconds ago      Up 50 seconds       0.0.0.0:32768->80/tcp   admiring_roentgen

$ docker ps --filter publish=80

CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS              PORTS                   NAMES
fc7e477723b7        busybox             "top"               About a minute ago   Up About a minute   0.0.0.0:32768->80/tcp   admiring_roentgen
```

The following filter matches all containers that have exposed TCP port in the range of `8000-8080`:

```console
$ docker ps --filter expose=8000-8080/tcp

CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
9833437217a5        busybox             "top"               21 seconds ago      Up 19 seconds       8080/tcp            dreamy_mccarthy
```

The following filter matches all containers that have exposed UDP port `80`:

```console
$ docker ps --filter publish=80/udp

CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

### <a name=format></a> Format the output (--format)

The formatting option (`--format`) pretty-prints container output using a Go
template.

Valid placeholders for the Go template are listed below:

| Placeholder   | Description                                                                                     |
|:--------------|:------------------------------------------------------------------------------------------------|
| `.ID`         | Container ID                                                                                    |
| `.Image`      | Image ID                                                                                        |
| `.Command`    | Quoted command                                                                                  |
| `.CreatedAt`  | Time when the container was created.                                                            |
| `.RunningFor` | Elapsed time since the container was started.                                                   |
| `.Ports`      | Exposed ports.                                                                                  |
| `.State`      | Container status (for example; "created", "running", "exited").                                 |
| `.Status`     | Container status with details about duration and health-status.                                 |
| `.Size`       | Container disk size.                                                                            |
| `.Names`      | Container names.                                                                                |
| `.Labels`     | All labels assigned to the container.                                                           |
| `.Label`      | Value of a specific label for this container. For example `'{{.Label "com.docker.swarm.cpu"}}'` |
| `.Mounts`     | Names of the volumes mounted in this container.                                                 |
| `.Networks`   | Names of the networks attached to this container.                                               |

When using the `--format` option, the `ps` command will either output the data
exactly as the template declares or, when using the `table` directive, includes
column headers as well.

The following example uses a template without headers and outputs the `ID` and
`Command` entries separated by a colon (`:`) for all running containers:

```console
$ docker ps --format "{{.ID}}: {{.Command}}"

a87ecb4f327c: /bin/sh -c #(nop) MA
01946d9d34d8: /bin/sh -c #(nop) MA
c1d3b0166030: /bin/sh -c yum -y up
41d50ecd2f57: /bin/sh -c #(nop) MA
```

To list all running containers with their labels in a table format you can use:

```console
$ docker ps --format "table {{.ID}}\t{{.Labels}}"

CONTAINER ID        LABELS
a87ecb4f327c        com.docker.swarm.node=ubuntu,com.docker.swarm.storage=ssd
01946d9d34d8
c1d3b0166030        com.docker.swarm.node=debian,com.docker.swarm.cpu=6
41d50ecd2f57        com.docker.swarm.node=fedora,com.docker.swarm.cpu=3,com.docker.swarm.storage=ssd
```

To list all running containers in JSON format, use the `json` directive:

```console
$ docker ps --format json
{"Command":"\"/docker-entrypoint.…\"","CreatedAt":"2021-03-10 00:15:05 +0100 CET","ID":"a762a2b37a1d","Image":"nginx","Labels":"maintainer=NGINX Docker Maintainers \u003cdocker-maint@nginx.com\u003e","LocalVolumes":"0","Mounts":"","Names":"boring_keldysh","Networks":"bridge","Ports":"80/tcp","RunningFor":"4 seconds ago","Size":"0B","State":"running","Status":"Up 3 seconds"}
```
