```python
import json
import boto3
import uuid
from datetime import datetime

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('UploadLog')

def lambda_handler(event, context):
    print("Received event:", json.dumps(event, indent=2))

    for record in event['Records']:
        bucket_name = record['s3']['bucket']['name']
        object_key = record['s3']['object']['key']
        size = record['s3']['object'].get('size', 0)
        event_name = record.get('eventName', 'Unknown')
        event_time = record.get('eventTime', 'Unknown')

        if "ObjectRemoved" in event_name:
            continue

        item = {
            'unique': str(uuid.uuid4()),
            'Bucket': bucket_name,
            'Object': object_key,
            'Size': size,
            'Event': event_name,
            'EventTime': event_time
        }

        table.put_item(Item=item)

    return {
        'statusCode': 200,
        'body': json.dumps('Success')
    }

```


 SNS SQS
 
```python
def lambda_handler(event, context):
    for record in event['Records']:
        print("Message received from SQS:")
        print(record['body'])

    return {
        'statusCode': 200,
        'body': 'Message processed successfully'
    } 
```
