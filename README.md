# Change-Data-Capture
![image](https://user-images.githubusercontent.com/35773041/160399460-8cc37ddd-4ac1-424b-be51-12b20a2ce6de.png)

```
docker run --tty --network postgres_debezium_cdc_master_default confluentinc/cp-kafkacat kafkacat -b kafka:9092 -C -s key=s -s value=avro -r http:/schema-registry:8081 -t postgres.schemaname.tablename (Tail the kafka topic to see if it's listening to debezium postgres changes)

```
curl -i -X POST -H "Accept:application/json" -H "Content-Type:application/json" 127.0.0.1:8083/connectors/ --data "@debezium.json"


```
docker run --tty --network change-data-capture_debezium-net confluentinc/cp-kafkacat kafkacat -b kafka:9092 -C -s key=s -s value=avro -r http:
/schema-registry:8081 -t postgres.public.claims
```
http://localhost:8083/connectors/postgres-connector
http://localhost:8083/connectors/postgres-connector/status

```
 docker run --tty --network change-data-capture_debezium-net confluentinc/cp-kafkacat   kafkacat -b kafka:9092 -C -J -t postgres-server.public.claims

```
create table public.claims(claim_id integer, claim_data date,member_id integer,claim_status char(1));

alter table public.claims replica identity full; 

insert into public.claims values(1,'2020-02-02',2,'p');
insert into public.claims values(2,'2020-03-02',2,'p');


1. Create a Sink Connector to Sync Tables
bash
# Create a sink connector that reads from claims topic and writes to 

```
curl -X DELETE http://localhost:8083/connectors/claims-to-copy-sink

curl -X PUT http://localhost:8083/connectors/claims-to-copy-sink/config \
  -H "Content-Type: application/json" \
  -d '{
    "connector.class": "io.debezium.connector.jdbc.JdbcSinkConnector",
    "tasks.max": "1",
    "topics": "postgres-server.public.claims",
    "connection.url": "jdbc:postgresql://postgres:5432/exampledb",
    "connection.username": "docker",
    "connection.password": "docker",
    "table.name.format": "claims_copy",
    "transforms": "unwrap",
    "transforms.unwrap.type": "io.debezium.transforms.ExtractNewRecordState"
  }'
```
2. Alternative: Use Kafka Streams for Transformation
If you need to transform data between original and copy:

bash
# This would require a custom Kafka Streams application
# Example structure:
```
docker run --network debezium-net \
  -e BOOTSTRAP_SERVERS=kafka:9092 \
  -e SOURCE_TOPIC=postgres-server.public.claims \
  -e TARGET_TOPIC=postgres-server.public.claims_copy \
  your-kafka-streams-app
```