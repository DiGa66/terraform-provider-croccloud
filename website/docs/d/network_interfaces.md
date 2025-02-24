---
subcategory: "VPC (Virtual Private Cloud)"
layout: "aws"
page_title: "aws_network_interfaces"
description: |-
    Provides a list of network interface IDs.
---

# Data Source: aws_network_interfaces

## Example Usage

The following shows all network interface IDs.

```terraform
data "aws_network_interfaces" "example" {}

output "example" {
  value = data.aws_network_interfaces.example.ids
}
```

The following example retrieves a list of all network interface IDs with a custom tag of `Name` set to a value of `test`.

```terraform
data "aws_network_interfaces" "example1" {
  tags = {
    Name = "test"
  }
}

output "example1" {
  value = data.aws_network_interfaces.example.ids
}
```

The following example retrieves a network interface IDs which associated with specific subnet.

```terraform
data "aws_network_interfaces" "example2" {
  filter {
    name   = "subnet-id"
    values = ["subnet-xxxxxxxx"]
  }
}

output "example2" {
  value = data.aws_network_interfaces.example.ids
}
```

## Argument Reference

* `tags` - (Optional) Map of tags, each pair of which must exactly match
  a pair on the desired network interfaces.
* `filter` - (Optional) One or more name/value pairs to use as filters.
	Valid names and values can be found in the [EC2 API documentation][describe-network-interfaces].

## Attributes Reference

* `id` - The region (e.g., `region-1`).
* `ids` - A list of all the network interface IDs found.

[describe-network-interfaces]: https://docs.cloud.croc.ru/en/api/ec2/network_interfaces/DescribeNetworkInterfaces.html
