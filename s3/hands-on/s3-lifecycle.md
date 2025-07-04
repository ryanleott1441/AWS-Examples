## create a bucket 
aws s3 mb s3://leotti

## upload a file with a different storage type
 aws s3api put-object --bucket leotti --key hello.txt --storage-class STANDARD_IA

## change an existing files storage type 
bash s3/bash-scripts/change-storage-type leotti hello.txt STANDARD_IA
###### there is an issue here!! this copyies the object and essentials creates a new object with the different storage clas
###### so we will try to cerate a bash script for this instance!
bash s3/bash-scripts/change-storage-type
###### this script took me a bit lol as im still leanring scripting but it copies an object places it in the new storage type
###### and then deletes the old version

## create a lifecycle for an object
## fist thing here is we need to create a JSON document, then we will add it to our bucket 
aws s3api put-bucket-lifecycle-configuration \
  --bucket leotti \
  --lifecycle-configuration file://s3/JSON/lifecyclepolicy.json

## confirm it is added 
aws s3api get-bucket-lifecycle-configuration \
  --bucket leotti

## on the JSON document we have add comments for different ways to filter based on prefix, tags, and object size

## with tags, i am going to add a tag to an existing object
aws s3api put-object-tagging \
  --bucket leotti \
  --key hello.txt \
  --tagging 'TagSet=[{Key=archive,Value=true}]'