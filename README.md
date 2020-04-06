# Serverless S3 Sync [![npm](https://img.shields.io/npm/v/serverless-s3-sync.svg)](https://www.npmjs.com/package/serverless-s3-sync)

> A plugin to sync local directories and S3 prefixes for Serverless Framework :zap: .

## Use Case

- Static Website ( `serverless-s3-sync` ) & Contact form backend ( `serverless` ) .
- SPA ( `serverless` ) & assets ( `serverless-s3-sync` ) .

## Install

Run `npm install` in your Serverless project.

```sh
$ npm install --save serverless-s3-sync
```

Add the plugin to your serverless.yml file

```yaml
plugins:
  - serverless-s3-sync
```

## Setup

```yaml
custom:
  s3Sync:
    - bucketName: my-static-site-assets # required
      bucketPrefix: assets/ # optional
      localDir: dist/assets # required
    - bucketName: my-artifact-bucket # required
      bucketPrefix: scripts/ # optional
      localDir: scripts # required
      deleteRemoved: false   # optional, default true, whether to remove s3 objects that have no corresponding local file.
      ServerSideEncryption: "AES256" # optional, default inherits bucket encryption. The server-side encryption algorithm used when storing this object AES256 or aws:kms
    - bucketName: my-other-site
      localDir: path/to/other-site
      acl: public-read # optional
      followSymlinks: true # optional
      defaultContentType: text/html # optional
      params: # optional
        - index.html:
            CacheControl: 'no-cache'
        - "*.js":
            CacheControl: 'public, max-age=31536000'
    - bucketNameKey: AnotherBucketNameOutputKey
      localDir: path/to/another

resources:
  Resources:
    AssetsBucket:
      Type: AWS::S3::Bucket
      Properties:
        BucketName: my-static-site-assets
    OtherSiteBucket:
      Type: AWS::S3::Bucket
      Properties:
        BucketName: my-other-site
        AccessControl: PublicRead
        WebsiteConfiguration:
          IndexDocument: index.html
          ErrorDocument: error.html
    AnotherBucket:
      Type: AWS::S3::Bucket
  Outputs:
    AnotherBucketNameOutputKey:
      Value: !Ref AnotherBucket
```
| Parameter Name | Default Value | Description |
| --- | --- | --- |
| bucketName |_(Required)_ | An existing AWS s3 bucket. |
| bucketPrefix | `(none)` | The base path that will prepend all API endpoints. |
| localDir | Value of `--stage`, or `provider.stage` (serverless will default to `dev` if unset) | The stage to create the domain name for. This parameter allows you to specify a different stage for the domain name than the stage specified for the serverless deployment. |
| params | Closest match | The name of a specific certificate from Certificate Manager to use with this API. If not specified, the closest match will be used (i.e. for a given domain name `api.example.com`, a certificate for `api.example.com` will take precedence over a `*.example.com` certificate). <br><br> Note: Edge-optimized endpoints require that the certificate be located in `us-east-1` to be used with the CloudFront distribution. |
| deleteRemoved | `(none)` | The arn of a specific certificate from Certificate Manager to use with this API. |
| ServerSideEncryption | `true` | Toggles whether or not the plugin will create an A Alias and AAAA Alias records in Route53 mapping the `domainName` to the generated distribution domain name. If false, does not create a record. |
| acl | edge | Defines the endpoint type, accepts `regional` or `edge`. |
| followSymlinks | | If hostedZoneId is set the route53 record set will be created in the matching zone, otherwise the hosted zone will be figured out from the domainName (hosted zone with matching domain). |
| defaultContentType | | If hostedZonePrivate is set to `true` then only private hosted zones will be used for route 53 records. If it is set to `false` then only public hosted zones will be used for route53 records. Setting this parameter is specially useful if you have multiple hosted zones with the same domain name (e.g. a public and a private one) |

## Usage

Run `sls deploy`, local directories and S3 prefixes are synced.

Run `sls remove`, S3 objects in S3 prefixes are removed.

Run `sls deploy --nos3sync`, deploy your serverless stack without syncing local directories and S3 prefixes.

### `sls s3sync`

Sync local directories and S3 prefixes.
