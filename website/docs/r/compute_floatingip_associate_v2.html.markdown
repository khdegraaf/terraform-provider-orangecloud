---
layout: "orangecloud"
page_title: "OrangeCloud: orangecloud_compute_floatingip_associate_v2"
sidebar_current: "docs-orangecloud-resource-compute-floatingip-associate-v2"
description: |-
  Associate a floating IP to an instance
---

# orangecloud\_compute\_floatingip_associate_v2

Associate a floating IP to an instance. This can be used instead of the
`floating_ip` options in `orangecloud_compute_instance_v2`.

## Example Usage

### Automatically detect the correct network

```hcl
resource "orangecloud_compute_instance_v2" "instance_1" {
  name            = "instance_1"
  image_id        = "ad091b52-742f-469e-8f3c-fd81cadf0743"
  flavor_id       = 3
  key_pair        = "my_key_pair_name"
  security_groups = ["default"]
}

resource "orangecloud_networking_floatingip_v2" "fip_1" {
  pool = "my_pool"
}

resource "orangecloud_compute_floatingip_associate_v2" "fip_1" {
  floating_ip = "${orangecloud_networking_floatingip_v2.fip_1.address}"
  instance_id = "${orangecloud_compute_instance_v2.instance_1.id}"
}
```

### Explicitly set the network to attach to

```hcl
resource "orangecloud_compute_instance_v2" "instance_1" {
  name            = "instance_1"
  image_id        = "ad091b52-742f-469e-8f3c-fd81cadf0743"
  flavor_id       = 3
  key_pair        = "my_key_pair_name"
  security_groups = ["default"]

  network {
    name = "my_network"
  }

  network {
    name = "default"
  }
}

resource "orangecloud_networking_floatingip_v2" "fip_1" {
  pool = "my_pool"
}

resource "orangecloud_compute_floatingip_associate_v2" "fip_1" {
  floating_ip = "${orangecloud_networking_floatingip_v2.fip_1.address}"
  instance_id = "${orangecloud_compute_instance_v2.instance_1.id}"
  fixed_ip    = "${orangecloud_compute_instance_v2.instance_1.network.1.fixed_ip_v4}"
}
```

## Argument Reference

The following arguments are supported:

* `region` - (Optional) The region in which to obtain the V2 Compute client.
    Keypairs are associated with accounts, but a Compute client is needed to
    create one. If omitted, the `region` argument of the provider is used.
    Changing this creates a new floatingip_associate.

* `floating_ip` - (Required) The floating IP to associate.

* `instance_id` - (Required) The instance to associte the floating IP with.

* `fixed_ip` - (Optional) The specific IP address to direct traffic to.

## Attributes Reference

The following attributes are exported:

* `region` - See Argument Reference above.
* `floating_ip` - See Argument Reference above.
* `instance_id` - See Argument Reference above.
* `fixed_ip` - See Argument Reference above.

## Import

This resource can be imported by specifying all three arguments, separated
by a forward slash:

```
$ terraform import orangecloud_compute_floatingip_associate_v2.fip_1 <floating_ip>/<instance_id>/<fixed_ip>
```
