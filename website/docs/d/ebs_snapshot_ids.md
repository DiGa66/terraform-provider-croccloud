---
subcategory: "EBS (EC2)"
layout: "aws"
page_title: "aws_ebs_snapshot_ids"
description: |-
  Provides a list of EBS snapshot IDs.
---

[describe-snapshots]: https://docs.cloud.croc.ru/en/api/ec2/snapshots/DescribeSnapshots.html

# Data Source: aws_ebs_snapshot_ids

Provides a list of EBS snapshot IDs matching the specified criteria.

## Example Usage

```terraform
data "aws_ebs_snapshot_ids" "ebs_snapshot_ids" {
  owners = ["self"]

  filter {
    name   = "volume-size"
    values = ["40"]
  }

  filter {
    name   = "tag:Name"
    values = ["Example"]
  }
}
```

## Argument Reference

The following arguments are supported:

* `owners` - (Optional) List of the snapshot owners. Valid items are the project ID (`project@customer`) or `self`.
* `restorable_by_user_ids` - (Optional) List of the project IDs (`project@customer`).
  that can create volumes from the snapshot.
* `filter` - (Optional) One or more name/value pairs to use as filters.
	Valid names and values can be found in the [EC2 API documentation][describe-snapshots].

## Attributes Reference

* `id` - The region (e.g., `region-1`).
* `ids` - Set of EBS snapshot IDs, sorted by creation time in descending order.
