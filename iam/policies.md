## Create a yaml file then convert using yq 

download yq
sudo wget https://github.com/mikefarah/yq/releases/latest/download/yq_linux_amd64 -O /usr/local/bin/yq
sudo chmod +x /usr/local/bin/yq


## convert our policy
yq -o=json iam/policies/mypolicy.yaml > policy.json

## add inline policy
aws iam put-user-policy \
  --user-name Ryan \
  --policy-name S3ListPolicy \
  --policy-document file://policy.json

## add managed policy
aws iam attach-user-policy \
  --user-name my-user \
  --policy-arn arn:aws:iam::aws:policy/ReadOnlyAccess

## add customer managed policy

aws iam create-policy \
  --policy-name MyS3FullPolicy \
  --policy-document file://my-s3-policy.json

aws iam attach-user-policy \
  --user-name my-user \
  --policy-arn arn:aws:iam::<your-account-id>:policy/MyS3FullPolicy

## list policies
aws iam list-policy-versions \
  --policy-arn arn:aws:iam::ACCOUNT_ID:policy/POLICY_NAME

## delete non current policies
aws iam delete-policy-version \
  --policy-arn arn:aws:iam::ACCOUNT_ID:policy/POLICY_NAME \
  --version-id v2

## detach policy
aws iam delete-user-policy \
  --user-name my-user \
  --policy-name MyInlinePolicy

aws iam detach-user-policy \
  --user-name my-user \
  --policy-arn arn:aws:iam::ACCOUNT_ID:policy/POLICY_NAME

## delete policy
aws iam delete-policy \
  --policy-arn arn:aws:iam::ACCOUNT_ID:policy/POLICY_NAME

