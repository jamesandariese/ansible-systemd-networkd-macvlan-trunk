systemd-networkd-macvlan-trunk
=========

Setup a macvlan trunk and virtual ethernet device in bridge mode.

There is an issue commonly experienced with macvlan: you cannot communicate
between the parent device and children.  When used with VMs or containers, this
creates a situation where the hypervisor/container-host cannot communicate with
the children but the children and the parent can communicate with all other
hosts on the network.

Using this role, you can overcome the issue described above by creating a trunk
device from your physical ethernet device as well as a virtual device which will
be able to communicate with other virtual devices also created from this trunk.

Only the first virtual device is created for you but more may be created with,
e.g., `ip l add link trunk meth1 type macvlan` (if using the defaults for this
role).

Requirements
------------

You must be able to use systemd-networkd to use this role.  This role assumes
that systemd-networkd is used (it will attempt to enable it which will be a noop)
or that NetworkManager is currently in use (and it will be disabled in the
process of using this role).

Role Variables
--------------

### `trunk_physical_mac_address`
String: MAC Address of physical trunk device

### `meth_if_name`
String: Name of virtual interface to be created

### `trunk_if_name`
String: Name given to physical trunk device by renaming it.

### `network_static_ip`
String: IP address given to virtual interface.  This should match what was set for the trunk device so that reconfiguring will not cause loss of connectivity.

### `network_size`
String: Network size for virtual interface.  24 is a common value.  This must match your existing network for connectivity.

### `network_gateway`
String: Gateway (router) IP for your network.

### `network_domains`
String List: Domains present in your network.  `local`, `lan`, and `localdomain` are common values but this should match your DNS server's settings.

### `network_dns_servers`
String List: IP of DNS servers in your network.

Example Playbook
----------------

    # hosts
    ---
    all:
      vars:
        network_size: 24
        network_gateway: 192.168.1.1
        network_domains:
          - internal.caddr.local
          - caddr.local
        network_dns_servers:
          - 192.168.1.1
          - 192.168.1.254
      hosts:
        192.168.1.2
          network_static_ip: 192.168.1.2
          trunk_physical_mac_address: 00:80:10:17:92:31
        ninetails.caddr.local:
          network_static_ip: 192.168.1.35
          trunk_physical_mac_address: 00:80:10:e2:aa:17

    # main.yml
    ---
    - hosts: servers
      roles:
         - role: jamesandariese.systemd-networkd-macvlan-trunk

License
-------

MIT

Author and Copyright
--------------------

Copyright 2022, James Andariese (first name at strudelline.net).
