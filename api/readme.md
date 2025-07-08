## create a user with no prmissions

aws iam create-user --user-name leotti
aws iam create-access-key --user-name leotti --output table

Test who you are and open creditals 

aws sts get-caller-identity
open ~/.aws/credentials 

aws sts get-caller-identity --profile sts 


## Use new user creds and assume role

aws sts assume-role \
  --role-arn arn:aws:iam::804054839699:role/my-sts-fun-stack-StsRole-beWyBgRTA2MJ \
  --role-session-name s3-sts-fun \
  --profile sts

aws iam put-user-policy \
  --user-name Bob \
  --policy-name ExamplePolicy \
  --policy-document file://AdminPolicy.json