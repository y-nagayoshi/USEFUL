# Oracle DBセッティング

## DB権限付与SQL

```
DECLARE
    v_username VARCHAR2(30) := 'SCHEMA_NAME<>';
BEGIN
    EXECUTE IMMEDIATE 'GRANT CREATE SESSION TO ' || v_username;
    
    EXECUTE IMMEDIATE 'GRANT CREATE ANY TABLE TO ' || v_username;
    EXECUTE IMMEDIATE 'GRANT ALTER ANY TABLE TO ' || v_username;
    EXECUTE IMMEDIATE 'GRANT DROP ANY TABLE TO ' || v_username;
    EXECUTE IMMEDIATE 'GRANT SELECT ANY TABLE TO ' || v_username;
    
    EXECUTE IMMEDIATE 'GRANT UNLIMITED TABLESPACE TO ' || v_username;
    
    EXECUTE IMMEDIATE 'GRANT ALTER ANY SEQUENCE TO ' || v_username;
    EXECUTE IMMEDIATE 'GRANT CREATE ANY SEQUENCE TO ' || v_username;
    EXECUTE IMMEDIATE 'GRANT DROP ANY SEQUENCE TO ' || v_username;
    EXECUTE IMMEDIATE 'GRANT SELECT ANY SEQUENCE TO ' || v_username;
END;
```

## django settings.py

```
import oracledb
from decouple import config

oracledb.init_oracle_client(lib_dir="/usr/local/oracle/instantclient_23_3")
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.oracle',
        'NAME': config('TNS_NAME'),
        'USER': config('DB_USER'),
        'PASSWORD': config('DB_PASSWORD'),
    }
}
```

## requirements.txt

```
oci
oracledb
python-decouple
```

## .env

```
DB_USER=<DB_USER>
DB_PASSWORD=<DB_PASSWORD>
TNS_NAME=<TNS_NAME>
```

# オブジェクトストレージセッティング
## requirements.txt

```
boto3
django-storages
```

## バケットをパブリックにする
- 参考：[Support for Oracle Object Storage #1127](https://github.com/jschneier/django-storages/issues/1127)
- 参考：[Use AWS Python SDK to Upload Files to Oracle Cloud Infrastructure Object Storage](https://medium.com/oracledevs/use-aws-python-sdk-to-upload-files-to-oracle-cloud-infrastructure-object-storage-b623e5681412)
- 参考：[Storing Django Static and Media Files on Amazon S3](https://testdriven.io/blog/storing-django-static-and-media-files-on-amazon-s3/)

## .env

```
ORACLE_ACCESS_KEY=<ORACLE_ACCESS_KEY>
ORACLE_CUSTOMER_SECRET_KEY=<ORACLE_CUSTOMER_SECRET_KEY>
ORACLE_BUCKET_NAME=<ORACLE_BUCKET_NAME>
ORACLE_BUCKET_NAMESPACE=<ORACLE_BUCKET_NAMESPACE>
ORACLE_REGION=<ORACLE_REGION>
```

## settings.py

```
from decouple import config
import os

ORACLE_BUCKET_NAME = os.environ.get('ORACLE_BUCKET_NAME')
ORACLE_BUCKET_NAMESPACE = os.environ.get('ORACLE_BUCKET_NAMESPACE')
ORACLE_REGION = os.environ.get('ORACLE_REGION')

AWS_ACCESS_KEY_ID = os.environ.get('ORACLE_ACCESS_KEY')
AWS_SECRET_ACCESS_KEY = os.environ.get('ORACLE_CUSTOMER_SECRET_KEY')
AWS_STORAGE_BUCKET_NAME = ORACLE_BUCKET_NAME
AWS_S3_CUSTOM_DOMAIN = f"{ORACLE_BUCKET_NAMESPACE}.compat.objectstorage.{ORACLE_REGION}.oraclecloud.com"
AWS_S3_ENDPOINT_URL = f"https://{AWS_S3_CUSTOM_DOMAIN}"
AWS_S3_OBJECT_PARAMETERS = {
    'CacheControl': 'max-age=86400',
}

DEFAULT_FILE_STORAGE = "storages.backends.s3boto3.S3Boto3Storage"
MEDIA_URL = f"https://{AWS_S3_CUSTOM_DOMAIN}/{ORACLE_BUCKET_NAME}/"
```

## models.py

```
class Product(models.Model):
    image = models.ImageField(
        upload_to=f'{settings.ORACLE_BUCKET_NAME}/products/%Y/%m/%d',
        blank=True
    )
```

## template.html

```
<img src="{% if product.image %}{{ product.image.url }}{% else %}{% static "img/no_image.png" %}{% endif %}">
```