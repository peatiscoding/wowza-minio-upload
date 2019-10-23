# Minio (s3) Upload

## General Information

This project is the forked version from original Wowza's plugin [wse-plugin-s3upload](https://github.com/WowzaMediaSystems/wse-plugin-s3upload). The aim is to use this plugin to uplaod to Minio Server (s3 compatible) instead of AWS's S3 Servers.

The current implementation of original plugin is not working with the custom endpoint such as Minio's URL, eg. http://localhost:9000, the endpoint is actually built from the bucket's regions. So I forked the source code and create my own.

The main changes to original source code is just how we fixed the endpoint problem. As Minio implementation example. [Link](https://docs.min.io/docs/how-to-use-aws-sdk-for-java-with-minio-server.html). And I've also made some adjustment to the required parameters;

## Parameters

Parameter Name|Required?|Type
--|--|--
`minioUploadEndpoint`|Yes|minio server url
`minioUploadAccessKey`|Yes|Access Key of minio
`minioUploadSecretKey`|Yes|Secret Key of minio
`minioUploadBucketName`|Yes|Target bucket name
`minioUploadFilePrefix`|No|File's prefix - replacing `s3UploadFilePrefix`
`minioUploadDebugLog`|No|Write debug events? default = `false`

Other parameters are still prefixed as S3. Please see and there for you can use whatever you have configured for S3 plugin as for this one.

Normally **minio server url** should be the same value as you would use it with S3 command line like so:

```
# given that
more ~/.aws/credentials
[minio]
aws_access_key_id = ...<access_key_to_minio>
aws_secret_access_key = ...<secret_key_to_minio>

# then you should be able to:
aws s3 rm --endpoint-url http://localhost:9000 --profile minio s3://test/test.png
```

The **ModuleMinioUpload** module for [Wowza Streaming Engine™ media server software](https://www.wowza.com/products/streaming-engine) automatically uploads finished recordings to an Minio bucket (Compatible to AWS S3). It uses the Amazon Web Services (AWS) SDK for Java to upload the recorded files.

## Prerequisites
Wowza Streaming Engine 4.7.2.02 or later is recommended. For earlier versions see note below regarding AWS SDK version.

[AWS SDK for Java](https://aws.amazon.com/sdk-for-java/)

**Note:** For earlier versions of Wowza Streaming Engine™, AWS SDK version 1.10.77 or earlier is required. As a minimum, the following packages are required.

-[AWS Java SDK For Amazon S3](http://mvnrepository.com/artifact/com.amazonaws/aws-java-sdk-s3/1.10.77)

-[AWS SDK For Java Core](http://mvnrepository.com/artifact/com.amazonaws/aws-java-sdk-core/1.10.77)

-[AWS Java SDK For AWS KMS](http://mvnrepository.com/artifact/com.amazonaws/aws-java-sdk-kms/1.10.77) (it's not clear if this package is actually required. It's only referenced from AmazonS3EncryptionClient which isn't used in the S3 uploader)

The version of [Apache httpclient](http://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) that ships with Wowza Streaming Engine prior to 4.7.2.02 isn't compatible with the later versions of the AWS SDK

## Usage

When a recording is finished, a temporary file named **[recording-name].upload** is created to track the recording and sort any data that may be needed to resume the file upload later if it's interrupted. AWS TransferManager uploads the recorded file, splitting it into a multipart upload if required. After the recorded file is uploaded, the temporary **[recording-name].upload** file is deleted.

When the Wowza Streaming Engine application starts or restarts, the module checks to see if any interrupted uploads must be completed. Interrupted single part uploads are restarted from the beginning while interrupted multipart uploads are resumed from the last complete part. If the module is set to not resume uploads after interruptions (**s3UploadResumeUploads** = **false**), incomplete multipart uploads are deleted from the S3 bucket.

## Installation

1. Compile the lib.
1. Uplaod the JAR file to your wowza server.
1. Also uplaod the JAR file within [https://sdk-for-java.amazonwebservices.com/latest/aws-java-sdk.zip] as you would have done in s3-plugin.
1. Place these 2 JAR files to `[WOWZA-INSTALL-DIR]/lib` normally: `/usr/local/WowzaStreamingEngine/lib`
1. Restart **Wowza Server** - so that your JAR is loaded.
1. Go to Applications, select your app, select Module Tab, add new module with fully qualitfied name = `me.peatiscoding.wms.plugin.minio.ModuleMinioUpload`
1. Select Properties Tab, configure the module by add custom properties as per minimal example below.

Parameter Name|Type|Example value
--|--|--
`minioUploadEndpoint`|String|`http://localhost:9000`
`minioUploadAccessKey`|String|`here is my key`
`minioUploadSecretKey`|String|`here is my secret`
`minioUploadBucketName`|String|test

## Useful Links

* [Minio Implementation using AWS SDK (Java)](https://docs.min.io/docs/how-to-use-aws-sdk-for-java-with-minio-server.html)
* [Wowza Publishing URL Tool (WebRTC)](https://www.wowza.com/_private/webrtc/4.7.7/publish/)
* [Original Project](https://www.wowza.com/docs/how-to-upload-recorded-media-to-an-amazon-s3-bucket-modules3upload)
