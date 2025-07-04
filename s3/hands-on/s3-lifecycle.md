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

  ## with prefex,
  "Filter": { 
        "Prefix": "logs/" This tags with prefex

        }
      },

  ## with tags, i am going to add a tag to an existing object
  aws s3api put-object-tagging \
    --bucket leotti \
    --key hello.txt \
    --tagging 'TagSet=[{Key=archive,Value=true}]'

  ## confirm the tag 
  aws s3api get-object-tagging \
  --bucket leotti \
  --key hello.txt

  ## json doc
     "Filter": { 
        "Tag":{
          "Key": "archive",
          "Value": "true"
        }
      },


  ## using object size

  "ID": "LogsToStandardIA",
      "Filter": { 
         "And": {
          "Prefix": "",
          "ObjectSizeGreaterThan": 10485760  // 10 MB
        }
        
        "Prefix": "logs/" This tags with prefex

        "Tag":{
          "Key": "archive",
          "Value": "true"
        }
      },
  #### creating rule that only affects an object with a minimum nd maximum size
        "Prefix": "";
        "ObjectSizeGreaterThan": 1048576,        // 1 MB
        "ObjectSizeLessThan": 52428800           // 50 MB

## upload policy make sure you reupload when change something
aws s3api put-bucket-lifecycle-configuration \
  --bucket leotti \
  --lifecycle-configuration file://s3/JSON/lifecyclepolicy.json

## apply lifecycle to current or non current versions\
{
  "Rules": [
    {
      "ID": "NonCurrentToGlacier",
      "Filter": {
        "Prefix": ""
      },
      "Status": "Enabled",
      "NoncurrentVersionTransitions": [
        {
          "NoncurrentDays": 30,
          "StorageClass": "GLACIER"
        }
      ],
      "NoncurrentVersionExpiration": {
        "NoncurrentDays": 90
      }
    }
  ]
}

## lifecycle that goes through multi-tier cycle
{
  "Rules": [
    {
      "ID": "FullTieredStorageLifecycle",
      "Filter": {
        "Prefix": ""
      },
      "Status": "Enabled",
      "Transitions": [
        {
          "Days": 30,
          "StorageClass": "STANDARD_IA"
        },
        {
          "Days": 90,
          "StorageClass": "ONEZONE_IA"
        },
        {
          "Days": 365,
          "StorageClass": "GLACIER"
        },
        {
          "Days": 1095,
          "StorageClass": "DEEP_ARCHIVE"
        }
      ]
    }
  ]
}

## delete current version after 10 years and delete noncurrent versions permanetly after 1 year
{
  "Rules": [
    {
      "ID": "TieredStorageWithExpiration",
      "Filter": {
        "Prefix": ""
      },
      "Status": "Enabled",
      "Transitions": [
        {
          "Days": 30,
          "StorageClass": "STANDARD_IA"
        },
        {
          "Days": 90,
          "StorageClass": "ONEZONE_IA"
        },
        {
          "Days": 365,
          "StorageClass": "GLACIER"
        },
        {
          "Days": 1095,
          "StorageClass": "DEEP_ARCHIVE"
        }
      ],
      "Expiration": {
        "Days": 3650
      },
      "NoncurrentVersionExpiration": {
        "NoncurrentDays": 365
      }
    }
  ]
}

##  Step 11: Delete expired delete markers & incomplete multipart uploads
{
  "Rules": [
    {
      "ID": "FullLifecycleManagement",
      "Filter": {
        "Prefix": ""
      },
      "Status": "Enabled",
      "Transitions": [
        {
          "Days": 30,
          "StorageClass": "STANDARD_IA"
        },
        {
          "Days": 90,
          "StorageClass": "ONEZONE_IA"
        },
        {
          "Days": 365,
          "StorageClass": "GLACIER"
        },
        {
          "Days": 1095,
          "StorageClass": "DEEP_ARCHIVE"
        }
      ],
      "Expiration": {
        "Days": 3650
      },
      "NoncurrentVersionExpiration": {
        "NoncurrentDays": 365
      },
      "AbortIncompleteMultipartUpload": {
        "DaysAfterInitiation": 7
      }
    },
    {
      "ID": "DeleteMarkers",
      "Filter": {
        "Prefix": ""
      },
      "Status": "Enabled",
      "Expiration": {
        "ExpiredObjectDeleteMarker": true
      }
    }
  ]
}
## upload policy make sure you reupload when change something
aws s3api put-bucket-lifecycle-configuration \
  --bucket leotti \
  --lifecycle-configuration file://s3/hands-on/JSON/lifecyclepolicy.json