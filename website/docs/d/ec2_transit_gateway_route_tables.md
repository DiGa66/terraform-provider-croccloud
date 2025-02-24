---
subcategory: "Transit Gateway"
layout: "aws"
page_title: "aws_ec2_transit_gateway_route_tables"
description: |-
  Provides list of transit gateway route table IDs.
---

[describe-tgw-rtb]: https://docs.cloud.croc.ru/en/api/ec2/transit_gateways/DescribeTransitGatewayRouteTables.html

# Data Source: aws_ec2_transit_gateway_route_tables

Provides list of transit gateway route table IDs.

## Example Usage

```terraform
data "aws_ec2_transit_gateway_route_tables" "selected" {}

output "tgw-rtb-ids" {
  value = data.aws_ec2_transit_gateway_route_tables.selected.ids
}
```

## Argument Reference

The following arguments are supported:

* `filter` - (Optional) One or more name/value pairs to use as filters.
  Valid names and values can be found in the [EC2 API documentation][describe-tgw-rtb].

## Attribute Reference

### Supported attributes

In addition to all arguments above, the following attributes are exported:

* `id` - The region (e.g., `region-1`).
* `ids` - List of transit gateway route table IDs.

### Unsupported attributes

~> **Note** This attribute may be present in the `terraform.tfstate` file but it has a preset value and cannot be specified in configuration files.

The following attribute is not currently supported: `tags`.
