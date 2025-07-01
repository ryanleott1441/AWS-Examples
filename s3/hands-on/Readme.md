## Create a bucket 
aws s3 mb s3://leotti

## Change block public access
aws s3api put-public-access-block \
    --bucket leotti \
    --public-access-block-configuration "BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=false,RestrictPublicBuckets=false"
## Create a bucket policy 
aws s3api put-bucket-policy --bucket leotti --policy file://policy.json
## Turn on static wesbite hosting
aws s3api put-bucket-website --bucket leotti --website-configuration file://website.json
## Upload our index.html file and include a resource that be cross origin 
aws s3 cp index.html s3://leotti

## Get wesbite endporint for s3
aws s3api getput-bucket-website --bucket leotti 
## Apply a CORS Policy



