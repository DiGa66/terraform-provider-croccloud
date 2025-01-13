---
subcategory: "SageMaker"
layout: "aws"
page_title: "AWS: aws_sagemaker_feature_group"
description: |-
  Provides a SageMaker Feature Group resource.
---

# Resource: aws_sagemaker_feature_group

Provides a SageMaker Feature Group resource.

## Example Usage

Basic usage:

```terraform
resource "aws_sagemaker_feature_group" "example" {
  feature_group_name             = "example"
  record_identifier_feature_name = "example"
  event_time_feature_name        = "example"
  role_arn                       = aws_iam_role.test.arn

  feature_definition {
    feature_name = "example"
    feature_type = "String"
  }

  online_store_config {
    enable_online_store = true
  }
}
```

## Argument Reference

The following arguments are supported:

* `feature_group_name` - (Required) The name of the Feature Group. The name must be unique within an AWS Region in an AWS account.
* `record_identifier_feature_name` - (Required) The name of the Feature whose value uniquely identifies a Record defined in the Feature Store. Only the latest record per identifier value will be stored in the Online Store.
* `event_time_feature_name` - (Required) The name of the feature that stores the EventTime of a Record in a Feature Group.
* `description` (Optional) - A free-form description of a Feature Group.
* `role_arn` (Required) - The Amazon Resource Name (ARN) of the IAM execution role used to persist data into the Offline Store if an `offline_store_config` is provided.
* `feature_definition` (Optional) - A list of Feature names and types. See [Feature Definition](#feature-definition) Below.
* `offline_store_config` (Optional) - The Offline Feature Store Configuration. See [Offline Store Config](#offline-store-config) Below.
* `online_store_config` (Optional) - The Online Feature Store Configuration. See [Online Store Config](#online-store-config) Below.
* `tags` - (Optional) Map of resource tags for the resource. If configured with a provider [`default_tags` configuration block](/docs/providers/aws/index.html#default_tags-configuration-block) present, tags with matching keys will overwrite those defined at the provider-level.

### Feature Definition

* `feature_name` - (Required) The name of a feature. `feature_name` cannot be any of the following: `is_deleted`, `write_time`, `api_invocation_time`.
* `feature_type` - (Required) The value type of a feature. Valid values are `Integral`, `Fractional`, or `String`.

### Offline Store Config

* `enable_online_store` - (Optional) Set to `true` to disable the automatic creation of an AWS Glue table when configuring an OfflineStore.
* `s3_storage_config` - (Required) The Amazon Simple Storage (Amazon S3) location of OfflineStore. See [S3 Storage Config](#s3-storage-config) Below.
* `data_catalog_config` - (Optional) The meta data of the Glue table that is autogenerated when an OfflineStore is created. See [Data Catalog Config](#data-catalog-config) Below.

### Online Store Config

* `disable_glue_table_creation` - (Optional) Set to `true` to turn Online Store On.
* `security_config` - (Required) Security config for at-rest encryption of your OnlineStore. See [Security Config](#security-config) Below.

#### S3 Storage Config

* `kms_key_id` - (Optional) The AWS Key Management Service (KMS) key ID of the key used to encrypt any objects written into the OfflineStore S3 location.
* `s3_uri` - (Required) The S3 URI, or location in Amazon S3, of OfflineStore.

#### Data Catalog Config

* `catalog` - (Optional) The name of the Glue table catalog.
* `database` - (Optional) The name of the Glue table database.
* `table_name` - (Optional) The name of the Glue table.

#### Security Config

* `kms_key_id` - (Optional) The ID of the AWS Key Management Service (AWS KMS) key that SageMaker Feature Store uses to encrypt the Amazon S3 objects at rest using Amazon S3 server-side encryption.

## Attributes Reference

In addition to all arguments above, the following attributes are exported:

* `name` - The name of the Feature Group.
* `arn` - The Amazon Resource Name (ARN) assigned by AWS to this feature_group.
* `tags_all` - A map of tags assigned to the resource, including those inherited from the provider [`default_tags` configuration block](/docs/providers/aws/index.html#default_tags-configuration-block).

## Import

Feature Groups can be imported using the `name`, e.g.,

```
$ terraform import aws_sagemaker_feature_group.test_feature_group feature_group-foo
```