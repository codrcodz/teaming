Networking
===========

The purpose of this role is to ensure Networking is setup per a set of provided specifications.

Requirements
------------

- Ansible 2.4+
- Access to CentOS/RHEL 7 repositories

Role Variables
--------------

/path/to/your/group_vars/${your_group_name}:

    ---
    # Networking configuration
      networking:
        services:                   # List of network-managing daemons
          enabled:                  # Services to be enabled/unmasked/started/restarted-on-change
          - NetworkManager
          disabled:                 # Services to be disabled/masked/stopped
          - network 
        simples:
        - simple: p1p1
          nm_controlled: "yes"
          on_boot: "no"
        teams:
        - team: team0               # Name of team, applied as "conn_name" in role
          nm_controlled: "yes"      # Determines if the interface is NetworkManager controlled
          onboot: "yes"             # Start on boot? Default is "yes" if undefined
          bootproto: "none"         # Either "dhcp" for dynamic or "none" for static IPs
          ip4: "{{ team0_ip4 }}"    # Set IP address as a host_var
          cidr: "{{ team0_cidr }}"  # Set the CIDR (i.e. 24, 16, 8, 32, etc.)
          gw4: "{{ team0_gw4 }}"    # Set gateway IP as a host_var
          defroute: "yes"           # Either "yes" or "no" to make this gw4 the default route
          zone: "public"            # Firewalld zone; default is "public" if undefined
          mtu: "9000"               # "9000" for jumbo packets; "1500" if undefined
          mode: "balance-rr"        # Set to "balance-rr" for balanced round robin
          type: "ethernet"          # This is usually "ethernet"
          team_config: "lacp"       # Defines the team configuration to be used
          slaves:                   # This is a list of the slaved devices
            - em1                   # Use * in /etc/sysconfig/network-scripts/ifcfg-*
            - em2
        - team: disabled            # Subsequent teams should use "- team: <team_name>"
          onboot: "no"              # Disabled devices could be slaved to a disabled team
          slaves:
            - em3
            - em4
            - p2p1
            - p2p2
       bridges:
       - bridge: br10
       vlans:
       - vlan: team0.101
       iproute2:
       - connection: "br10"
         rt_number: "10"
         rt_alias: "rt_br10"
         def_route_cidr: "10.0.0.0/8"
         def_route_gateway: "10.255.255.254"
    ...

/path/to/your/host_vars/${your_host_name}:

    ---
    team0_ip4: 192.168.0.5
    team0_cidr: 24
    team0_gw4: 192.168.0.254
    ...


Role Dependencies
------------

None

Example Playbook
----------------

    ---
    - name: Set up teaming
      hosts: your_host_group_here
      become: True

      roles:
        - teaming
    ...

Notes
-----
Keep in mind, you can always slave a single interface to a team, so if you'd like to simplify your network interface management, you can assign all your interfaces to a team so that they can all be managed by this single ansible role.

Also note: teaming is new to RHEL/CentOS 7. For RHEL6, please use bonding instead.

License
-------

MIT

Author Information
------------------

The Development Range Engineering, Architecture, and Modernization (DREAM) Team.
