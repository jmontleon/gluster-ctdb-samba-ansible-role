Introduction
============
This role will configure a set of hosts (not tested with more than two) into a gluster cluster with Samba and CTDB running on top. It includes configuration of a WS-Discovery daemon so that newer Windows clients can find your Samba server as well.

This playbook will configure a Copr repo on the system which include a WS-Discovery package, as well as a custom selinux package. The selinux package is primarily allowing ctdb, samba, and ctdb to access their config files on the fuse file system. From there it will additionally install gluster, ctdb, and samba, configure the disk partitions if necessary, lvm, gluster, ctdb, samba, and wsdd.

Usage
-----
- Install ansible
- Create a host group called gluster-ctb-samba with your cluster nodes specified.
  For example:
```
[gluster-ctb-samba]
node1.example.com
node2.example.com
```
- Copy play.yml.example to play.yaml and modify the vars to suit your tastes.
- Run `ANSIBLE_ROLE_PATH=./ ansible-playbook play.yaml`

The playbook is idempodent and can be rerun if you need to make changes to service configurations.

Description of variables
------------------------
- lvm_pvs: A list of physical volumes that will be used for the volume group. The partitions do not need to exist ahead of time, but you need to make sure they are the correct size or sufficient space is available to create them.
-  lvm_vg_name: The lvm volume group name you'd like to use
-  lvm_lv_name: The lvm logical volume name you'd like to use
-  lvm_lv_size: The size of the lvm logical volume that will be created. 100%FREE will use all available space.
-  gluster_brick_name: The brick name to be used to set up your first volume
-  gluster_fuse_mount: The mount point to be used for the gluster-fuse client mount
-  gluster_volume_mount: The mount point to be used for the logical volume that will be used by gluster
-  gluster_volume_name: The gluster volume name that will be created
-  ctdb_public_address_w_mask: The IP Address/Mask to be used for the shared CTDB IP address
-  ctdb_interface: The interface to be configured with the ctdb public address
-  samba_path: The path of your samba share relative to the gluster volume
-  samba_share_name: The name of the samba share
-  samba_server_name: The name of the server as it will appear to windows clients. This should resolve to the CTDB public address
-  samba_users: A list of user names with passwords that can access the samba share 
-  samba_workgroup: The workgroup for discovery. It should match your windows hosts and unless you changed it it is probably WORKGROUP.

TODO
----
- Handle some gotchas. The configs for samba, ctdb, and wsdd are stored on the gluster fuse mount so that there is nothing special that needs to be done to keep both hosts in sync. But on reboot services may not come up depending on whether the mount completes in time. There's probably ways to handle this. Off the top of my head, templatize a simple playbook and run it via cron to ensure the fuse mount is mounted, ctdb is started (which in turn starts smb), and restarts wsdd if necessary. If that proves too tedious the configs could be simply created on each host... which would probably eliminate the need for a selinux package as well. The downside is any changes to configs would have to be done on both hosts.
- Add some power management options. No one wants to heat their house with a home storage cluster. Setting vm.swapiness, noatime, and hdparm -B and -S options ccan help keep disks from spinning when not necessary.
- Maybe handle more complex cluster scenarios. More replicas, arbiters, etc.
- Maybe handle multiple volumes at install.
- Maybe package this repo into an rpm.
