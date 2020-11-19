# s3image
AWS Lambda function to view image file on S3 bucket

## Flow
### 1. SSM Parameter Store
**TransferS3_Bucket**
```
{
	"bucket":"dittosdev",
	"key":"shared",
	"path":"/storage/"
}
```

### 2. Upload image to s3://dittosdev/shared
```
aws s3 cp c:\tmp\image s3://dittosdev/shared --recursive
```
- Pic/20200610/20200610104537/0/020200610105526775.jpg
- Pic/20200610/20200610104537/0/020200610105528015.jpg
- Pic/20200610/20200610104537/0/020200610105530723.jpg


### 3. Create a Lambda Function in Python
named **TransferS3**
1. get setup json data from SSM parameter store. key=TransferS3_Bucket
1. decode json. 
1. get query string 'path'
1. s3 getobject
1. read body
1. return image/jpeg with image data encoded in base64
```
    return {
        'statusCode': 200,
        'body': data,
        'isBase64Encoded': True,
        'headers': {
            'Content-Type': 'image/jpeg'
            # 'Content-Disposition': 'attachment; filename=020200610105526775.jpg'
        }
    }
```

### 4. Register ALB rule
path: /storage/* <br>
Target group: s3images<br>
Target type: lambda -> **TransferS3**<br>

### 5. Test
https://www.dummydomain.co.jp/storage/Pic/20200610/20200610104537/0/020200610105526775.jpg
