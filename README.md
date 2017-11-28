Introduction
============
This role will configure a set of hosts (not tested with more than two) into a gluster cluster with Samba and CTDB running on top. It includes configuration of a WS-Discovery daemon so that newer Windows clients can find your Samba server as well.

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
- Run `ANSIBLE_ROLE_PATH=./ ansible-playbook play.yaml

Description of variables
------------------------
- lvm_pvs: A list of physical volumes that will be used for the volume group. The partitions do not need to exist ahead of time.
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
