## create a bucket 
aws s3 mb s3://leotti

## enable versioning
aws s3api put-bucket-versioning \
  --bucket leotti \
  --versioning-configuration Status=Enabled

## upload file
echo "Hello23" > hello.txt
aws s3 cp hello.txt s3://leotti

## delete the current version of the file
  aws s3 rm s3://leotti/hello.txt

  aws s3api delete-object \
  --bucket leotti \
  --key hello.txt

## list versions of a object
aws s3api list-object-versions \
  --bucket leotti \
  --prefix hello.txt

## delete a version of the object
aws s3api delete-object \
  --bucket leotti \
  --key hello.txt \
  --version-id nLQVQeoRtvB9eBjlahnS3tbbXxsIYafu

## Recover the file
aws s3api delete-object \
  --bucket leotti \
  --key hello.txt \
  --version-id DELETE_MARKER_VERSION_ID

## delete all verisons of a file
for type in Versions DeleteMarkers; do
  aws s3api list-object-versions \
    --bucket leotti \
    --prefix hello.txt \
    --query="$type[].{Key:Key,VersionId:VersionId}" \
    --output text | grep -v '^$' | while read key version; do
      if [[ -n "$version" ]]; then
        aws s3api delete-object --bucket leotti --key "$key" --version-id "$version"
      fi
  done
done

## check to make sure all versions and file is deleted
aws s3api list-object-versions \
  --bucket leotti \
  --prefix hello.txt