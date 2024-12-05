### Grafana

```
sudo docker run -dit --name cs  -p 9000:9000/tcp -p 8123:8123  clickhouse/clickhouse-server:24.11-alpine

sudo docker exec -i cs clickhouse-client -mn -q "INSERT INTO btech FORMAT CSV" < data.csv
```