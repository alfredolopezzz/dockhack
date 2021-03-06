dockhack
========

Some rudimentary utils I cooked up to introspect &amp; hack docker containers from the host

Installation
============
```
sudo wget -O /usr/local/sbin/dockhack https://raw.githubusercontent.com/tavisrudd/dockhack/master/dockhack
sudo chmod +x /usr/local/sbin/dockhack
sudo dockhack install_deps
```

Usage
=====
Note, most of these commands require `root` privileges to work.
```
  dockhack install_deps
    attempts to install dockhack's deps: the `cgroup-bin` package and
    `nsenter` from `util-linux`

  dockhack last
  dockhack last_id
    prints the full id of the last container to run
    short-hand for `docker ps -l -q --no-trunc`

  dockhack id CID
  dockhack get_id CID
    resolve and print the full SHA version of a container ID

  dockhack get_prop CID PROP
    lookup a container property via `docker inspect --format`

  dockhack get_ip CID
    print the container's IPv4 address

  dockhack mem CID
  dockhack memory CID
    print the container's memory stats and limits

  dockhack net CID
    print lots of info about the network configuration of the container

  dockhack netfilter CID
    print all netfilter/iptables rules related to the container

  dockhack get_root_pid CID
    print the container's top-level PID

  dockhack pids CID
    print a comma delimited list of all PIDs in the container

  dockhack ps_outside CID
    ps -H -f -p $(pids "$CID")

  dockhack ps_inside CID
    run `ps -efH` inside the container

  dockhack psx CID
    run both ps_outside and ps_inside

  dockhack pgrep CID <pgrep args>
    run pgrep inside the container

  dockhack pstree CID
    run `pstree` scoped to the container processes

  dockhack htop CID
    run htop, on the host, with the PIDs limited to those in the container

  dockhack fsroot CID
    print the mount path of the container's filesystem inside /var/lib/docker

  dockhack cp CID source-filepath-on-host target-filepath-in-container
    the inverse of `docker cp` which copies from the container to the host

  dockhack ssh CID
    ssh into the container, assuming you've got init+sshd running

  dockhack ssh-cp-key CID [pubkey-path]
    cp your ssh pubkey into /root/.ssh/authorized_keys in the container

  The following require the `cgroup-bin` package (cgexec, lscgroup,
  etc.) and `nsenter`
  (http://man7.org/linux/man-pages/man1/nsenter.1.html) from
  util-linux version 2.23 or greater

  dockhack inside CID <any command available in the container plus its args>
    run a command inside the container cgroups + namespaces BUT without
    dropping any capabilities. This is useful for a) running
    privileged commands inside a non-privileged container and b) doing
    the equivalent of lxc-attach when running docker with
    libcontainer.
    e.g.
      ip route ...
      mount ....
      tcpdump ...

  dockhack cg_exec CID <any command available in the host plus its args>
    a wrapper around `cgexec` from `cgroup-bin` which wires up the
    required arguments. This uses the host's filesystem and, thus, the
    command must be available on the host.

  dockhack ns_enter CID <any command available in the container plus its args>
    a wrapper around `nsenter` from `util-linux` which wires up the
    required arguments. It uses the mount, uts, ipc, pid and net namespaces.

  dockhack supported_cgroups CID
    print a comma separated list of cgroups used for the container

  dockhack get_caps CID
    print the list of posix capabilities available in the container
    requires the ruby gem `cap2`

where 
  CID := { container-long-sha | container-short-sha | container-name | l | last}
      l is a short-hand for last
      e.g. 6a3827868f31
           6a3827868f31b549e2cfcf47e0f28a1ecc631aeec7a661c5a3cfa3966a66ca1d
           compassionate_lovelace
  PROP := anything that would resolve correctly in `docker inspect --format '{{PROP}}'`


You can also source this script as a bash library: `source dockhack`.
```
