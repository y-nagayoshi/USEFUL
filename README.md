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
TNS_NAME=<TNS_NAME
```