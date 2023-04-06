---
subcategory: "EC2 (Elastic Compute Cloud)"
layout: "aws"
page_title: "CROC Cloud: aws_ami"
description: |-
  Creates and manages a custom Amazon Machine Image (AMI).
---

[default-tags]: https://www.terraform.io/docs/providers/aws/index.html#default_tags-configuration-block
[images]: https://docs.cloud.croc.ru/en/services/instances_and_volumes/images.html
[tf-ami-launch-permission]: ami_launch_permission.html

# Resource: aws_ami

The AMI (Amazon Machine Image) resource allows you to create and manage images.

If you just want to share an existing image with another CROC Cloud account,
it's better to use [`aws_ami_launch_permission`][tf-ami-launch-permission] instead.

For details about images, see the [user documentation][images].

## Example Usage

```terraform
# Create an image that will start a machine whose root device is backed by
# an EBS volume populated from a snapshot. It is assumed that such a snapshot
# already exists with the id "snap-12345678".
resource "aws_ami" "example" {
  name                = "terraform-example"
  virtualization_type = "hvm"
  root_device_name    = "disk1"

  ebs_block_device {
    device_name = "disk1"
    snapshot_id = "snap-12345678"
    volume_size = 8
  }
}
```

## Argument Reference

### Required Arguments

* `name` - A unique name for the image.
  * Required: Yes

### Optional Arguments

* `architecture` - Machine architecture for created instances.
  * Required: No
  * Default value: `x86_64`.
* `ebs_block_device` - An EBS block device that should be
  attached to created instances. The structure of this block is [described below](#ebs_block_device).
  * Required: No
* `ephemeral_block_device` - An ephemeral block device that
  should be attached to created instances. The structure of this block is [described below](#ephemeral_block_device).
  * Required: No
* `description` - A longer, human-readable description for the image.
  * Required: No
* `root_device_name` - The name of the root device.
  * Required: No
  * Valid values: `disk<N>` | `cdrom<N>` | `floppy<N>` | `menu` (`N` - disk number)
  * Default value: `disk1`
* `tags` - A map of tags to assign to the resource. If configured with a provider [`default_tags` configuration block][default-tags] present, tags with matching keys will overwrite those defined at the provider-level.
  * Required: No
* `virtualization_type` - Keyword to choose what virtualization mode created instances will use.
  * Required: No
  * Valid values: `hvm`

### Block Descriptions

<a id="ebs_block_device"></a>
The `ebs_block_device` block has the following structure:

* `device_name` - The device name of one or more block device mapping entries.
  * Required: Yes
  * Valid values: `disk<N>` | `cdrom<N>` | `floppy<N>`, (`N` - disk number)
* `delete_on_termination` - Boolean controlling whether the EBS volumes created to
  support each created instance will be deleted once that instance is terminated.
  * Required: No
` *iops` - Number of I/O operations per second the created volumes will support.
  * Required: Only when `volume_type` is `io2`
` *snapshot_id` - The ID of an EBS snapshot that will be used to initialize the created
  EBS volumes.
  * Required: No
  * Constraints: If set, the `volume_size` attribute must be at least as large as the referenced snapshot
* `throughput` - The throughput that the volume supports, in MiB/s.
* `volume_size` - The size of created volumes in GiB.
  * Required: Required unless `snapshot_id` is set
  * Constraints: If `snapshot_id` is set and `volume_size` is omitted then the volume will have the same size as the selected snapshot.
* `volume_type` - The type of EBS volume to create.
  * Required: No
  * Valid values: `st2` | `gp2` | `io2`
  * Default value: `st2`

<a id="ephemeral_block_device"></a>
The `ephemeral_block_device` block has the following structure:

* `device_name` - The device name of one or more block device mapping entries.
  * Required: Yes
  * Valid values: `cdrom<N>` | `floppy<N>` (`N` - disk number)
* `virtual_name` - A name for the ephemeral device.
  * Required: Yes

### Timeouts

The `timeouts` block allows you to specify [timeouts](https://www.terraform.io/docs/configuration/blocks/resources/syntax.html#operation-timeouts) for certain actions:

* `create` - Used when creating the image.
  * Default value: `40 minutes`
* `update` - Used when updating the image.
  * Default value: `40 minutes`
* `delete` - Used when deregistering the image.
  * Default value: `40 minutes`

## Attribute Reference

### Supported Attributes

In addition to all arguments above, the following attributes are exported:

* `arn` - The ARN of the image.
* `hypervisor` - The hypervisor type of the image.
* `id` - The ID of the created image.
* `image_owner_alias` - The owner alias (for example, `self`) or the CROC Cloud project ID.
* `image_type` - The type of image.
* `owner_id` - The CROC Cloud project ID.
* `platform` - This value is set to windows for Windows images; otherwise, it is blank.
* `public` - Indicates whether the image has public launch permissions.
* `root_snapshot_id` - The Snapshot ID for the root volume (for EBS-backed images).
* `tags_all` - A map of tags assigned to the resource, including those inherited from the provider [`default_tags` configuration block][default-tags].

### Unsupported Attributes

<span style="color:MediumVioletRed">These exported attributes are currently unsupported by CROC Cloud:</span>

* `boot_mode` - The boot mode of the image.
  * Fixed value: `""`
* `deprecation_time` - The date and time to deprecate the image.
  * Fixed value: `""`
* `ebs_block_device` - Some elements of the block are unsupported. See [details below](#ebs_block_device_unsupported).
* `ena_support` - Specifies whether enhanced networking with ENA is enabled.
  * Fixed value: `false`
* `image_location` - Path to an S3 object containing an image manifest.
  * Fixed value: `""`
* `kernel_id` - The ID of the kernel image (AKI) that is used as the paravirtual kernel in created instances.
  * Fixed value: `""`
* `platform_details` - The platform details associated with the billing code of the image.
  * Fixed value: `""`
* `ramdisk_id` - The id of an initrd image (ARI) that is used when booting the created instances.
  * Fixed value: `""`
* `sriov_net_support` - When set to `simple`, enables enhanced networking for created instances.
  * Fixed value: `""`
* `usage_operation` - The operation of the Amazon EC2 instance and the billing code that is associated with the image.
  * Fixed value: `""`

<a id="ebs_block_device_unsupported"></a>
The `ebs_block_device` block has the following unsupported elements:

* `encrypted` - Whether the disk is encrypted.
  * Fixed value: `false`
* `kms_key_id` - The ARN for the KMS encryption key.
  * Fixed value: `""`
* `outpost_arn` - The ARN of the Outpost.
  * Fixed value: `""`

## Import

`aws_ami` can be imported using the ID of the image, e.g.,

```
$ terraform import aws_ami.example cmi-12345678
```
