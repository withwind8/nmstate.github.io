<!-- vim-markdown-toc GFM -->

* [NMState state examples](#nmstate-state-examples)
    * [Interfaces: Generic state manipulation](#interfaces-generic-state-manipulation)
        * [Setting the iface up:](#setting-the-iface-up)
        * [Setting the iface down](#setting-the-iface-down)
        * [Removing an iface:](#removing-an-iface)
    * [Interfaces: ethernet](#interfaces-ethernet)
    * [Interfaces: bond](#interfaces-bond)
    * [Interfaces: ovs-bridge](#interfaces-ovs-bridge)
    * [Interfaces: dummy](#interfaces-dummy)
    * [Interfaces: VLAN](#interfaces-vlan)
    * [Interfaces: VXLAN](#interfaces-vxlan)
    * [Interface: Linux Bridge](#interface-linux-bridge)
    * [Interfaces: Team](#interfaces-team)
    * [Interfaces: Veth](#interfaces-veth)
    * [Route](#route)
    * [DNS](#dns)
    * [Dynamic IP Configuration](#dynamic-ip-configuration)

<!-- vim-markdown-toc -->

# NMState state examples
This page includes various configuration state examples of various entities.
For readability, the examples are shown in yaml format.

## Interfaces: Generic state manipulation
The examples below show how to control the iface state, regardless of
its type.

### Setting the iface up:
The `up` state also covers the creation of a virtual iface.
```yaml
interfaces:
- name: eth0
  type: ethernet
  state: up
```

### Setting the iface down
For virtual ifaces, it removes the iface. Nevertheless, the correct way to
remove an interface is using the `absent` keyword.
```yaml
interfaces:
- name: foo0
  type: unknown
  state: down
```

### Removing an iface:
For a physical device (like an ethernet NIC), it just removes any
configuration from the iface but does not (or can) physically delete it.
```yaml
interfaces:
- name: dummy0
  type: dummy
  state: absent
```

## Interfaces: ethernet
The example includes 3 ethernet interfaces, two with static IPv4 and IPv6 and
the 3rd with no IP and with the state set to down.

```yaml
interfaces:
- name: eth0
  type: ethernet
  state: up
  ipv4:
    address:
    - ip: 192.168.122.250
      prefix-length: 24
    enabled: true
  ipv6:
    address:
    - ip: 2001:db8::1:1
      prefix-length: 64
    enabled: true
- name: eth1
  type: ethernet
  state: up
  ipv4:
    address:
    - ip: 192.168.100.192
      prefix-length: 24
    enabled: true
  ipv6:
    address:
    - ip: 2001:db8::2:1
      prefix-length: 64
    enabled: true
- name: eth2
  type: ethernet
  state: down
  ipv4:
    enabled: false
```

## Interfaces: bond
The example defines a bond with two ports and an IPv4 static address.

The bond mode is specified to be `balance-rr` and the `miimon` options is
specified.

```yaml
interfaces:
- name: bond99
  type: bond
  state: up
  ipv4:
    address:
    - ip: 192.0.2.0
      prefix-length: 24
    enabled: true
  link-aggregation:
    mode: balance-rr
    options:
      miimon: '140'
    port:
    - eth3
    - eth2

```

## Interfaces: ovs-bridge
The example defines an openvswitch bridge and attaches to it the
eth3 interface (as a port).

The bridge has spanning tree (stp) enabled.

```yaml
interfaces:
- name: ovs-br0
  type: ovs-bridge
  state: up
  bridge:
    options:
      stp: true
    port:
    - name: eth3
```

The following example shows a bridge with a system (eth3) and an internal (ovs0)
ports. The internal interface represents the bridge itself in kernel and it can
be used for IPv4/IPv6 configuration.

```yaml
interfaces:
- name: ovs0
  type: ovs-interface
  state: up
  ipv4:
    enabled: true
    address:
      - ip: 192.0.2.1
        prefix-length: 24
- name: ovs-br0
  type: ovs-bridge
  state: up
  bridge:
    options:
      stp: true
    port:
    - name: eth3
    - name: ovs0
```

## Interfaces: dummy

```yaml
interfaces:
- name: ;vdsmdummy;
  type: unknown
  state: down
  ipv4:
    enabled: false
```

## Interfaces: VLAN

```yaml
---
interfaces:
  - name: eth1.101
    type: vlan
    state: up
    vlan:
      base-iface: eth1
      id: 101
```

## Interfaces: VXLAN

```yaml
---
interfaces:
  - name: eth1.101
    type: vxlan
    state: up
    vxlan:
      base-iface: eth1
      id: 101
      remote: 192.0.2.2
```

## Interface: Linux Bridge

```yaml
---
interfaces:
  - name: linux-br0
    type: linux-bridge
    state: up
    bridge:
      options:
        group-forward-mask: 0
        mac-ageing-time: 300
        multicast-snooping: true
        stp:
          enabled: true
          forward-delay: 15
          hello-time: 2
          max-age: 20
          priority: 32768
      port:
        - name: eth1
          stp-hairpin-mode: false
          stp-path-cost: 100
          stp-priority: 32
```

## Interfaces: Team

```yaml
---
interfaces:
- name: team0
  type: team
  state: up
  team:
    ports:
    - name: eth1
    - name: eth2
    runner:
      name: loadbalance
```

## Interfaces: Veth

Create a veth interface and a peer with link-up:

```yaml
---
interfaces:
- name: veth1
  type: veth
  state: up
  veth:
    peer: veth2
```

To create a veth with the peers being hold by an ovs bridge and a linux bridge,
the peer must be defined in the desired state too.

```yaml
---
interfaces:
- name: veth1
  type: veth
  state: up
  veth:
    peer: veth2
- name: veth2
  type: veth
  state: up
  veth:
    peer: veth1
- name: ovs-br0
  type: ovs-bridge
  state: up
  bridge:
    options:
      stp: true
    port:
      - name: veth1
- name: linux-br0
  type: linux-bridge
  state: up
  bridge:
    port:
      - name: veth2
```

## Route

```yaml
---
interfaces:
  - name: eth1
    type: ethernet
    state: up
    ipv4:
      address:
      - ip: 192.0.2.251
        prefix-length: 24
      dhcp: false
      enabled: true

routes:
  config:
  - destination: 198.51.100.0/24
    metric: 150
    next-hop-address: 192.0.2.1
    next-hop-interface: eth1
    table-id: 254
```

## DNS

```yaml
---
dns-resolver:
  config:
    search:
    - example.com
    - example.org
    server:
    - 2001:4860:4860::8888
    - 8.8.8.8
```

## Dynamic IP Configuration

```yaml
---
interfaces:
  - name: eth1
    type: ethernet
    state: up
    ipv4:
      enabled: true
      dhcp: true
      auto-dns: true
      auto-gateway: true
      auto-routes: true
    ipv6:
      enabled: true
      autoconf: true
      dhcp: true
      auto-dns: true
      auto-gateway: true
      auto-routes: true
```
