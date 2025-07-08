## create a user with no prmissions

aws iam create-user --user-name leotti
aws iam create-access-key --user-name leotti --output table

Test who you are and open creditals 

aws sts get-caller-identity
open ~/.aws/credentials 

aws sts get-caller-identity --profile sts 




## create a role

## Use new user creds and assume role