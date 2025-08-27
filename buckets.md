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

### Create an index.html, css/index.css, js/index.js and upload them to a s3 bucket, you can use the next commands:

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
aws s3 ls s3://<your bucket name>
```

</p>
</details>

### List all objects in your s3 bucket using a recursive list(Including folders and subfolders)

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

### Copy the index html in another s3 bucket

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3 cp s3://<your bucket name source>/<your file> s3://<your bucket name destination>/
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

## Basic permissions

- READ → Allows reading the object or listing the bucket.

- WRITE → Allows uploading or modifying objects in the bucket.

- READ_ACP → Allows reading the ACL configuration.

- WRITE_ACP → Allows modifying the ACL configuration.

- FULL_CONTROL → Allows all of the above.

### List all permissions in your s3 bucket

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
```

</p>
</details>

### Create an S3 bucket with public write permissions

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api create-bucket --bucket <your bucket name> --acl public-write
```

</p>
</details>

### Grant full public access and control to a bucket

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

### Make an object ACL modifiable by the owner

---

<details>
<summary>Show commands / answers</summary>
<p>


```bash
aws s3api put-object-acl --bucket <your bucket name> --key index.html --acl bucket-owner-full-control
```

</p>
</details>

### Make aunthenticated users to see and download files from your bucket

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws s3api put-object-acl --bucket <your bucket name> --acl authenticated-users
```

</p>
</details>

# Grant permissions to an specific user

- grant-read	 →  Allows reading the object
- grant-write	 →  Allows writing (buckets only)
- grant-read-acp	→  Allows reading the ACLs
- grant-write-acp	→  Allows modifying the ACLs
- grant-full-control	→  Grants all of the above permissions
