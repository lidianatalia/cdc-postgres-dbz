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
```

### Pre-requisites
#### Create a replica identity full for specific table:
```bash
ALTER TABLE public.students REPLICA IDENTIY FULL;
```

#### Create a Debezium Publication
Create a publication for all tables:
```bash
CREATE PUBLICATION cdc_db_test
FOR ALL TABLES;
```

### Deploy the Connector
```bash
curl -i \
  -X POST \
  -H "Accept:application/json" \
  -H "Content-Type:application/json" \
  http://debezium:8083/connectors/ \
  --data @/workspace/source/postgres.json
```

### Delete the connector
```bash
curl -X DELETE http://debezium:8083/connectors/db_test-connector
```

### Verify Connector
```bash
curl -s \
  http://debezium:8083/connectors/exampledb-connector/status \
  | jq
```

### Troubleshooting
Check connector status
```bash
curl -s \
  http://debezium:8083/connectors/exampledb-connector/status \
  | jq
```