# EC2 SSL termination for a Beanstalk single instance environment
Letsencrypt integration for Elastic Beanstalk (AL2, nginx webserver, without proxy) Tested on the platform: PHP 7.4 running on 64bit Amazon Linux 2/3.1.2

- Avoiding the 5-time-a-week Letsencrypt cert issuing limit
- Auto cert renewal
- http to https redirect
- domain.tld to www.domain.tld redirect

## Installation:
1. Create a bucket to store a letsencrypt certificate
2. Create an ec2 instance role based on "aws-elasticbeanstalk-ec2-role" and add this inline policy (replace `<bucket-name>` with the real bucket name):

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "LeBucketAccess",
            "Action": [
                "s3:Get*",
                "s3:List*",
                "s3:PutObject"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:s3:::<bucket-name>/*"
        },
        {
            "Sid": "BucketAccess",
            "Action": [
                "s3:List*"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:s3:::*"
        }
    ]
}
```
3. Create a new Beanstalk environment, associate the created ec2 role with beanstalk instance and deploy a SAMPLE app (not your app).
4. Point DNS names to the beanstalk environment
5. Put your info in the .ebextensions/https.config and .platform/nginx.conf files
6. Make an app bundle by adding the .ebextensions and .platform folders with the files.
7. Deploy your app
