```sql

DROP STREAM submission_stream;
CREATE STREAM submission_stream (
  clpiaId STRING,
  bbId STRING,
  requestTrackingNumber STRING,
  applicationStatus STRING,
  stakeholderCode STRING,
  serviceType STRING,
  payload STRING
) WITH (
  KAFKA_TOPIC='ems.application.submission.topic',
  VALUE_FORMAT='JSON',
  PARTITIONS=1,
  REPLICAS=1
);

SELECT *
FROM submission_stream
WHERE requestTrackingNumber = 'BBP.DOESC.20260304.367FE7A5'
EMIT CHANGES;
```