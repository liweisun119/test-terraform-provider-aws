---
subcategory: "Lambda"
layout: "aws"
page_title: "AWS: aws_lambda_layer_version"
description: |-
  Provides a Lambda Layer Version resource. Lambda Layers allow you to reuse shared bits of code across multiple lambda functions.
---

# Resource: aws_lambda_layer_version

Provides a Lambda Layer Version resource. Lambda Layers allow you to reuse shared bits of code across multiple lambda functions.

For information about Lambda Layers and how to use them, see [AWS Lambda Layers][1]

## Example Usage

```terraform
resource "aws_lambda_layer_version" "lambda_layer" {
  filename   = "lambda_layer_payload.zip"
  layer_name = "lambda_layer_name"

  compatible_runtimes = ["nodejs12.x"]
}
```

## Specifying the Deployment Package

AWS Lambda Layers expect source code to be provided as a deployment package whose structure varies depending on which `compatible_runtimes` this layer specifies.
See [Runtimes][2] for the valid values of `compatible_runtimes`.

Once you have created your deployment package you can specify it either directly as a local file (using the `filename` argument) or
indirectly via Amazon S3 (using the `s3_bucket`, `s3_key` and `s3_object_version` arguments). When providing the deployment
package via S3 it may be useful to use [the `aws_s3_bucket_object` resource](s3_bucket_object.html) to upload it.

For larger deployment packages it is recommended by Amazon to upload via S3, since the S3 API has better support for uploading
large files efficiently.

## Argument Reference

* `layer_name` (Required) A unique name for your Lambda Layer
* `filename` (Optional) The path to the function's deployment package within the local filesystem. If defined, The `s3_`-prefixed options cannot be used.
* `s3_bucket` - (Optional) The S3 bucket location containing the function's deployment package. Conflicts with `filename`. This bucket must reside in the same AWS region where you are creating the Lambda function.
* `s3_key` - (Optional) The S3 key of an object containing the function's deployment package. Conflicts with `filename`.
* `s3_object_version` - (Optional) The object version containing the function's deployment package. Conflicts with `filename`.
* `compatible_runtimes` - (Optional) A list of [Runtimes][2] this layer is compatible with. Up to 5 runtimes can be specified.
* `compatible_architectures` - (Optional) A list of [Architectures][4] this layer is compatible with. Currently `x86_64` and `arm64` can be specified.
* `description` - (Optional) Description of what your Lambda Layer does.
* `license_info` - (Optional) License info for your Lambda Layer. See [License Info][3].
* `source_code_hash` - (Optional) Used to trigger updates. Must be set to a base64-encoded SHA256 hash of the package file specified with either `filename` or `s3_key`. The usual way to set this is `${filebase64sha256("file.zip")}` (Terraform 0.11.12 or later) or `${base64sha256(file("file.zip"))}` (Terraform 0.11.11 and earlier), where "file.zip" is the local filename of the lambda layer source archive.

## Attributes Reference

In addition to all arguments above, the following attributes are exported:

* `arn` - The Amazon Resource Name (ARN) of the Lambda Layer with version.
* `layer_arn` - The Amazon Resource Name (ARN) of the Lambda Layer without version.
* `created_date` - The date this resource was created.
* `signing_job_arn` - The Amazon Resource Name (ARN) of a signing job.
* `signing_profile_version_arn` - The Amazon Resource Name (ARN) for a signing profile version.
* `source_code_size` - The size in bytes of the function .zip file.
* `version` - This Lambda Layer version.

[1]: https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html
[2]: https://docs.aws.amazon.com/lambda/latest/dg/API_PublishLayerVersion.html#SSS-PublishLayerVersion-request-CompatibleRuntimes
[3]: https://docs.aws.amazon.com/lambda/latest/dg/API_PublishLayerVersion.html#SSS-PublishLayerVersion-request-LicenseInfo
[4]: https://docs.aws.amazon.com/lambda/latest/dg/API_PublishLayerVersion.html#SSS-PublishLayerVersion-request-CompatibleArchitectures

## Import

Lambda Layers can be imported using `arn`.

```
$ terraform import \
    aws_lambda_layer_version.test_layer \
    arn:aws:lambda:_REGION_:_ACCOUNT_ID_:layer:_LAYER_NAME_:_LAYER_VERSION_
```
