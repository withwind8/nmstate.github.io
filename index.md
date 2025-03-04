# We are NMState!
A declarative network manager API for hosts.

## What is it?
NMState is a library with an accompanying command line tool that manages
host networking settings in a declarative manner.
The networking state is described by a pre-defined schema.
Reporting of current state and changes to it (desired state) both conform to
the schema.

NMState is aimed to satisfy enterprise needs to manage host networking through
a northbound declarative API and multi provider support on the southbound.
NetworkManager acts as the main (and currently the only) provider supported.

Visit the [examples page](./examples.md) to see what it can do.

## Features
- [ethernet](./examples.md#interfaces-ethernet)
- [bond](./examples.md#interfaces-bond)
- [Vlan](./examples.md#interfaces-vlan)
- [Vxlan](./examples.md#interfaces-vxlan)
- [Linux bridge](./examples.md#interface-linux-bridge)
- [ovs-bridge](./examples.md#interfaces-ovs-bridge)
- [dummy](./examples.md#interfaces-dummy)
- [team](./examples.md#interfaces-team)
- [veth](./features/veth.md)
- [IPv4/IPv6 addresses (static)](./examples.md#interfaces-ethernet)
- [IPv4/IPv6 route](./examples.md#route)
- [IPv4/IPv6 DNS client configuration](./examples.md#dns)
- [LLDP](./features/lldp.md)
- [Open vSwitch Patch Port](./features/ovs_patch.md)
- [Open vSwitch database plugin](./features/ovsdb.md)
- [Generate Network Configuration](./features/gen_conf.md)

## Example output

```yaml
---
dns:
  config:
    server:
      - 192.0.2.1
    search:
      - example.org
routes:
  config:
    - destination: 0.0.0.0/0
      next-hop-interface: eth1
      next-hop-address: 192.0.2.1
interfaces:
  - name: eth1
    type: ethernet
    description: Main-NIC
    state: up
    ipv4:
      enabled: true
      dhcp: false
      address:
        - ip: 192.0.2.9
          prefix-length: 24
    ipv6:
      enabled: false
```

## Documentation
- [Nmstate Quick Start Guide](./user/quick_guide.md)
- [Nmstate Developer Guide](./devel/dev_guide.md)
- [Design](./devel/design/networking-api.md)
- [API documentation](./devel/api.md)
- [Plugin Design](./devel/plugin.md)
- [Varlink support for libnmstate](./devel/varlink-libnmstate.md)

## Related projects
- [kubernetes-nmstate](https://nmstate.io/kubernetes-nmstate)
- [nmpolicy](https://nmstate.io/nmpolicy)

## Contacts
- You can find us on #nmstate IRC channel on [Libera](https://libera.chat/).
  The maintainers are Gris, edwardh and ffmancera, please feel free to ask
  whatever you need!

- Nmstate uses the nmstate-devel@lists.fedorahosted.org for discussions. To
  subscribe you can send an email with 'subscribe' in the subject to
  nmstate-devel-join@lists.fedorahosted.org or visit the [mailing list
page](https://lists.fedorahosted.org/admin/lists/nmstate-devel.lists.fedorahosted.org).

- You can also report any issue on our [official Github repository](https://github.com/nmstate/nmstate)

