# 🔐 AWS CLI – IAM Cheatsheet

---

## List all users on IAM

---
<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws iam list-users
```

</p>
</details>

## Get current identity (current logged user)

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws sts get-caller-identity
aws iam get-user
```
</p>
</details>

## Create an user using IAM

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws iam create-user --user-name <your user>
```
</p>
</details>

## Delete an user

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws iam delete-user --user-name <your user>
```
</p>
</details>

## Create access key for an user

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws iam create-access-key --user-name <your user>
```
</p>
</details>

## List access keys

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws iam list-access-keys --user-name <your user>
```
</p>
</details>

## Delete access key

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws iam delete-access-key \
  --user-name <your user> \
  --access-key-id <access key id>
```
</p>
</details>

## List IAM policies

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws iam list-policies --scope All
```
</p>
</details>

## Get policy metadata

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws iam get-policy --policy-arn arn:aws:iam::aws:policy/<name of the policy>
```
</p>
</details>

## Get policy version (JSON document)

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws iam get-policy-version \
  --policy-arn arn:aws:iam::aws:policy/<your policy> \
  --version-id v1
```
</p>
</details>

##  Attach policy to user

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws iam attach-user-policy \
  --user-name <your user> \
  --policy-arn arn:aws:iam::aws:policy/<your policy>
```
</p>
</details>

## Detach policy from user

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws iam detach-user-policy \
  --user-name <your user> \
  --policy-arn arn:aws:iam::aws:policy/<your policy>
```
</p>
</details>

## List policies attached in an user

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws iam list-attached-user-policies --user-name <your user>
```
</p>
</details>


## Create inline policy for user (custom policy created by you)

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws iam put-user-policy \
  --user-name <your user> \
  --policy-name <your policy name>
  --policy-dcoument <path>
```
</p>
</details>


## Get inline policy

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws iam get-user-policy \
  --user-name <your user> \
  --policy-name <your policy name>
```
</p>
</details>

## Create a group

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws iam create-group --group-name <group name>
```
</p>
</details>


## Add user to group

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws iam add-user-to-group \
  --user-name <your user> \
  --group-name <your group>
```
</p>
</details>

## List all groups

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws iam list-groups
```
</p>
</details>

## List all groups for a user

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws iam list-groups-for-user --user-name <your user>
```
</p>
</details>

## List all users in a group

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws iam get-group --group-name <your group>
```
</p>
</details>

## Attach policy to group

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws iam attach-group-policy \
  --group-name <your group> \
  --policy-arn arn:aws:iam::aws:policy/<your policy>
```
</p>
</details>

## Create a role

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws iam create-role \
  --role-name <your role> \
  --assume-role-policy-document <path of your policy>
```
</p>
</details>

## Attach policy to role

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws iam attach-role-policy \
  --role-name <your role> \
  --policy-arn arn:aws:iam::aws:policy/<your policy>
```

</p>
</details>

## Simulate permissions (debug IAM)

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws iam simulate-principal-policy \
  --policy-source-arn arn:aws:iam::123456789012:user/<your user> \
  --action-names <your action eg. s3:ListBucket>
```

</p>
</details>

## Example trust policy (full access)

```bash
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "*",
      "Resource": "*"
    }
  ]
}
```

## Example permission policy

```bash
{
  "Effect": "Allow",
  "Action": "*",
  "Resource": "*"
}
```



