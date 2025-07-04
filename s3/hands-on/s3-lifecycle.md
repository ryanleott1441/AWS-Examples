## create a bucket 
aws s3 mb s3://leotti

## upload a file with a different storage type
 aws s3api put-object --bucket leotti --key hello.txt --storage-class STANDARD_IA

## change an existing files storage type 
aws s3api copy-object --bucket leotti --key hello.txt --copy-source leotti/hello.txt --storage-class STANDARD
###### there is an issue here!! this copyies the object and essentials creates a new object with the different storage clas
###### so we will try to cerate a bash script for this instance!