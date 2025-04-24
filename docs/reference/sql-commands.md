# Referência de Comandos SQL

Esta seção fornece uma referência completa dos comandos SQL suportados pelo Apache Spark com Delta Lake e Iceberg.

## Comandos Básicos

### CREATE TABLE

#### Delta Lake

```sql
CREATE TABLE nome_tabela (
    coluna1 TIPO1,
    coluna2 TIPO2,
    ...
) USING delta
[PARTITIONED BY (coluna1, coluna2, ...)]
[LOCATION 'caminho']
```

#### Iceberg

```sql
CREATE TABLE catalogo.nome_tabela (
    coluna1 TIPO1,
    coluna2 TIPO2,
    ...
) USING iceberg
[PARTITIONED BY (coluna1, coluna2, ...)]
[LOCATION 'caminho']
```

### INSERT

#### Delta Lake

```sql
INSERT INTO nome_tabela (coluna1, coluna2, ...)
VALUES (valor1, valor2, ...)
```

#### Iceberg

```sql
INSERT INTO catalogo.nome_tabela (coluna1, coluna2, ...)
VALUES (valor1, valor2, ...)
```

### UPDATE

#### Delta Lake

```sql
UPDATE nome_tabela
SET coluna1 = valor1,
    coluna2 = valor2
WHERE condição
```

#### Iceberg

```sql
UPDATE catalogo.nome_tabela
SET coluna1 = valor1,
    coluna2 = valor2
WHERE condição
```

### DELETE

#### Delta Lake

```sql
DELETE FROM nome_tabela
WHERE condição
```

#### Iceberg

```sql
DELETE FROM catalogo.nome_tabela
WHERE condição
```

## Comandos Avançados

### Time Travel

#### Delta Lake

```sql
-- Versão específica
SELECT * FROM nome_tabela VERSION AS OF versão

-- Timestamp específico
SELECT * FROM nome_tabela TIMESTAMP AS OF '2024-01-01 00:00:00'
```

#### Iceberg

```sql
-- Snapshot específico
SELECT * FROM catalogo.nome_tabela VERSION AS OF snapshot_id

-- Timestamp específico
SELECT * FROM catalogo.nome_tabela TIMESTAMP AS OF '2024-01-01 00:00:00'
```

### Otimização

#### Delta Lake

```sql
-- Compactar arquivos
OPTIMIZE nome_tabela

-- Limpar arquivos antigos
VACUUM nome_tabela [RETAIN número HOURS]
```

#### Iceberg

```sql
-- Compactar arquivos
CALL catalogo.system.rewrite_data_files(
    table => 'nome_tabela',
    strategy => 'binpack'
)

-- Limpar snapshots antigos
CALL catalogo.system.expire_snapshots(
    table => 'nome_tabela',
    older_than => TIMESTAMP '2024-01-01 00:00:00'
)
```

## Funções SQL

### Funções de Agregação

```sql
COUNT(*)
SUM(coluna)
AVG(coluna)
MIN(coluna)
MAX(coluna)
```

### Funções de Data

```sql
CURRENT_DATE()
CURRENT_TIMESTAMP()
DATE_ADD(data, dias)
DATE_SUB(data, dias)
DATEDIFF(data1, data2)
```

### Funções de String

```sql
CONCAT(string1, string2)
SUBSTRING(string, inicio, tamanho)
UPPER(string)
LOWER(string)
TRIM(string)
```

## Exemplos Práticos

### Criação de Tabela com Partições

```sql
-- Delta Lake
CREATE TABLE vendas_delta (
    id_venda INT,
    produto STRING,
    quantidade INT,
    valor_unitario DECIMAL(10,2),
    data_venda DATE,
    status STRING
) USING delta
PARTITIONED BY (data_venda, status)

-- Iceberg
CREATE TABLE local.vendas_iceberg (
    id_venda INT,
    produto STRING,
    quantidade INT,
    valor_unitario DECIMAL(10,2),
    data_venda DATE,
    status STRING
) USING iceberg
PARTITIONED BY (data_venda, status)
```

### Consulta com Agregações

```sql
SELECT
    data_venda,
    status,
    COUNT(*) as total_vendas,
    SUM(quantidade * valor_unitario) as valor_total,
    AVG(valor_unitario) as valor_medio
FROM vendas_delta
GROUP BY data_venda, status
ORDER BY data_venda DESC
```
