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

