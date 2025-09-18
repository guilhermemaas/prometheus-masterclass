# prometheus-masterclass
prometheus-masterclass

## Prometheus Server
Retrievel: Is the component of the Prometheus server, which will retrieve the metrics from the instance.
Storage/TSDB - Timeseries Database, Prometheu store metrics here.
HTTP Server - So either you are using the Promsql to query the time series database from the Prometheus or either (Or using the API).

Jobs - Target scheduling scraping
  Intances - Multiples instances of same data (CPU from server1, 2, 3)
Exporter - /metrics, used to retrieve metrics from targets.
Pull metrics - Default config is pull metrics from targets to tsdb.

Prometheus Web UI - Promql queries.

## Push Gateway

Short live Jobs - cronjobs, small jobs. Que podem morrer antes do prometheus identiticar e fazer scrap, assim o métrica é enviada para o push gateway.

Pod/container -> Push metric > Push Gateway > /metrics <- Prometheus scraping

## Service Discovery

Discovery targets in dynamic environment.
Discover Targets

## Alert Manager

Retrieve alerts from Prometheus Server. Prometheus Server will push alerts to Alert Manager.
Alert Manager push alert to external tools like slack, email, pagerduty.

## Metric Types

### Counter
Tipo de métrica que representa uma contagem crescente. Só pode aumentar ou resetar ou reiniciar o server prometheus.
Exemplo: Número de requests servidas. Tarefas completadas, ou quantidade de erros.

### Gauges
Numeric format.  Usado para mensuarar uso de cpu, temperatura, etc. Representa o status em determinado horário.
http_requests_active 3000 04:00
memory_allocated_bytes 2.143e+06 @02:00

memory_allocated_bytes = Gauge Value
http_requests_total = Rate of Change

### Histogram


### Summary
Usada para calcular quantidades em uma fatia de tempo. Usado para verificar a distribuicao de valores incluindo quantidades (percentis) e soma de metricas observadas.

Count - Número de vezes que um evento ocorreu - basename_count
Sum - Tamanho total dos eventos - basename_sum
Quantiles, Contagem, Quantil - Valores que representam percentis especifgicos (median, percentile).

HTTP Request Latency Summary Metric:
#HELP http_request_duration_seconds Summary of HTTP request duration in seconds
#TYPE http_request_duration_seconds summary
http_request_duration_seconds{quantile="0.5"} 0.043 -> 50% das requests serviram em 0.043 segundos ou menos
http_request_duration_seconds{quantile="0.9"} 0.056 -> 90% das requests serviram em 0.056 segundos ou menos
http_request_duration_seconds{quantile="0.99"} 0.072 -> 99% das requests serviram em 0.072 segundos ou menos
Obs.:
  http_request_duration_seconds is the summary metric for HTTP request latencies
  Quantile labels represent different percentiles.

http_request_duration_seconds_sum 10.862 -> Tempo total em segundos para servir todas as requests
http_request_duration_seconds_count 2433 -> Total de requests servidas

Média em 5m: rate(http_request_duration_seconds_sum[5m]) / rate(http_request_duration_seconds_count[5m])
Total no período: increase(http_request_duration_seconds_sum[5m])
Se você precisa agregar entre pods/instâncias (SLOs globais, percentis por serviço), prefira Histogram (ou native histogram), que permite compor percentis a partir dos buckets.

### Summary

Parecido com Summary, porém calcula a distribuição de valores em buckets (baldes), assim podemos ter um insight em como os dados estão distribuídos em vários ranges de valores. Especialmente em tempos de resposta, por exemplo.

Count - The number of observations in each bucket.
Sum - The sum of all observed values in each bucket.
Bucket values - Upper bounds of each bucket's range.

Exemplo de buckets em tempo de resposta de uma api
0.001 to 0.01 second
0.01 second to 0.02
0.02 second to 0.05
and so on.

Example: Database Query Response Time Histogram Metric
#HELP db_query_response_time_seconds Histogram of database query response times in seconds
#TYPE db_query_response_time_seconds histogram

db_query_response_time_seconds_bucket{le="0.1"} 15
db_query_response_time_seconds_bucket{le="1"} 120
db_query_response_time_seconds_bucket{le="10"} 400
db_query_response_time_seconds_sum 278.53
db_query_response_time_seconds_count 550