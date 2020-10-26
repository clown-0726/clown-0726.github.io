---
title: My code snippet 01
categories:
    - Snippet
tags:
    - Snippet
---
在这里，分享值得记录下来的好的文章～

<!--more-->

# AWS Snippet

#### S3 - Loop bucket file

```python
s3resource = boto3.resource('s3', aws_access_key_id=access_key, aws_secret_access_key=secret_access_key)
cur_bucket = 'xxx'
s3_bucket = self.s3resource.Bucket(cur_bucket)

for ind, obj in enumerate(s3_bucket.objects.filter(Prefix=prefix)):
    data_json_raw = json.loads(obj.get()['Body'].read())
```

#### S3 - Copy from one to another

```python
old_file_name_with_bucket_name = cur_bucket + '/' + obj.key
s3resource.Object(cur_bucket, str(obj.key)).copy_from(CopySource=old_file_name_with_bucket_name)
```

#### S3 - Delete file

```python
self.s3resource.Object(bucket_name, entire_path).delete()
```

#### S3 - Save file with pre check

```python
# Reference: https://stackoverflow.com/questions/33842944/check-if-a-key-exists-in-a-bucket-in-s3-using-boto3
try:
	self.s3resource.Object(bucket_name, entire_path).load()
except botocore.exceptions.ClientError as e:
  if e.response['Error']['Code'] == "404":
    # The object does not exist.
    object_put = self.s3resource.Object(bucket_name, entire_path)
    return object_put.put(Body=json.dumps(item))
  else:
    # Something else has gone wrong.
    return "failed"
else:
  # The object does exist.
  return "failed"
```

#### S3 - Read single file content

```python
obj = s3resource.Object(bucket_name=bucket_name, key=entire_path)
obj.get()['Body'].read()
```

#### S3 - Get file amount

```python
s3_bucket = self.s3resource.Bucket(bucket_name)
sum(1 for _ in s3_bucket.objects.filter(Prefix=prefix))
```

#### Write file to local

```python
def save_data_local(self, report_folder, report_path, final_str):
  if not os.path.exists(report_folder):
    os.mkdir(report_folder)
  if not os.path.isfile(report_path):
    with open(report_path, 'wb') as f:
      f.write(str(final_str).encode('utf-8'))
      f.close()
```



#### Get all str between given term

```python
contents_extracted_list = re.findall(r"<html.*?</html>", contents, flags=re.DOTALL)
for contents_extracted in contents_extracted_list:
    print(contents_extracted)
```


# Python
#### Remove all tags

```python
def remove_all_tags(with_tag):
    return str(re.sub(r'<.*?>', ' ', with_tag))
```



#### Get file name with uuid

```python
def get_file_name_by_uuid(line, extension=''):
    return str(uuid.uuid3(uuid.NAMESPACE_DNS, str(line))) + extension
```


#### Read a file line-by-line into a list?

```python
with open('all_pscode') as f:
    content = f.readlines()
# you may also want to remove whitespace characters like `\n` at the end of each line
content = [x.strip() for x in content]
```

