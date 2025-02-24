---
subcategory: "VPC (Virtual Private Cloud)"
layout: "aws"
page_title: "aws_vpcs"
description: |-
    Provides a list of VPC IDs in a region.
---

# Data Source: aws_vpcs

Provides a list of VPC IDs for a region.

The following example retrieves a list of VPC IDs with a custom tag of `service` set to a value of "production".

## Example Usage

The following shows outputing all VPC IDs.

```terraform
data "aws_vpcs" "foo" {
  tags = {
    service = "production"
  }
}

output "foo" {
  value = data.aws_vpcs.foo.ids
}
```

## Argument Reference

* `tags` - (Optional) Map of tags, each pair of which must exactly match
  a pair on the desired VPCs.
* `filter` - (Optional) One or more name/value pairs to use as filters.
  A VPC will be selected if any one of the given values matches.
    * _Valid values:_ See supported names and values in [EC2 API documentation][describe-vpcs]

## Attributes Reference

* `id` - The region (e.g., `region-1`).
* `ids` - A list of all the VPC IDs found.

[describe-vpcs]: https://docs.cloud.croc.ru/en/api/ec2/vpcs/DescribeVpcs.html
