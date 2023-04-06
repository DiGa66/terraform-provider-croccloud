---
subcategory: "EC2 (Elastic Compute Cloud)"
layout: "aws"
page_title: "CROC Cloud: aws_ami"
description: |-
  Get information on an Amazon Machine Image (AMI).
---

[describe-images]: https://docs.cloud.croc.ru/en/api/ec2/images/DescribeImages.html
[tf-ami-ids]: ami_ids.html

# Data Source: aws_ami

Use this data source to get the ID of a registered image for use in other resources.

## Example Usage

```terraform
data "aws_ami" "example" {
  executable_users = ["self"]
  most_recent      = true
  name_regex       = "^example\\d{1}"
  owners           = ["self"]

  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }
}
```

## Argument Reference

### Required arguments

* `owners` - List of image owners to limit search. At least 1 value must be specified.
  * Required: Yes
  * Valid values: The CROC Cloud project ID or `self`

### Optional arguments

* `executable_users` - Limit search to project with *explicit* launch permission on the image.
  * Required: No
  * Valid values: The CROC Cloud project ID or `self`
* `filter` - One or more name/value pairs to filter.
  For more information about filtering, see the [EC2 API documentation][describe-images].
  * Required: No
* `most_recent` - If more than one result is returned, use the most recent image.
  * Required: No

* `name_regex` - A regex string to apply to the image list returned by CROC Cloud.
  This allows more advanced filtering not supported by the CROC Cloud API.
  This filtering is done locally on what CROC Cloud returns, and could have a performance impact if the result is large.
  It is recommended to combine this with other options to narrow down the list CROC Cloud returns.
  * Required: No

~> **NOTE** If the search returns zero or multiple matches, Terraform will fail.
Ensure that your search is specific enough to return
a single image ID only, or use *most_recent* to choose the most recent one.
If you want to match multiple images, use the [`aws_ami_ids`][tf-ami-ids] data source instead.

## Attribute Reference

### Supported Attributes

~> **NOTE:** Some values are not always set and may not be available for
interpolation.

The following attributes are exported:

* `architecture` - The OS architecture of the image.
  * Valid values: `i386` | `x86_64`
* `arn` - The ARN of the image.
* `block_device_mappings` - Set of objects with block device mappings of the image.
  The structure of this block is [described below](#block_device_mappings).
* `creation_date` - The date and time the image was created.
* `description` - The description of the image that was provided during image
  creation.
* `id` - The ID of the found image.
* `image_id` - The ID of the image.
  * Constraints: Should be the same as the resource `id`
* `image_location` - The location of the image.
* `image_owner_alias` -  The alias of the image owner name.
* `image_type` - The type of image.
* `name` - The name of the image that was provided during image creation.
* `owner_id` - The CROC Cloud project ID.
* `platform` - The value is `Windows` for Windows images; otherwise blank.
* `public` - `true` if the image has public launch permissions.
* `root_device_name` - The device name of the root device.
* `root_device_type` - The type of root device.
  * Valid values: `ebs` | `instance-store`
* `root_snapshot_id` - The snapshot id associated with the root device, if any.
  * Constraints: Only applies to `ebs` root devices
* `state` - The current state of the image. If the state is `available`, the image
  is successfully registered and can be used to launch an instance.
* `tags` - Any tags assigned to the image.
* `virtualization_type` - The type of virtualization of the image.
  * Valid values: `hvm` | `hvm-legacy`

<a id="block_device_mappings"></a>
The `block_device_mappings` block has the following structure:

* `device_name` - The physical name of the device.
* `ebs` - Map containing EBS information, if the device is EBS-based.
  The structure of this subblock is [described below](#ebs).

  ~> **NOTE** Unlike most object attributes, these are accessed directly (e.g., `ebs.volume_size` or `ebs["volume_size"]`) rather than accessed through the first element of a list (e.g., `ebs[0].volume_size`).
* `no_device` - Suppresses the specified device included in the block device mapping of the image.
* `virtual_name` - The virtual device name (for instance stores).

<a id="ebs"></a>
The `ebs` subblock of the [`block_device_mappings` block](#block_device_mappings) has the following structure:
* `delete_on_termination` - `true` if the EBS volume will be deleted on termination.
* `iops` - `0` if the EBS volume is not a provisioned IOPS image, otherwise the supported IOPS count.
* `snapshot_id` - The ID of the snapshot.
* `throughput` - The throughput that the volume supports, in MiB/s.
* `volume_size` - The size of the volume, in GiB.
* `volume_type` - The volume type.

### Unsupported attributes

<span style="color:MediumVioletRed">These exported attributes are currently unsupported by CROC Cloud:</span>

* `boot_mode` - The boot mode of the image.
  * Fixed value: `""`
* `deprecation_time` - The date and time to deprecate the image.
  * Fixed value: `""`
* `ena_support` - Specifies whether enhanced networking with ENA is enabled.
  * Fixed value: `false`
* `ebs_block_device` - Some elements of the block are unsupported. See [description below](#ebs_block_device_unsupported).
* `hypervisor` - The hypervisor type of the image.
  * Fixed value: `""`
* `image_location` - Path to an S3 object containing an image manifest.
  * Fixed value: `""`
* `kernel_id` - The id of the kernel image (AKI) that is used as the paravirtual kernel in created instances.
  * Fixed value: `""`
* `platform_details` - The platform details associated with the billing code of the image.
  * Fixed value: `""`
* `product_codes` - Any product codes associated with the image.
  * `product_codes.#.product_code_id` - The product code.
  * `product_codes.#.product_code_type` - The type of product code.
* `ramdisk_id` - The id of an initrd image (ARI) that is used when booting the created instances.
  * Fixed value: `""`
* `sriov_net_support` - When set to `simple`, enables enhanced networking for created instances.
  * Fixed value: `""`
* `state_reason` - Describes a state change. Fields are `UNSET` if not available.
    * `state_reason.code` - The reason code for the state change.
    * `state_reason.message` - The message for the state change.
* `usage_operation` - The operation of the Amazon EC2 instance and the billing code that is associated with the image.
  * Fixed value: `""`

<a id="ebs_block_device_unsupported"></a>
The `ebs_block_device` block has the following unsupported elements:

* `ebs` - This subblock has unsupported elements. See [description below](#ebs_unsupported).

<a id="ebs_unsupported"></a>
The `ebs` subblock of the [`ebs_block_device` block](#ebs_block_device_unsupported) has the following unsupported elements:

  * `encrypted` - Whether the disk is encrypted.
      * Fixed value: `false`
  * `kms_key_id` - The ARN for the KMS encryption key.
      * Fixed value: `""`
  * `outpost_arn` - The ARN of the Outpost.
      * Fixed value: `""`
