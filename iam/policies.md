## Create a yaml file then convert using yq 

download yq
sudo wget https://github.com/mikefarah/yq/releases/latest/download/yq_linux_amd64 -O /usr/local/bin/yq
sudo chmod +x /usr/local/bin/yq


## convert our policy
yq -o=json iam/policies/mypolicy.yaml > policy.json

## add the policy
aws iam put-user-policy \
  --user-name Ryan \
  --policy-name S3ListPolicy \
  --policy-document file://policy.json
