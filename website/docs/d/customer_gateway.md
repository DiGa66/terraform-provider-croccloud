---
subcategory: "VPN (Site-to-Site)"
layout: "aws"
page_title: "aws_customer_gateway"
description: |-
  Get an existing Customer Gateway.
---

# Data Source: aws_customer_gateway

Get an existing customer gateway.

## Example Usage

-> The terms VPC, Internet Gateway, VPN Gateway are equivalent.

```terraform
data "aws_customer_gateway" "selected" {
  filter {
    name   = "tag:Name"
    values = ["foo-prod"]
  }
}

resource "aws_vpc" "main" {
  cidr_block = "10.1.0.0/16"
}

resource "aws_vpn_connection" "transit" {
  vpn_gateway_id      = aws_vpc.main.id # vpc_id can be used as vpn_gateway_id
  customer_gateway_id = data.aws_customer_gateway.selected.id
  type                = data.aws_customer_gateway.selected.type
  static_routes_only  = false
}
```

## Argument Reference

The following arguments are supported:

* `id` - (Optional) The ID of the gateway.
* `filter` - (Optional) One or more name/value pairs to filter.

For more information about filtering, see the [EC2 API documentation][describe-customer-gateways].

## Attribute Reference

### Supported attributes

In addition to the arguments above, the following attributes are exported:

* `arn` - The ARN of the customer gateway.
* `bgp_asn` - The gateway's Border Gateway Protocol (BGP) Autonomous System Number (ASN).
* `ip_address` - The IP address of the gateway's Internet-routable external interface.
* `tags` - Map of key-value pairs assigned to the gateway.
* `type` - The type of customer gateway. Possible values: `ipsec.1`, `ipsec.legacy`.

### Unsupported attributes

~> **Note** These attributes may be present in the `terraform.tfstate` file but they have preset values and cannot be specified in configuration files.

The following attributes are not currently supported:

`certificate_arn`, `device_name`.

[describe-customer-gateways]: https://docs.cloud.croc.ru/en/api/ec2/customer_gateways/DescribeCustomerGateways.html
