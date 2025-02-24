---
subcategory: "EBS (EC2)"
layout: "aws"
page_title: "aws_ebs_volume"
description: |-
  Provides information about an EBS volume.
---

# Data Source: aws_ebs_volume

Provides information about an EBS volume for use in other resources.

## Example Usage

```terraform
data "aws_ebs_volume" "ebs_volume" {
  most_recent = true

  filter {
    name   = "volume-type"
    values = ["st2"]
  }

  filter {
    name   = "tag:Name"
    values = ["Example"]
  }
}
```

## Argument Reference

The following arguments are supported:

* `most_recent` - (Optional) If more than one result is returned, use the most recent Volume.
* `filter` - (Optional) One or more name/value pairs to filter.

For more information about filtering, see the [EC2 API documentation][describe-volumes].

## Attributes Reference

### Supported attributes

In addition to all arguments above, the following attributes are exported:

* `id` - The volume ID (e.g., vol-12345678).
* `volume_id` - The volume ID (e.g., vol-12345678).
* `arn` - Amazon Resource Name (ARN) of the volume.
* `availability_zone` - The AZ where the EBS volume exists.
* `iops` - The amount of IOPS for the disk.
* `size` - The size of the drive in GiB.
* `snapshot_id` - The snapshot_id the EBS volume is based off.
* `volume_type` - The type of EBS volume.
* `tags` - A map of tags for the resource.
* `throughput` - The throughput that the volume supports, in MiB/s.

### Unsupported attributes

~> **Note** These attributes may be present in the `terraform.tfstate` file but they have preset values and cannot be specified in configuration files.

The following attributes are not currently supported:

`encrypted`, `kms_key_id`, `multi_attach_enabled`, `outpost_arn`.

[describe-volumes]: https://docs.cloud.croc.ru/en/api/ec2/volumes/DescribeVolumes.html
