mdadm
=========

This role creates RAID arrays.

Requirements
------------

When using Gentoo, ensure that genkernel-next is installed.

Role Variables
--------------

Each array is defined as a dictionary object in the `mdadm_arrays`. Each key is
the device name, md0 for /dev/md0, md1 for /dev/md1, etc.

```yaml
mdadm_arrays:
  md0
    name: 0
    level: 10
    raid-devices: 4
    options: "--spare-devices=1"
    devices:
      - "/dev/sda3"
      - "/dev/sdb3"
      - "/dev/sdc3"
      - "/dev/sdd3"
      - "/dev/sde3"
```
- *name* of the RAID device.
- *level* is the type of RAID being either 0, 1, 4, 5, 6, or 10.
- *raid-devices* specifies the number of devices that will be in the raid.
- *options* contains any other of the mdadm create options.
- *devices* are the devices that should be a part of the array.


Dependencies
------------

This role doesn't require any roles but the drives must be partitioned first

Example Playbook
----------------

```yml
- hosts: localhost
  vars:
    mdadm_arrays:
      md0
        name: home
        level: 10
        raid-devices: 4
        options: "--spare-devices=1"
        devices:
          - "/dev/sda3"
          - "/dev/sdb3"
          - "/dev/sdc3"
          - "/dev/sdd3"
          - "/dev/sde3"
  pre_tasks:
    - name: "Create Partition Tables"
      parted:
        device: "{{ item }}"
        state: present
        label: gpt
      with_items:
        - "/dev/sda"
        - "/dev/sdb"
        - "/dev/sdc"
        - "/dev/sdd"
        - "/dev/sde"

    - name: "Partition drive"
      parted:
        device: "{{ item['device'] }}"
        part_start: "{{ item['start'] }}"
        flags: "{{ item['flags']|default([]) }}"
        name: "{{ item['name'] }}"
        state: present
        align: optimal
        number: "{{ num }}"
        part_end: "{{ item['end'] }}"
        unit: "MiB"
      with_items:
        - device: /dev/sda
          start: 1
          flags: []
          name: ''
          end: 3
        - device: /dev/sda
          start: 3
          flags: []
          name: ''
          end: 131
        - device: /dev/sda
          start: 131
          flags: []
          name: ''
          end: "-1"
      # Same for sdb, sdc, sdd, and sde or use with_nested.
      loop_control:
        index_var: num
  roles:
     - the-paulus.ansible-mdadm
```

License
-------

BSD
