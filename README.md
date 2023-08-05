# AWS-glue-ETL

## Business use case
```
We have data available in Amazon S3 storage in the CSV format and we want to create a pipeline
that will be able to transfer data from Amazon S3 location into another S3 bucket of avro format.
The pipeline should run through a lambda trigger.
```
### Steps
#### 1) Create an S3 bucket
![image](https://github.com/DhanashriSaner/AWS-glue-ETL/assets/88526990/b92eb290-3e02-413e-997a-03d9f2af028f)

##### Upload the data into inputbucket
![image](https://github.com/DhanashriSaner/AWS-glue-ETL/assets/88526990/e24434ae-bba3-4879-8d26-6f32404007ab)


#### 2) Create a crawler
![image](https://github.com/DhanashriSaner/AWS-glue-ETL/assets/88526990/9b89abc9-cb90-48b1-9434-6d78df1f775c)

#### 3) Create ETL jobs script
**Source**
![image](https://github.com/DhanashriSaner/AWS-glue-ETL/assets/88526990/b3c9c615-0f1f-46db-8fc4-6a090d809b58)
**Transformation**
![image](https://github.com/DhanashriSaner/AWS-glue-ETL/assets/88526990/2ceb0eff-88f0-4523-b2eb-071af9f92c08)
**Target**
![image](https://github.com/DhanashriSaner/AWS-glue-ETL/assets/88526990/fcdc3dd7-c6ff-496f-afc2-7d8505fc9abd)

**Script**

```
import sys
from awsglue.transforms import *
from awsglue.utils import getResolvedOptions
from pyspark.context import SparkContext
from awsglue.context import GlueContext
from awsglue.job import Job

args = getResolvedOptions(sys.argv, ["JOB_NAME"])
sc = SparkContext()
glueContext = GlueContext(sc)
spark = glueContext.spark_session
job = Job(glueContext)
job.init(args["JOB_NAME"], args)

# Script generated for node S3 bucket
S3bucket_node1 = glueContext.create_dynamic_frame.from_catalog(
    database="glue", table_name="inputetlbucket123", transformation_ctx="S3bucket_node1"
)

# Script generated for node Select Fields
SelectFields_node1691226854212 = SelectFields.apply(
    frame=S3bucket_node1,
    paths=[
        "ordernumber",
        "quantityordered",
        "priceeach",
        "sales",
        "status",
        "customername",
        "city",
        "country",
    ],
    transformation_ctx="SelectFields_node1691226854212",
)

# Script generated for node Amazon S3
AmazonS3_node1691227003853 = glueContext.write_dynamic_frame.from_options(
    frame=SelectFields_node1691226854212,
    connection_type="s3",
    format="avro",
    connection_options={
        "path": "s3://outputetlbucket123",
        "compression": "snappy",
        "partitionKeys": [],
    },
    transformation_ctx="AmazonS3_node1691227003853",
)

job.commit()
```

#### 4) Do Transformation
#### 5) Attach IAM policy
#### 6) Lambda trigger

