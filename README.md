# Minio (s3) Upload

## General Information

This project is the forked version from original Wowza's plugin [wse-plugin-s3upload](https://github.com/WowzaMediaSystems/wse-plugin-s3upload). The aim is to use this plugin to uplaod to Minio Server (s3 compatible) instead of AWS's S3 Servers.

The current implementation of original plugin is not working with the custom endpoint such as Minio's URL, eg. http://localhost:9000, the endpoint is actually built from the bucket's regions. So I forked the source code and create my own.

The main changes to original source code is just how we fixed the endpoint problem. As Minio implementation example. [Link](https://docs.min.io/docs/how-to-use-aws-sdk-for-java-with-minio-server.html). And I've also made some adjustment to the required parameters;

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

## Useful Links

* [Minio Implementation using AWS SDK (Java)](https://docs.min.io/docs/how-to-use-aws-sdk-for-java-with-minio-server.html)
* [Wowza Publishing URL Tool (WebRTC)](https://www.wowza.com/_private/webrtc/4.7.7/publish/)
* [Original Project](https://www.wowza.com/docs/how-to-upload-recorded-media-to-an-amazon-s3-bucket-modules3upload)
