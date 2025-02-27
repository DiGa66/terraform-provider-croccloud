---
subcategory: "EC2 (Elastic Compute Cloud)"
layout: "aws"
page_title: "aws_key_pair"
description: |-
    Provides information about a specific EC2 key pair.
---

[describe-key-pairs]: https://docs.cloud.croc.ru/en/api/ec2/key_pairs/DescribeKeyPairs.html

# Data Source: aws_key_pair

Provides information about a specific EC2 key pair.

## Example Usage

The following example shows how to get an EC2 key pair from its name.

```terraform
data "aws_key_pair" "example" {
  key_name = "test"
  filter {
    name   = "tag:Component"
    values = ["web"]
  }
}

output "fingerprint" {
  value = data.aws_key_pair.example.fingerprint
}

output "name" {
  value = data.aws_key_pair.example.key_name
}

output "id" {
  value = data.aws_key_pair.example.id
}
```

## Argument Reference

The arguments of this data source act as filters for querying the available key pairs.
The given filters must match exactly one key pair whose data will be exported as attributes.

* `filter` –  (Optional) One or more name/value pairs to use as filters.
    * _Valid values_: See valid names and values in [EC2 API documentation][describe-key-pairs]
* `key_name` – (Optional) The name of the key pair.
* `key_pair_id` – (Optional) The ID of the key pair.

## Attributes Reference

In addition to all arguments above, the following attributes are exported:

* `arn` – The ARN of the key pair.
* `fingerprint` – The SHA-1 digest of the DER encoded private key.
* `id` – The ID of the key pair.
* `tags` – The map of tags assigned to the key pair.
