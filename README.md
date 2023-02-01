# Deploying a React App to AWS S3 and CloudFront

## Get Security Credentials

`<username> >  Security credentials > Access keys > Create access key`

Create and Access key and make note of the key and secret

Install and configure AWS CLI with `aws configure`

```
AWS Access Key ID [None]: <aws-access-key>
AWS Secret Access Key [None]: <aws-access-key-secret>
Default region name [None]: <your-preferred-region>
Default output format [None]: json
```

## Create an S3 Bucket

Under `Block Public Access settings for this bucket`, leave `Block all public access` checked.

Upload assets to S3

```
aws s3 sync dist/ s3://<bucket-name-here>
```

## Create a CloudFront Distribution

In `Settings` set the `Default root object` to `/index.html`

Under `Origins > Create origin`, set the `Origin domain` to the previously created S3 bucket.

The Name will be automatically populated.

Set `Origin access` to `Origin access control settings`, and `Create control setting`

Copy the generated Bucket policy, go to the Bucket Permissions page and paste in the policy.

Set `Default root object` to `index.html`

Static site will be served at the CloudFront `Distribution domain name`

Under `Error pages > Create custom error response`, add redirects for 400, 403, 404 to `/index.html` returning `200`

## Notes

Invalidate CloudFront cache

```
"aws cloudfront create-invalidation --distribution-id <your-distribution-id> --paths '/*'
```

## Custom Domain

### AWS Certificate Manager

Request a certificate

Enter your domain name as `*.your-domain.tld`

Copy the `CNAME name` and `CNAME value`

Create a CNAME entry in your domain manager, and copy the above values into `Hostname` and `Target name`

### CloudFront Distribution Settings

Add Alternate domain name (CNAME)

For each distribution, create a CNAME entry in your domain manager with the subdomain as the `Hostname` and the CloudFront domain as the `Target name`

CloudFront does not work with root domains. For a root domain, a subdomain of `www` must be used, and the root domain must be forwarded to `www.your-domain.tld` from your domain manager.