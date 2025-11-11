### Metrica{Labels}
up{team="gmaas-team"}

### Filter Matchers/Selectors
<metric name> {filter_key=vale, filter_key=value,... }

, = AND
= -> Two values must be equal
!= -> Two values must NOT be equal
=~ Value on left must match. the Regular Expression (regex) on right
!~ Value on left must NOT match the Regular Expression (regex) on right
 

 ### Exemplos:

### Total de requests com status http 200 e job prometheus
```
prometheus_http_requests_total{code=200, job="prometheus"}
```

### Total de requests com status http 200, job prometheus e handler=/api/v1/status/config
```
prometheus_http_requests_total{code="200", job="prometheus", handler="/api/v1/status/config"}
```

### Total de requests com status http 2xx, job prometheus
```
prometheus_http_requests_total{code=~"2.*", job="prometheus"}
```

### Total de requests com status http 5xx, job prometheus
``` 
prometheus_http_requests_total{code=~"5.*", job="prometheus"}
```

### Aggregations Operators
Agrega os elementos de um unico vetor.
O resultado [e um novo vetor com os valores agregados.]

sum -> Calculates sum over dimensions
min -> Selects minimum over dimensions
max -> Selects maximum over dimensions
avg -> Selects average over dimensions
count -> Selects number of elements over dimensions
group -> Group elements. All values in resulting vector are equal to 1.
count_values -> Counts the number of elements with the same values
topk -> Largest elements by sample value
bottomk -> Smallest elements by sample value
stddev -> Finds population standard deviation over dimensions
stdvar -> Finds population standard variation over dimensions

<Aggregation Operator> (<Instant Vector>)
sum(node_cpu_total)

<Aggregation Operator> (<Instant Vector>) by (<label list>) 
sum(node_cpu_total) by (http_code)

<Aggregation Operator> (<Instant Vector>) without (<label list>)
sum(node_cpu_total) without (http_code)

 
### Soma agrupada por label:
```
sum(node_cpu_seconds_total) by (mode)
```

### Soma das metricas onde nao tem a label mode:
```
sum(node_cpu_seconds_total) without (mode)
```

### Top 3 maiores valores:
```
topk(3, sum(node_cpu_seconds_total) without (mode))
```

### 3 Menores valores:
```
bottomk(3, sum(node_cpu_seconds_total) without (mode))
```

### Media de cpu agrupada por mode:
```
avg(node_cpu_seconds_total) by (mode)
```

## Offset -> Filtro por periodo, tempo

### Gera um vetor de amostragem de como estava essa metrica exatamente 30 minutos atras
```
prometheus_http_requests_total{handler="/api/v1/notifications/live"} offset 30m
```

## Range

### Gera uma amostragem de todos os valores coletados nos ultimos 30 minutos, mas perde as funcones de agregacao como:
rate(), increase(), avg_over_time(), max_over_time()
```
prometheus_http_requests_total{handler="/api/v1/notifications/live"}[30m]
```

## Outros exemplos

### Total de requests agrupados por status code http
```
group(prometheus_http_requests_total) by (code)
```

### Media de requests por status http nos ultimos 30 minutos
```
avg(prometheus_http_requests_total) by (code)
```

### Media de requests por status http nas 8h atras
```
avg(prometheus_http_requests_total offset 30m) by (code)
```


## Functions
Serve para vericicar se alguma metrica parou de ser enviada para o prometheus.

### absent
```
absent(node_cpu_seconds_total{cpu="0", mode="idlaw"})
```
Essa retorna=1, por que de fato nao existe, o que temos e mode="idle".

### absent_over_time
```
absent_over_time(node_cpu_seconds_total{cpu="0", mode="idlaw"}[30m])
``` 

### clamp
Retorna valores em um intervalo.
```
clamp(node_cpu_seconds_total, 3584, 5915)
```

### clamp_max e clamp_min
Retorna as metricas com valores em determinado campo ate determinado valor de teto, ou a partir desse valor (piso).
```
clamp_min(node_cpu_seconds_total, 152522.46)
```

### abs 
Converte os valores para seu valor absoluto.
Exemplo: -5 to 5.

### ceil
Arredonda um float para o inteiro mais proximo.
Exemplo: 1.6 -> 2.

### floor
Corta as casas decimais.
1.6 -> 1, 2.55 -> 2. 

### day_of_month
Retorna o dia do mês (1–31) do timestamp da amostra.
```
day_of_month(time()) → 7 (se hoje é dia 7).
```

### day_of_week
Retorna o dia da semana (0–6), onde 0 = domingo.
```
day_of_week(time()) → 2 (se hoje é terça).
```

### delta
Calcula a diferença entre o primeiro e o último valor de uma métrica num intervalo.
```
delta(node_memory_Active_bytes[5m]) → quanto a memória mudou em 5 min.
```

### idelta
Igual ao delta(), mas usa os valores instantâneos, sem corrigir por intervalo irregular (sem interpolação).
```
idelta(node_network_receive_bytes_total[1m]) → bytes recebidos no último minuto, direto da diferença bruta.
```

### sort
sort(<instant vector>) Sorts elements in ascending order

### sort_desc
sort_desc(<instant vector>) Sorts elements in ascending order time()

### timeseries
timeseries(<instant vector>) Return the timestamp of each time series


## Aggreations over time

### avg_over_time(<Range vetor>) 
Return average of itens in a range vector

### sum_over_time(<range vecotr>)
Return sum of itens in a range vector

### min_over_time(<range vector>)
Return min item value in a range vector

### max_over_time(<range vector>)
Return the maximum of itens in a range vector

### count_over_time(<range vector>)
Return the count of itens in a range vector

node_cpu_seconds_total -> Is a instant vector

````
avg_over_time(node_cpu_seconds_total[5h])
```