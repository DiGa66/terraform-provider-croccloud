---
subcategory: "VPC (Virtual Private Cloud)"
layout: "aws"
page_title: "aws_subnet_ids"
description: |-
    Provides information about subnet IDs for a VPC.
---

# Data Source: aws_subnet_ids

Provides information about IDs for a vpc_id.

## Example Usage

The following shows all cidr blocks for every subnet id in a vpc.

```terraform
variable vpc_id {}

data "aws_subnets" "example" {
  filter {
    name   = "vpc-id"
    values = [var.vpc_id]
  }
}

data "aws_subnet" "example" {
  for_each = toset(data.aws_subnets.example.ids)
  id       = each.value
}

output "subnet_cidr_blocks" {
  value = [for s in data.aws_subnet.example : s.cidr_block]
}
```

The following example retrieves a set of all subnets in a VPC with a custom
tag of `Tier` set to a value of "Private" so that the `aws_instance` resource
can loop through the subnets, putting instances across availability zones.

```terraform
variable vpc_id {}

data "aws_subnets" "private" {
  filter {
    name   = "vpc-id"
    values = [var.vpc_id]
  }

  tags = {
    Tier = "Private"
  }
}

resource "aws_instance" "app" {
  for_each      = toset(data.aws_subnets.example.ids)
  ami           = "cmi-12345678" # add image id, change instance type if needed
  instance_type = "m1.micro"
  subnet_id     = each.value
}
```

Fort matching against tag `Name`, use:

```terraform
data "aws_subnet_ids" "selected" {
  filter {
    name   = "tag:Name"
    values = [""] # insert values here
  }
}
```

## Argument Reference

* `vpc_id` - (Required) The VPC ID that you want to filter from.
* `filter` - (Optional) One or more name/value pairs to use as filters.
  Subnet IDs will be selected if any one of the given values match.
	Valid names and values can be found in the [EC2 API documentation][describe-subnets].
* `tags` - (Optional) Map of tags, each pair of which must exactly match
  a pair on the desired subnets.

## Attributes Reference

* `ids` - A set of all the subnet ids found.

[describe-subnets]: https://docs.cloud.croc.ru/en/api/ec2/subnets/DescribeSubnets.html
[tf-subnets]: subnets.html
