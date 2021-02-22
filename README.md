#  Terraform RnD
sample template for VNF provisioning

resource "openstack_compute_secgroup_v2" "secgroup_hms" {
  name        = "hms_secgroup"
  description = "hms grp"

  rule {
    from_port   = 22
    to_port     = 22
    ip_protocol = "tcp"
    cidr        = "0.0.0.0/0"
  }

  rule {
    from_port   = 80
    to_port     = 80
    ip_protocol = "tcp"
    cidr        = "0.0.0.0/0"
}

rule {
  from_port   = -1
  to_port     = -1
  ip_protocol = "icmp"
  cidr        = "0.0.0.0/0"
}

}

resource "openstack_blockstorage_volume_v2" "myvol" {
  name = "myvol"
  size = 15
}

resource "openstack_blockstorage_volume_v2" "myvol2" {
  name = "myvol2"
  size = 15
}


resource "openstack_compute_instance_v2" "myinstance" {
  name            = "myinstance"
  image_id        = "df470042-7eab-427b-9f79-2df0e4326bef"
  flavor_id       = "3"
  key_pair        = "vfnc"
  security_groups = ["hms_secgroup"]
  user_data   = file("scripts/script.sh")


   network {
    name = "vnf_net"
    fixed_ip_v4 = "10.0.21.12"
  }

   network {
    name = "hms"
    fixed_ip_v4 = "10.0.22.12"
  }
}

resource "openstack_compute_instance_v2" "myinstance2" {
  name            = "myinstance2"
  image_id        = "df470042-7eab-427b-9f79-2df0e4326bef"
  flavor_id       = "3"
  key_pair        = "vfnc"
  security_groups = ["hms_secgroup"]
  user_data   = file("scripts/script.sh")


   network {
    name = "vnf_net"
    fixed_ip_v4 = "10.0.21.13"
  }

   network {
    name = "hms"
    fixed_ip_v4 = "10.0.22.13"
  }

}


resource "openstack_compute_volume_attach_v2" "attached" {
  instance_id = "${openstack_compute_instance_v2.myinstance.id}"
  volume_id   = "${openstack_blockstorage_volume_v2.myvol.id}"
}


resource "openstack_compute_volume_attach_v2" "attached2" {
  instance_id = "${openstack_compute_instance_v2.myinstance2.id}"
  volume_id   = "${openstack_blockstorage_volume_v2.myvol2.id}"
}
