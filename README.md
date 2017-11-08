Teaming
===========

The purpose of this role is to ensure teaming is setup per a set of provided specifications.

Requirements
------------

- Ansible 2.4+
- Access to CentOS/RHEL 7 repositories

Role Variables
--------------

/path/to/your/group_vars/${your_group_name}:

    ---
    # Teaming configuration
      teams:
      - team: team0               # Name of team, applied as "conn_name" in role
        onboot: "yes"             # Start on boot? Default is "yes" if undefined
        ip4: "{{ team0_ip4 }}"    # Set IP address as a host_var
        cidr: "{{ team0_cidr }}"  # Set the CIDR (i.e. 24, 16, 8, 32, etc.)
        gw4: "{{ team0_gw4 }}"    # Set gateway IP as a host_var
        zone: "public"            # Firewalld zone; default is "public" if undefined
        mtu: "9000"               # "9000" for jumbo packets; "1500" if undefined
        mode: "balance-rr"        # Set to "balance-rr" for balanced round robin
        type: "ethernet"          # This is usually "ethernet"
        slaves:                   # This is a list of the slaved devices
          - em1                   # Use * in /etc/sysconfig/network-scripts/ifcfg-*
          - em2
      - team: disabled            # Subsequent teams should use "- team: <team_name>"
        onboot: "no"              # Disabled devices should be slaved to a disabled team
        slaves:
          - em3
          - em4
          - p2p1
          - p2p2
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
