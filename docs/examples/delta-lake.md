# Exemplos com Delta Lake

Nesta seção, vamos explorar exemplos práticos de uso do Delta Lake com Apache Spark.

## Configuração Inicial

```python
from pyspark.sql import SparkSession
from delta import *

# Criando a sessão do Spark com suporte ao Delta Lake
spark = (
    SparkSession
    .builder
    .master("local[*]")
    .config("spark.jars.packages", "io.delta:delta-spark_2.12:3.2.0")
    .config("spark.sql.extensions", "io.delta.sql.DeltaSparkSessionExtension")
    .config("spark.sql.catalog.spark_catalog", "org.apache.spark.sql.delta.catalog.DeltaCatalog")
    .getOrCreate()
)
```

## Criando uma Tabela Delta

```python
# Criando tabela de vendas
spark.sql("""
    CREATE TABLE vendas_delta (
        id_venda INT,
        produto STRING,
        quantidade INT,
        valor_unitario DECIMAL(10,2),
        data_venda DATE,
        status STRING
    ) USING delta
""")
```

## Inserindo Dados

```python
# Inserindo dados iniciais
spark.sql("""
    INSERT INTO vendas_delta (id_venda, produto, quantidade, valor_unitario, data_venda, status)
    VALUES
        (1, 'Notebook', 2, 4500.00, '2024-01-15', 'CONCLUIDO'),
        (2, 'Smartphone', 3, 2500.00, '2024-01-16', 'CONCLUIDO'),
        (3, 'Tablet', 1, 1800.00, '2024-01-17', 'PENDENTE'),
        (4, 'Monitor', 2, 1200.00, '2024-01-18', 'CANCELADO')
""")
```

## Atualizando Dados

```python
# Atualizando status e valor de uma venda
spark.sql("""
    UPDATE vendas_delta
    SET status = 'CONCLUIDO',
        valor_unitario = 1700.00
    WHERE id_venda = 3
""")
```

## Deletando Dados

```python
# Deletando vendas canceladas
spark.sql("""
    DELETE FROM vendas_delta
    WHERE status = 'CANCELADO'
""")
```

## Consultando Dados

```python
# Consultando vendas com valor total
spark.sql("""
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
""").show()
```

## Time Travel

```python
# Acessando versão anterior dos dados
spark.sql("""
    SELECT * FROM vendas_delta VERSION AS OF 0
""").show()
```

## Otimizações

```python
# Compactando arquivos
spark.sql("""
    OPTIMIZE vendas_delta
""")

# Limpando arquivos antigos
spark.sql("""
    VACUUM vendas_delta RETAIN 168 HOURS
""")
```
