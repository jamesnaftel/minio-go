# MinIO Go Client SDK for Amazon S3 Compatible Cloud Storage [![Slack](https://slack.min.io/slack?type=svg)](https://slack.min.io) [![Sourcegraph](https://sourcegraph.com/github.com/minio/minio-go/-/badge.svg)](https://sourcegraph.com/github.com/minio/minio-go?badge) [![Apache V2 License](http://img.shields.io/badge/license-Apache%20V2-blue.svg)](https://github.com/minio/minio-go/blob/master/LICENSE)

The MinIO Go Client SDK provides simple APIs to access any Amazon S3 compatible object storage.

This quickstart guide will show you how to install the MinIO client SDK, connect to MinIO, and provide a walkthrough for a simple file uploader. For a complete list of APIs and examples, please take a look at the [Go Client API Reference](https://docs.min.io/docs/golang-client-api-reference).

This document assumes that you have a working [Go development environment](https://docs.min.io/docs/how-to-install-golang).

## Download from Github
```sh
go get -u github.com/minio/minio-go
```

## Initialize MinIO Client
MinIO client requires the following four parameters specified to connect to an Amazon S3 compatible object storage.

| Parameter  | Description|
| :---         |     :---     |
| endpoint   | URL to object storage service.   |
| accessKeyID | Access key is the user ID that uniquely identifies your account. |   
| secretAccessKey | Secret key is the password to your account. |
| secure | Set this value to 'true' to enable secure (HTTPS) access. |


```go
package main

import (
	"github.com/minio/minio-go/v6"
	"log"
)

func main() {
	endpoint := "play.min.io:9000"
	accessKeyID := "Q3AM3UQ867SPQQA43P2F"
	secretAccessKey := "zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG"
	useSSL := true

	// Initialize minio client object.
	minioClient, err := minio.New(endpoint, accessKeyID, secretAccessKey, useSSL)
	if err != nil {
		log.Fatalln(err)
	}

	log.Printf("%#v\n", minioClient) // minioClient is now setup
}
```

## Quick Start Example - File Uploader
This example program connects to an object storage server, creates a bucket and uploads a file to the bucket.

We will use the MinIO server running at [https://play.min.io:9000](https://play.min.io:9000) in this example. Feel free to use this service for testing and development. Access credentials shown in this example are open to the public.

### FileUploader.go
```go
package main

import (
	"github.com/minio/minio-go/v6"
	"log"
)

func main() {
	endpoint := "play.min.io:9000"
	accessKeyID := "Q3AM3UQ867SPQQA43P2F"
	secretAccessKey := "zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG"
	useSSL := true

	// Initialize minio client object.
	minioClient, err := minio.New(endpoint, accessKeyID, secretAccessKey, useSSL)
	if err != nil {
		log.Fatalln(err)
	}

	// Make a new bucket called mymusic.
	bucketName := "mymusic"
	location := "us-east-1"

	err = minioClient.MakeBucket(bucketName, location)
	if err != nil {
		// Check to see if we already own this bucket (which happens if you run this twice)
		exists, errBucketExists := minioClient.BucketExists(bucketName)
		if errBucketExists == nil && exists {
			log.Printf("We already own %s\n", bucketName)
		} else {
			log.Fatalln(err)
		}
	} else {
		log.Printf("Successfully created %s\n", bucketName)
	}

	// Upload the zip file
	objectName := "golden-oldies.zip"
	filePath := "/tmp/golden-oldies.zip"
	contentType := "application/zip"

	// Upload the zip file with FPutObject
	n, err := minioClient.FPutObject(bucketName, objectName, filePath, minio.PutObjectOptions{ContentType:contentType})
	if err != nil {
		log.Fatalln(err)
	}

	log.Printf("Successfully uploaded %s of size %d\n", objectName, n)
}
```

### Run FileUploader
```sh
go run file-uploader.go
2016/08/13 17:03:28 Successfully created mymusic
2016/08/13 17:03:40 Successfully uploaded golden-oldies.zip of size 16253413

mc ls play/mymusic/
[2016-05-27 16:02:16 PDT]  17MiB golden-oldies.zip
```

## API Reference
The full API Reference is available here.

* [Complete API Reference](https://docs.min.io/docs/golang-client-api-reference)

### API Reference : Bucket Operations
* [`MakeBucket`](https://docs.min.io/docs/golang-client-api-reference#MakeBucket)
* [`ListBuckets`](https://docs.min.io/docs/golang-client-api-reference#ListBuckets)
* [`BucketExists`](https://docs.min.io/docs/golang-client-api-reference#BucketExists)
* [`RemoveBucket`](https://docs.min.io/docs/golang-client-api-reference#RemoveBucket)
* [`ListObjects`](https://docs.min.io/docs/golang-client-api-reference#ListObjects)
* [`ListObjectsV2`](https://docs.min.io/docs/golang-client-api-reference#ListObjectsV2)
* [`ListIncompleteUploads`](https://docs.min.io/docs/golang-client-api-reference#ListIncompleteUploads)

### API Reference : Bucket policy Operations
* [`SetBucketPolicy`](https://docs.min.io/docs/golang-client-api-reference#SetBucketPolicy)
* [`GetBucketPolicy`](https://docs.min.io/docs/golang-client-api-reference#GetBucketPolicy)

### API Reference : Bucket notification Operations
* [`SetBucketNotification`](https://docs.min.io/docs/golang-client-api-reference#SetBucketNotification)
* [`GetBucketNotification`](https://docs.min.io/docs/golang-client-api-reference#GetBucketNotification)
* [`RemoveAllBucketNotification`](https://docs.min.io/docs/golang-client-api-reference#RemoveAllBucketNotification)
* [`ListenBucketNotification`](https://docs.min.io/docs/golang-client-api-reference#ListenBucketNotification) (MinIO Extension)

### API Reference : File Object Operations
* [`FPutObject`](https://docs.min.io/docs/golang-client-api-reference#FPutObject)
* [`FGetObject`](https://docs.min.io/docs/golang-client-api-reference#FGetObject)
* [`FPutObjectWithContext`](https://docs.min.io/docs/golang-client-api-reference#FPutObjectWithContext)
* [`FGetObjectWithContext`](https://docs.min.io/docs/golang-client-api-reference#FGetObjectWithContext)

### API Reference : Object Operations
* [`GetObject`](https://docs.min.io/docs/golang-client-api-reference#GetObject)
* [`PutObject`](https://docs.min.io/docs/golang-client-api-reference#PutObject)
* [`GetObjectWithContext`](https://docs.min.io/docs/golang-client-api-reference#GetObjectWithContext)
* [`PutObjectWithContext`](https://docs.min.io/docs/golang-client-api-reference#PutObjectWithContext)
* [`PutObjectStreaming`](https://docs.min.io/docs/golang-client-api-reference#PutObjectStreaming)
* [`StatObject`](https://docs.min.io/docs/golang-client-api-reference#StatObject)
* [`CopyObject`](https://docs.min.io/docs/golang-client-api-reference#CopyObject)
* [`RemoveObject`](https://docs.min.io/docs/golang-client-api-reference#RemoveObject)
* [`RemoveObjects`](https://docs.min.io/docs/golang-client-api-reference#RemoveObjects)
* [`RemoveIncompleteUpload`](https://docs.min.io/docs/golang-client-api-reference#RemoveIncompleteUpload)
* [`SelectObjectContent`](https://docs.min.io/docs/golang-client-api-reference#SelectObjectContent)


### API Reference : Presigned Operations
* [`PresignedGetObject`](https://docs.min.io/docs/golang-client-api-reference#PresignedGetObject)
* [`PresignedPutObject`](https://docs.min.io/docs/golang-client-api-reference#PresignedPutObject)
* [`PresignedHeadObject`](https://docs.min.io/docs/golang-client-api-reference#PresignedHeadObject)
* [`PresignedPostPolicy`](https://docs.min.io/docs/golang-client-api-reference#PresignedPostPolicy)

### API Reference : Client custom settings
* [`SetAppInfo`](http://docs.min.io/docs/golang-client-api-reference#SetAppInfo)
* [`SetCustomTransport`](http://docs.min.io/docs/golang-client-api-reference#SetCustomTransport)
* [`TraceOn`](http://docs.min.io/docs/golang-client-api-reference#TraceOn)
* [`TraceOff`](http://docs.min.io/docs/golang-client-api-reference#TraceOff)

## Full Examples

### Full Examples : Bucket Operations
* [makebucket.go](https://github.com/minio/minio-go/blob/master/examples/s3/makebucket.go)
* [listbuckets.go](https://github.com/minio/minio-go/blob/master/examples/s3/listbuckets.go)
* [bucketexists.go](https://github.com/minio/minio-go/blob/master/examples/s3/bucketexists.go)
* [removebucket.go](https://github.com/minio/minio-go/blob/master/examples/s3/removebucket.go)
* [listobjects.go](https://github.com/minio/minio-go/blob/master/examples/s3/listobjects.go)
* [listobjectsV2.go](https://github.com/minio/minio-go/blob/master/examples/s3/listobjectsV2.go)
* [listincompleteuploads.go](https://github.com/minio/minio-go/blob/master/examples/s3/listincompleteuploads.go)

### Full Examples : Bucket policy Operations
* [setbucketpolicy.go](https://github.com/minio/minio-go/blob/master/examples/s3/setbucketpolicy.go)
* [getbucketpolicy.go](https://github.com/minio/minio-go/blob/master/examples/s3/getbucketpolicy.go)
* [listbucketpolicies.go](https://github.com/minio/minio-go/blob/master/examples/s3/listbucketpolicies.go)

### Full Examples : Bucket lifecycle Operations
* [setbucketlifecycle.go](https://github.com/minio/minio-go/blob/master/examples/s3/setbucketlifecycle.go)
* [getbucketlifecycle.go](https://github.com/minio/minio-go/blob/master/examples/s3/getbucketlifecycle.go)

### Full Examples : Bucket notification Operations
* [setbucketnotification.go](https://github.com/minio/minio-go/blob/master/examples/s3/setbucketnotification.go)
* [getbucketnotification.go](https://github.com/minio/minio-go/blob/master/examples/s3/getbucketnotification.go)
* [removeallbucketnotification.go](https://github.com/minio/minio-go/blob/master/examples/s3/removeallbucketnotification.go)
* [listenbucketnotification.go](https://github.com/minio/minio-go/blob/master/examples/minio/listenbucketnotification.go) (MinIO Extension)

### Full Examples : File Object Operations
* [fputobject.go](https://github.com/minio/minio-go/blob/master/examples/s3/fputobject.go)
* [fgetobject.go](https://github.com/minio/minio-go/blob/master/examples/s3/fgetobject.go)
* [fputobject-context.go](https://github.com/minio/minio-go/blob/master/examples/s3/fputobject-context.go)
* [fgetobject-context.go](https://github.com/minio/minio-go/blob/master/examples/s3/fgetobject-context.go)

### Full Examples : Object Operations
* [putobject.go](https://github.com/minio/minio-go/blob/master/examples/s3/putobject.go)
* [getobject.go](https://github.com/minio/minio-go/blob/master/examples/s3/getobject.go)
* [putobject-context.go](https://github.com/minio/minio-go/blob/master/examples/s3/putobject-context.go)
* [getobject-context.go](https://github.com/minio/minio-go/blob/master/examples/s3/getobject-context.go)
* [statobject.go](https://github.com/minio/minio-go/blob/master/examples/s3/statobject.go)
* [copyobject.go](https://github.com/minio/minio-go/blob/master/examples/s3/copyobject.go)
* [removeobject.go](https://github.com/minio/minio-go/blob/master/examples/s3/removeobject.go)
* [removeincompleteupload.go](https://github.com/minio/minio-go/blob/master/examples/s3/removeincompleteupload.go)
* [removeobjects.go](https://github.com/minio/minio-go/blob/master/examples/s3/removeobjects.go)

### Full Examples : Encrypted Object Operations
* [put-encrypted-object.go](https://github.com/minio/minio-go/blob/master/examples/s3/put-encrypted-object.go)
* [get-encrypted-object.go](https://github.com/minio/minio-go/blob/master/examples/s3/get-encrypted-object.go)
* [fput-encrypted-object.go](https://github.com/minio/minio-go/blob/master/examples/s3/fputencrypted-object.go)

### Full Examples : Presigned Operations
* [presignedgetobject.go](https://github.com/minio/minio-go/blob/master/examples/s3/presignedgetobject.go)
* [presignedputobject.go](https://github.com/minio/minio-go/blob/master/examples/s3/presignedputobject.go)
* [presignedheadobject.go](https://github.com/minio/minio-go/blob/master/examples/s3/presignedheadobject.go)
* [presignedpostpolicy.go](https://github.com/minio/minio-go/blob/master/examples/s3/presignedpostpolicy.go)

## Explore Further
* [Complete Documentation](https://docs.min.io)
* [MinIO Go Client SDK API Reference](https://docs.min.io/docs/golang-client-api-reference)

## Contribute
[Contributors Guide](https://github.com/minio/minio-go/blob/master/CONTRIBUTING.md)

[![Build Status](https://travis-ci.org/minio/minio-go.svg)](https://travis-ci.org/minio/minio-go)
[![Build status](https://ci.appveyor.com/api/projects/status/1d05e6nvxcelmrak?svg=true)](https://ci.appveyor.com/project/harshavardhana/minio-go)

## License
This SDK is distributed under the [Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0), see [LICENSE](./LICENSE) and [NOTICE](./NOTICE) for more information.
