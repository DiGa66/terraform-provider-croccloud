---
subcategory: "CodeArtifact"
layout: "aws"
page_title: "AWS: aws_codeartifact_domain"
description: |-
  Provides a CodeArtifact Domain resource.
---

# Resource: aws_codeartifact_domain

Provides a CodeArtifact Domain Resource.

## Example Usage

```terraform
resource "aws_codeartifact_domain" "example" {
  domain = "example"
}
```

## Argument Reference

The following arguments are supported:

* `domain` - (Required) The name of the domain to create. All domain names in an AWS Region that are in the same AWS account must be unique. The domain name is used as the prefix in DNS hostnames. Do not use sensitive information in a domain name because it is publicly discoverable.
* `encryption_key` - (Optional) The encryption key for the domain. This is used to encrypt content stored in a domain. The KMS Key Amazon Resource Name (ARN). The default aws/codeartifact AWS KMS master key is used if this element is absent.
* `tags` - (Optional) Key-value map of resource tags. If configured with a provider [`default_tags` configuration block](/docs/providers/aws/index.html#default_tags-configuration-block) present, tags with matching keys will overwrite those defined at the provider-level.

## Attributes Reference

In addition to all arguments above, the following attributes are exported:

* `id` - The ARN of the Domain.
* `arn` - The ARN of the Domain.
* `owner` - The AWS account ID that owns the domain.
* `repository_count` - The number of repositories in the domain.
* `created_time` - A timestamp that represents the date and time the domain was created in [RFC3339 format](https://tools.ietf.org/html/rfc3339#section-5.8).
* `asset_size_bytes` - The total size of all assets in the domain.
* `tags_all` - A map of tags assigned to the resource, including those inherited from the provider [`default_tags` configuration block](/docs/providers/aws/index.html#default_tags-configuration-block).

## Import

CodeArtifact Domain can be imported using the CodeArtifact Domain arn, e.g.,

```
$ terraform import aws_codeartifact_domain.example arn:aws:codeartifact:us-west-2:012345678912:domain/tf-acc-test-8593714120730241305
```