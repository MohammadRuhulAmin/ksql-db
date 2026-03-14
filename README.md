KSQL-DB

to enter:
```sh
docker exec -it ksql-testing-ksqldb-server-1  ksql http://localhost:8088
```

```sql
# show topics:
show TOPICS;
# show streams/tables
SHOW TABLES;
SHOW STREAMS;
# Which Stream is using in which topic
DESCRIBE EXTENDED test_stream;
```

create a stream with topic
```sql
CREATE STREAM test_stream (
  id INT,
  name STRING
) WITH (
  KAFKA_TOPIC='test-topic',
  VALUE_FORMAT='JSON',
  PARTITIONS=1,
  REPLICAS=1
);
# drop a stream
DROP STREAM test_stream;
```
Message publishing using Query:

```sql
INSERT INTO test_stream (id, name) VALUES (1, 'Ruhul Amin');
```


unstractured stream create

```sql
CREATE STREAM raw_stream (
  user STRUCT<name STRING, age INT>
) WITH (
  KAFKA_TOPIC='test-topic',
  VALUE_FORMAT='JSON'
);

# insert unstractured data:
INSERT INTO raw_stream (user)
VALUES (STRUCT(name := 'Ruhul Amin', age := 27));
INSERT INTO raw_stream (user)
VALUES (
  STRUCT(
    name := 'Ruhul Amin',
    age := 27,
    about := STRUCT(
      school := 'ruhusxx',
      email := 'ruh@gml'
    )
  )
);

SET 'auto.offset.reset' = 'earliest';

SELECT user->name FROM raw_stream EMIT CHANGES;

```



creating a topic :

```sql
CREATE STREAM raw_json_stream (
    payload STRING
) WITH (
    KAFKA_TOPIC='raw-json-topic',
    VALUE_FORMAT='KAFKA',
    PARTITIONS=1,
    REPLICAS=1
);
SELECT * FROM raw_json_stream EMIT CHANGES;
INSERT INTO raw_json_stream (payload)
VALUES (
'{
  "vat": 5,
  "binNo": "",
  "bankVat": 0,
  "bankName": "SONALI_BANK",
  "name":{
     "email":"ruhul@basystems.com",
     "contact": "+880-1322352864"
  }
  
}');

SELECT
  EXTRACTJSONFIELD(payload,'$.vat') AS clpiaId
FROM raw_json_stream
EMIT CHANGES;

SELECT
  EXTRACTJSONFIELD(payload,'$.vat') AS vat
FROM raw_json_stream
WHERE EXTRACTJSONFIELD(payload,'$.vat') = '5'
EMIT CHANGES;

SELECT
EXTRACTJSONFIELD(payload,'$.name.contact') AS contact
FROM raw_json_stream
EMIT CHANGES;
```