---
subcategory: "Transit Gateway"
layout: "aws"
page_title: "aws_ec2_transit_gateway"
description: |-
  Manages a transit gateway.
---

[default-tags]: https://www.terraform.io/docs/providers/aws/index.html#default_tags-configuration-block
[tgw]: https://docs.cloud.croc.ru/en/services/tgw/tgw.html#transitgatewaymanual
[timeouts]: https://www.terraform.io/docs/configuration/blocks/resources/syntax.html#operation-timeouts

# Resource: aws_ec2_transit_gateway

Manages a transit gateway. For details about transit gateways, see the [user documentation][tgw].

## Example Usage

```terraform
resource "aws_ec2_transit_gateway" "example" {
  description = "tf example"

  tags = {
    Name = "tf-tgw"
  }
}
```

## Argument Reference

The following arguments are supported:

* `default_route_table_association` - (Optional) Indicates whether the association with default association route table is created automatically.
  Valid values are `disable`, `enable`. Defaults to `enable`.
* `default_route_table_propagation` - (Optional) Indicates whether the routes are automatically propagated to the default propagation route table.
  Valid values are `disable`, `enable`. Defaults to `enable`.
* `description` - (Optional) The description of the transit gateway.
* `shared_owners` - (Optional) List of project IDs (`project@customer`) that are granted access to the transit gateway.
* `tags` - (Optional) Map of tags to assign to the transit gateway.
  If configured with a provider [`default_tags` configuration block][default-tags] present,
  tags with matching keys will overwrite those defined at the provider-level.

## Attribute Reference

### Supported attributes

In addition to all arguments above, the following attributes are exported:

* `association_default_route_table_id` - The ID of the default association route table.
* `id` - The ID of the transit gateway.
* `owner_id` - The ID of the project that owns the transit gateway.
* `propagation_default_route_table_id` - The ID of the default propagation route table.
* `tags_all` - Map of tags assigned to the resource, including those inherited from the provider [`default_tags` configuration block][default-tags].

### Unsupported attributes

~> **Note** These attributes may be present in the `terraform.tfstate` file but they have preset values and cannot be specified in configuration files.

The following attributes are not currently supported:

`amazon_side_asn`, `arn`, `auto_accept_shared_attachments`, `dns_support`, `multicast_support`, `transit_gateway_cidr_blocks`, `vpn_ecmp_support`.

## Timeouts

`aws_ec2_transit_gateway` provides the following [Timeouts][timeouts] configuration options:

* `create` - (Default `10 minutes`) How long to wait for the transit gateway to be created.
* `update` - (Default `10 minutes`) How long to wait for the transit gateway to be updated.
* `delete` - (Default `10 minutes`) How long to wait for the transit gateway to be deleted.

## Import

The transit gateway can be imported using `id`, e.g.,

```
$ terraform import aws_ec2_transit_gateway.example tgw-12345678
```
