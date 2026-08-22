# PostgreSQL → Debezium → Kafka CDC

This project demonstrates Change Data Capture (CDC) from PostgreSQL to Kafka using Debezium.

## Architecture

```text
┌──────────────┐
│  PostgreSQL  │
└──────┬───────┘
       │
       │ Logical Replication
       │
       ▼
┌──────────────────────┐
│       Debezium       │
└──────────┬───────────┘
           │
           │ CDC Events
           ▼
┌──────────────────────┐
│        Kafka         │
└──────────────────────┘
           │
           │ (Optional) Sink
           ▼
┌──────────────────────┐
│          S3          │
└──────────────────────┘

```

### Pre-requisites
#### Create a replica identity full for specific table:
```bash
ALTER TABLE public.students REPLICA IDENTIY FULL;
```

#### Create a Debezium Publication
```bash
CREATE PUBLICATION cdc_db_test
FOR ALL TABLES;
```

### Deploy the Connector
Source Connector:
```bash
curl -i \
  -X POST \
  -H "Accept:application/json" \
  -H "Content-Type:application/json" \
  http://debezium:8083/connectors/ \
  --data @/workspace/source/postgres/db_test.json
```

(Optional) Sink Connector:
```bash
curl -i \
  -X POST \
  -H "Accept:application/json" \
  -H "Content-Type:application/json" \
  http://debezium:8083/connectors/ \
  --data @/workspace/sink/s3/db_test.json
```


### Delete the connector
```bash
curl -X DELETE http://debezium:8083/connectors/postgres-debezium-source-connector
```

### Verify Connector
```bash
curl -s \
  http://debezium:8083/connectors/postgres-debezium-source-connector/status \
  | jq
```

### Troubleshooting
Check Kafka topics:
```bash
kcat -b broker-cdc-1:9092 -L
```

Check Debezium logs:
```bash
docker logs debezium --tail 200
```

Check PostgreSQL:
```bash
SHOW wal_level;

SELECT *
FROM pg_replication_slots;

SELECT pubname, puballtables
FROM pg_publication;
```