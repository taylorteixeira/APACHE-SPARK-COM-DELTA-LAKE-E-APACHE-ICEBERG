# Referência da API

Esta seção fornece uma referência completa da API do Apache Spark para trabalhar com Delta Lake e Iceberg.

## Configuração do SparkSession

### Delta Lake

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .appName("DeltaLakeExample") \
    .config("spark.jars.packages", "io.delta:delta-core_2.12:3.2.0") \
    .config("spark.sql.extensions", "io.delta.sql.DeltaSparkSessionExtension") \
    .config("spark.sql.catalog.spark_catalog", "org.apache.spark.sql.delta.catalog.DeltaCatalog") \
    .getOrCreate()
```

### Iceberg

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .appName("IcebergExample") \
    .config("spark.jars.packages", "org.apache.iceberg:iceberg-spark-runtime-3.5_2.12:1.6.1") \
    .config("spark.sql.extensions", "org.apache.iceberg.spark.extensions.IcebergSparkSessionExtensions") \
    .config("spark.sql.catalog.local", "org.apache.iceberg.spark.SparkCatalog") \
    .config("spark.sql.catalog.local.type", "hadoop") \
    .config("spark.sql.catalog.local.warehouse", "file:///tmp/warehouse") \
    .getOrCreate()
```

## Operações com DataFrames

### Criação de DataFrames

```python
# A partir de uma lista de dicionários
data = [
    {"id": 1, "nome": "João", "idade": 30},
    {"id": 2, "nome": "Maria", "idade": 25}
]
df = spark.createDataFrame(data)

# A partir de um arquivo
df = spark.read.format("csv") \
    .option("header", "true") \
    .load("caminho/arquivo.csv")
```

### Operações Básicas

```python
# Seleção de colunas
df.select("coluna1", "coluna2")

# Filtro
df.filter(df.idade > 25)

# Ordenação
df.orderBy(df.idade.desc())

# Agregação
df.groupBy("departamento").agg(
    {"salario": "avg", "idade": "max"}
)
```

## Operações com Delta Lake

### Criação de Tabela

```python
df.write.format("delta") \
    .mode("overwrite") \
    .saveAsTable("nome_tabela")
```

### Atualização

```python
from delta.tables import DeltaTable

deltaTable = DeltaTable.forPath(spark, "caminho/tabela")
deltaTable.update(
    condition="status = 'PENDENTE'",
    set={"status": "'CONCLUIDO'"}
)
```

### Merge

```python
deltaTable.alias("target").merge(
    sourceDF.alias("source"),
    "target.id = source.id"
).whenMatchedUpdate(
    set={"status": "source.status"}
).whenNotMatchedInsert(
    values={
        "id": "source.id",
        "status": "source.status"
    }
).execute()
```

## Operações com Iceberg

### Criação de Tabela

```python
df.write.format("iceberg") \
    .mode("overwrite") \
    .save("local.nome_tabela")
```

### Atualização

```python
spark.sql("""
    UPDATE local.nome_tabela
    SET status = 'CONCLUIDO'
    WHERE id = 1
""")
```

### Merge

```python
spark.sql("""
    MERGE INTO local.nome_tabela target
    USING sourceDF source
    ON target.id = source.id
    WHEN MATCHED THEN
        UPDATE SET status = source.status
    WHEN NOT MATCHED THEN
        INSERT (id, status) VALUES (source.id, source.status)
""")
```

## Funções Úteis

### Delta Lake

```python
# Histórico de versões
deltaTable.history().show()

# Otimização
spark.sql("OPTIMIZE nome_tabela")

# Limpeza
spark.sql("VACUUM nome_tabela RETAIN 168 HOURS")
```

### Iceberg

```python
# Listar snapshots
spark.sql("SELECT * FROM local.nome_tabela.snapshots").show()

# Otimização
spark.sql("""
    CALL local.system.rewrite_data_files(
        table => 'nome_tabela',
        strategy => 'binpack'
    )
""")

# Limpeza
spark.sql("""
    CALL local.system.expire_snapshots(
        table => 'nome_tabela',
        older_than => TIMESTAMP '2024-01-01 00:00:00'
    )
""")
```

## Configurações Avançadas

### Delta Lake

```python
# Configurações de otimização
spark.conf.set("spark.databricks.delta.optimizeWrite.enabled", "true")
spark.conf.set("spark.databricks.delta.autoCompact.enabled", "true")

# Configurações de retenção
spark.conf.set("spark.databricks.delta.retentionDurationCheck.enabled", "false")
spark.conf.set("spark.databricks.delta.vacuum.logging.enabled", "true")
```

### Iceberg

```python
# Configurações de otimização
spark.conf.set("spark.sql.iceberg.handle-timestamp-without-timezone", "true")
spark.conf.set("spark.sql.iceberg.vectorization.enabled", "true")

# Configurações de catálogo
spark.conf.set("spark.sql.catalog.local.cache-enabled", "true")
spark.conf.set("spark.sql.catalog.local.cache.expiration-interval-ms", "300000")
```
