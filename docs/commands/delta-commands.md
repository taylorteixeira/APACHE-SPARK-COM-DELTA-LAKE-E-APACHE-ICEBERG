# Comandos do Spark com Delta Lake

Esta página detalha os principais comandos usados para trabalhar com o Delta Lake no Apache Spark.

## Configuração da Sessão Spark

Para começar a trabalhar com o Delta Lake, você precisa configurar sua sessão Spark corretamente:

```python
from pyspark.sql import SparkSession
from delta import *

# Configurando a sessão Spark para trabalhar com Delta Lake
spark = (
    SparkSession
    .builder
    .master("local[*]")  # Para execução local
    .config("spark.jars.packages", "io.delta:delta-spark_2.12:3.2.0")
    .config("spark.sql.extensions", "io.delta.sql.DeltaSparkSessionExtension")
    .config("spark.sql.catalog.spark_catalog", "org.apache.spark.sql.delta.catalog.DeltaCatalog")
    .getOrCreate()
)
```

### Explicação:

- `master("local[*]")`: Define que o Spark rodará em modo local usando todos os núcleos disponíveis
- `config("spark.jars.packages", ...)`: Adiciona a dependência do Delta Lake
- `config("spark.sql.extensions", ...)`: Habilita as extensões SQL do Delta Lake
- `config("spark.sql.catalog.spark_catalog", ...)`: Configura o catálogo Spark para utilizar o Delta Lake

## Criação de Tabelas

Para criar uma tabela no formato Delta:

```sql
CREATE TABLE vendas_delta (
    id_venda INT,
    produto STRING,
    quantidade INT,
    valor_unitario DECIMAL(10,2),
    data_venda DATE,
    status STRING
) USING delta
```

Também é possível criar tabelas a partir de DataFrames:

```python
# Criando tabela a partir de DataFrame
df.write.format("delta").saveAsTable("nome_da_tabela")

# Especificando particionamento
df.write.format("delta").partitionBy("coluna1", "coluna2").saveAsTable("nome_da_tabela")
```

## Operações CRUD

### Inserção de Dados (INSERT)

```sql
INSERT INTO vendas_delta (id_venda, produto, quantidade, valor_unitario, data_venda, status)
VALUES 
    (1, 'Notebook', 2, 4500.00, '2024-01-15', 'CONCLUIDO'),
    (2, 'Smartphone', 3, 2500.00, '2024-01-16', 'CONCLUIDO'),
    (3, 'Tablet', 1, 1800.00, '2024-01-17', 'PENDENTE'),
    (4, 'Monitor', 2, 1200.00, '2024-01-18', 'CANCELADO')
```

### Atualização de Dados (UPDATE)

```sql
UPDATE vendas_delta 
SET status = 'CONCLUIDO',
    valor_unitario = 1700.00
WHERE id_venda = 3
```

Utilizando a API Python:

```python
from delta.tables import DeltaTable

deltaTable = DeltaTable.forPath(spark, "caminho/para/vendas_delta")
deltaTable.update(
    condition="id_venda = 3",
    set={
        "status": "'CONCLUIDO'",
        "valor_unitario": "1700.00"
    }
)
```

### Exclusão de Dados (DELETE)

```sql
DELETE FROM vendas_delta 
WHERE status = 'CANCELADO'
```

Utilizando a API Python:

```python
deltaTable.delete("status = 'CANCELADO'")
```

### Consulta de Dados (SELECT)

```sql
SELECT 
    id_venda,
    produto,
    quantidade,
    valor_unitario,
    quantidade * valor_unitario as valor_total,
    data_venda,
    status
FROM vendas_delta
ORDER BY data_venda
```

## Recursos Avançados

### Time Travel (Viagem no Tempo)

O Delta Lake permite acessar versões anteriores dos dados:

```sql
-- Consultar dados como estavam em um timestamp específico
SELECT * FROM vendas_delta TIMESTAMP AS OF '2023-01-01 12:00:00'

-- Consultar dados por número de versão
SELECT * FROM vendas_delta VERSION AS OF 0
```

### Otimização de Arquivos

```sql
-- Compactar arquivos pequenos
OPTIMIZE vendas_delta

-- Compactar e ordenar por colunas específicas
OPTIMIZE vendas_delta ZORDER BY (data_venda)
```

### Limpeza de Arquivos Antigos

```sql
-- Limpar arquivos que não são mais referenciados por versões mais recentes
VACUUM vendas_delta

-- Limpar arquivos mais antigos que 168 horas (7 dias)
VACUUM vendas_delta RETAIN 168 HOURS
```

### Histórico de Alterações

```sql
-- Visualizar histórico de alterações da tabela
DESCRIBE HISTORY vendas_delta
```

## Melhores Práticas

1. **Particionamento**: Particione os dados por colunas frequentemente usadas em filtros para melhorar o desempenho das consultas.

2. **Z-Ordering**: Use Z-Ordering para co-localizar dados relacionados em arquivos, melhorando a eficiência das consultas.

3. **Vacuum Regular**: Execute VACUUM periodicamente para limpar arquivos antigos e otimizar o espaço em disco.

4. **Checkpoints**: O Delta Lake cria checkpoints automaticamente, mas você pode forçar a criação para melhorar o desempenho.

5. **Evolution de Schema**: Aproveite a capacidade do Delta Lake de evoluir o schema de forma controlada, adicionando ou alterando colunas quando necessário.