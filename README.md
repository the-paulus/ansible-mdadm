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
```
- *name* of the RAID device.
- *level* - type of RAID being either 0, 1, 4, 5, 6, or 10.
- *raid-devices* specifies the number of devices that will be in the raid.
- *options* contains any of the mdadm create options.
- *devices - Specifies the devices that should be a part of the array.


Dependencies
------------

None

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
  roles:
     - the-paulus.ansible-mdadm
```

License
-------

BSD
