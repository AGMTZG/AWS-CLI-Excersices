# Buckets

# Basic operations with S3 Buckets

### List all buckets

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws s3 ls
```

</p>
</details>

### Create a bucket

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3 mb s3://<your bucket name>
```

</p>
</details>

### Delete a bucket

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3 rb s3://<your bucket name>
```

</p>
</details>

### Force delete a bucket

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3 rb s3://<your bucket name> --force
```

</p>
</details>

# Operations with objects

### Create an index.html, css/index.css, js/index.js and upload index.js to a s3 bucket, you can use the next commands:

```bash
echo "Hello World" > $(pwd)/index.html
sudo mkdir css
sudo mkdir js
cd css
sudo touch index.css
cd ..
cd js
sudo touch index.js
```

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3 cp index.html s3://<your bucket name>
```

</p>
</details>


### List all objects in your s3 bucket

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3 ls s3://<your bucket name> --recursive
```

</p>
</details>

### Upload a folder including subfolders to the s3 bucket

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3 cp ./myfolder/ s3://<your bucket name>/myfolder/ --recursive
```

</p>
</details>

### Download a file from your s3 bucket

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3 cp s3://<your bucket name>/index.html ./index.html
```

</p>
</details>

### Download a folder from you s3 bucket

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3 cp s3://<your bucket name>/folder ./folder --recursive
```

</p>
</details>

### Make changes to the index.html and synchronize your local folder with the files in your s3 bucket

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3 sync ./ s3://<your bucket name>/
```

</p>
</details>

### Sync all the files from a bucket to another bucket

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3 sync s3://<your bucket source>  s3://<your bucket destination>
```

</p>
</details>

### Copy the index html from a bucket to another s3 bucket

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3 cp s3://<your bucket name source>/index.html s3://<your bucket name destination>/index.html
```

</p>
</details>

### Move the index.css to another s3 bucket

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3 mv s3://<your bucket name source>/index.css s3://<your bucket name destination>
```

</p>
</details>

### Erase files in your s3 bucket

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3 rm s3://<your bucket name>/index.html
or
aws s3 rm s3://<your bucket name> --recursive
```

</p>
</details>

# Permissions and ACL

Note: It’s actually not recommended to use ACL permissions on buckets, but it’s worth learning them anyway.

## Basic ACL permissions

- READ → Allows reading the object or listing the bucket.

- WRITE → Allows uploading or modifying objects in the bucket.

- READ_ACP → Allows reading the ACL configuration.

- WRITE_ACP → Allows modifying the ACL configuration.

- FULL_CONTROL → Allows all of the above.

## Policies

- s3:GetObject → Read permissions
- s3:PutObject → Uploading permissions
- s3:DeleteObject →  Deleting permissions
- s3:DeleteObjectVersion → Delete specific version permissions.
- s3:ListObject →  Get objects permissions
- s3:ListBucketVersions → Get versions of bucket
- s3:ListBucketMultipartUploads → Check large uploaded files 

### List the ACL permissions of your S3 bucket

---


<details>
<summary>Show commands / answers</summary>
<p>


```bash
aws s3api get-bucket-acl --bucket <your bucket name>
```

</p>
</details>

### Upload an object to your S3 bucket and make it publicly accessible

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3 cp ./index.html s3://<your bucket name> --acl public-read
or
aws s3api put-bucket-policy --bucket mi-bucket --policy '{"Version":"2012-10-17","Statement":[{"Effect":"Allow","Principal":"*","Action":["s3:GetObject"],"Resource":["arn:aws:s3:::mi-bucket/<file>"]}]}'
```

</p>
</details>

### View the acl from the recent uploaded object

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws s3api get-object-acl --bucket <your bucket name> --key <your file route>
```

</p>
</details>

### Create an S3 bucket that allows public uploading of objects using a bucket policy

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api create-bucket --bucket <your bucket name> --region us-east-1
aws s3api put-bucket-policy --bucket <your bucket name> --policy \
'{"Version":"2022-10-17","Statement":[{"Effect":"Allow","Principal":"*","Action":["s3:PutObject"],"Resource":["arn:aws:s3:::<your bucket name>/*"]}]}'
```

</p>
</details>

### Create a private bucket

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws s3api create-bucket --bucket <your bucket name> --acl private
or
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Deny",
            "Principal": "*",
            "Action": "s3:*",
            "Resource": [
                "arn:aws:s3:::mi-bucket",
                "arn:aws:s3:::mi-bucket/*"
            ],
            "Condition": {
                "StringNotEquals": {
                    "aws:PrincipalArn": "arn:aws:iam::<userid>:root"
                }
            }
        }
    ]
}
```

</p>
</details>

### Allow anyone to read existing objects and upload new objects to a bucket.

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api put-object-acl --bucket <your bucket name> --acl public-read-write
```

</p>
</details>

### Make an object ACL readable by the owner

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api put-object-acl --bucket <your bucket name> --key index.html --acl bucket-owner-read
```


</p>
</details>

### Give the bucket owner full control of the object.

---

<details>
<summary>Show commands / answers</summary>
<p>


```bash
aws s3api put-object-acl --bucket <your bucket name> --key index.html --acl bucket-owner-full-control
```

</p>
</details>

### Allow any authenticated AWS user to read and download objects from your bucket

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api put-bucket-acl --bucket <your bucket name> --acl authenticated-read
```

</p>
</details>

### Allow selected AWS services to read and execute objects in your bucket

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api put-bucket-acl --bucket <your bucket name> --acl aws-exec-read
```

</p>
</details>

# Grant permissions to an specific user (Note: The grant permissions only works with canonical users (Root users, not IAM users))

- grant-read	 →  Allows reading the object
- grant-write	 →  Allows writing (buckets only)
- grant-read-acp	→  Allows reading the ACLs
- grant-write-acp	→  Allows modifying the ACLs
- grant-full-control	→  Grants all of the above permissions

### Get the canonical user id from the buckets

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api list-buckets --query "Owner.ID" --output text
```

</p>
</details>

### Grant to another AWS account READ access to an object

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api put-object-acl --bucket <your bucket name> --key <file> --grant-read 'id=<user id>'
```

</p>
</details>

### Grant to another AWS account FULL CONTROL to an object

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api put-object-acl --bucket <your bucket name> --key <file> --grant-full-control 'id=<userid>'
```

</p>
</details>

### Grant to another aws account the ability to change ACLs to an object

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api put-object-acl --bucket <your bucket name> --key <file> --grant-write-acp 'id=<user id>'
```

</p>
</details>

### Grant upload permissions to a bucket to another user

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api put-bucket-acl --bucket <your bucket name> --grant-write 'id=<user id>'
```

</p>
</details>

### Give S3 log delivery group access to a bucket (To enable logging). (The S3 log delivery group is a special AWS group that allows S3 to automatically deliver log files to your bucket)

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api put-bucket-acl --bucket <your bucket name> --grant-write 'URI="http://acs.amazonaws.com/groups/s3/LogDelivery"'
```

</p>
</details>

### Combine multiple grants by giving certain users read and full control permissions to an object

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api put-object-acl --bucket <your bucket name> --key <file> --grant-read 'id=<user id 1>' --grant-full-control 'id=<user id 2>'
```

</p>
</details>

### Give temporary access to private objects

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3 presign s3://<your bucket name>/<file> --expires-in 3600
```

</p>
</details>

# Versioning

### Enable versioning to the s3 bucket

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api put-bucket-versioning --bucket <your bucket name> --versioning-configuration Status=Enabled
```

</p>
</details>

### Check whether versioning is enabled, suspended, or not configured for your S3 bucket

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api get-bucket-versioning --bucket mi-bucket
```

</p>
</details>

### List versions of an object in your S3 bucket

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api list-object-versions --bucket <your bucket name> --prefix index.html
```

</p>
</details>

### Restore a previous version of an object in your S3 bucket

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api copy-object --bucket <your bucket name> --copy-source <your bucket name>/<file>?versionId=<version-id> --key <file>
```

</p>
</details>

### Delete a specific version of an object in a versioned bucket, without affecting other versions.

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api delete-object --bucket <your bucket name> --key <file> --version-id <VersionID>
```

</p>
</details>

# Encryption

### Enable default bucket encryption

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api put-bucket-encryption --bucket <your bucket name> --server-side-encryption-configuration '{
  "Rules":[{"ApplyServerSideEncryptionByDefault":{"SSEAlgorithm":"AES256"}}]
}'
```

</p>
</details>

### View encryption

---


<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api get-bucket-encryption --bucket  <your bucket name>
```

</p>
</details>

# Logs, tags, multipart uploading, cross region

### Create a bucket in region us-east-1

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api create-bucket --bucket <your bucket name> --region us-east-1
```

</p>
</details>

### Enable S3 bucket logging to track access logs, cross region replication

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api put-bucket-logging --bucket <your bucket name> --bucket-logging-status file://logging.json
# Logging can include cross region replication
aws s3api put-bucket-logging \
    --bucket <your bucket name> \
    --bucket-logging-status '{
        "LoggingEnabled": {
            "TargetBucket": "logs-bucket",
            "TargetPrefix": "<your bucket name>-logs/"
        }
    }'
```

</p>
</details>

### Add tags to organize resources in your buckets

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api put-bucket-tagging --bucket <your bucket name> --tagging 'TagSet=[{Key=Environment,Value=Dev}]'
```

</p>
</details>

### Replicate objects to another bucket

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api put-bucket-replication --bucket <your source bucket name> --replication-configuration file://replication.json

# Replication.json
{
  "Role": "arn:aws:iam::123456789012:role/replication-role",
  "Rules": [
    {
      "ID": "ReplicationRule1",
      "Status": "Enabled",
      "Prefix": "", 
      "Destination": {
        "Bucket": "arn:aws:s3:::destination-bucket-name",
        "StorageClass": "STANDARD"
      }
    }
  ]
}

```

</p>
</details>

### Initiate Multipart Upload (For large files)

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
# Initiate Multipart Upload - It returns you an upload-id
aws s3api create-multipart-upload --bucket <your bucket name> --key <file>

# Upload Parts
# part-number ranges from 1 to 10,000.
# Each part except the last must be at least 5 MB.
aws s3api upload-part --bucket <your bucket name> --key <file> --part-number <part-number> --body <file-part> --upload-id <upload-id>
# Example:
aws s3api upload-part --bucket mi-bucket --key file.zip --part-number 1 --body parte1.zip --upload-id <upload-id>
# Complete Multipart Upload
aws s3api complete-multipart-upload --bucket <your bucket name> --key <file> --upload-id <upload-id> --multipart-upload file://parts.json
# Abort Multipart Upload (if needed)
aws s3api abort-multipart-upload --bucket <your bucket name> --key <file> --upload-id <upload-id>
```

</p>
</details>

# Storage classes and lifecycle policies

Storage classes(You assign a storage class when uploading objects):

| Class                | Description                          |
| -------------------- | ------------------------------------ |
| STANDARD             | Frequent access, higher cost         |
| STANDARD\_IA         | Infrequent access, lower cost        |
| ONEZONE\_IA          | Infrequent access, single AZ         |
| GLACIER              | Long-term archive, slow retrieval    |
| DEEP\_ARCHIVE        | Very long-term archive, minimal cost |
| INTELLIGENT\_TIERING | Automatic tiering based on access    |


### Upload an object using storage class STANDARD_IA

---


<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api put-object --bucket <your bucket name> --key <file> --body ./<file location>/ --storage-class STANDARD_IA
```

</p>
</details>

### Upload an object using GLACIER storage class

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api put-object --bucket <your bucket name> --key <file> --body ./<file location>/ --storage-class GLACIER
```

</p>
</details>

### Create an S3 lifecycle policy that automatically deletes objects older than 30 days or transitions them to Glacier storage

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api put-bucket-lifecycle-configuration --bucket <your bucket name> --lifecycle-configuration file://lifecycle.json

# lifecycle.json
{
  "Rules": [
    {
      "ID": "MoveToGlacierAndExpire",
      "Status": "Enabled",
      "Filter": {
        "Prefix": "" 
      },
      "Transitions": [
        {
          "Days": 30,
          "StorageClass": "GLACIER"
        }
      ],
      "Expiration": {
        "Days": 365
      }
    }
  ]
}


```

</p>
</details>

### Transition objects older than 60 days to STANDARD_IA

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api put-bucket-lifecycle-configuration --bucket <your bucket name> --lifecycle-configuration file://lifecycle-standard-ia.json

# lifecycle-standard-ia
{
  "Rules": [
    {
      "ID": "TransitionAndExpire",
      "Status": "Enabled",
      "Filter": {
        "Prefix": "" 
      },
      "Transitions": [
        {
          "Days": 60,
          "StorageClass": "STANDARD_IA"
        },
        {
          "Days": 90,
          "StorageClass": "GLACIER"
        }
      ],
      "Expiration": {
        "Days": 365
      }
    }
  ]
}


```

</p>
</details>

### Auto-delete objects older than 365 days

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api put-bucket-lifecycle-configuration --bucket <your bucket name> --lifecycle-configuration file://lifecycle-expire.json

# lifecycle-expire.json
{
  "Rules": [
    {
      "ID": "AutoDelete365Days",
      "Status": "Enabled",
      "Filter": {
        "Prefix": ""
      },
      "Expiration": {
        "Days": 365
      }
    }
  ]
}


```

</p>
</details>


# Other useful utilities

### List objects with details and filter using --query

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api list-objects --bucket  <your bucket name> --query "Contents[].{Key: Key, Size: Size}"
```

</p>
</details>

### List public objects in a bucket without using credentials by skipping the request signature.

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3 ls s3:// <your bucket name> --no-sign-request
```

</p>
</details>

### View bucket policies

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api get-bucket-policy --bucket <your bucket name>
```

</p>
</details>
