# Apache Spark com Delta Lake e Iceberg

Bem-vindo à documentação completa sobre Apache Spark com formatos de tabela Delta Lake e Apache Iceberg. Esta documentação fornece informações detalhadas, exemplos práticos e explicações sobre como utilizar estas tecnologias para processamento de dados em larga escala.

<div class="admonition info">
<p class="admonition-title">Novo Conteúdo!</p>
Adicionamos explicações detalhadas sobre comandos Spark com Delta Lake e Iceberg, além de um modelo ER para visualizar as relações entre os dados.
</div>

<div class="card-grid">
    <div class="card">
        <h3><span class="badge badge-spark">Apache Spark</span> Processamento Distribuído</h3>
        <p>Framework de processamento distribuído para análise de big data com APIs em Scala, Java, Python e R.</p>
        <a href="intro/spark/">Saiba mais →</a>
    </div>
    <div class="card">
        <h3><span class="badge badge-delta">Delta Lake</span> Transações ACID</h3>
        <p>Formato de armazenamento que adiciona transações ACID, viagem no tempo e evolução de schema para seu data lake.</p>
        <a href="intro/delta-lake/">Saiba mais →</a>
    </div>
    <div class="card">
        <h3><span class="badge badge-iceberg">Apache Iceberg</span> Alto Desempenho</h3>
        <p>Formato de tabela para grandes conjuntos de dados analíticos com controle de metadados e evolução de schema.</p>
        <a href="intro/iceberg/">Saiba mais →</a>
    </div>
</div>

## Comparação entre Delta Lake e Iceberg

Ambos os formatos trazem várias melhorias sobre formatos tradicionais como Parquet, mas têm abordagens diferentes:

| Recurso | Delta Lake | Apache Iceberg |
|---------|------------|---------------|
| **Transações ACID** | ✅ | ✅ |
| **Time Travel** | ✅ | ✅ |
| **Schema Evolution** | ✅ | ✅ |
| **Referência à tabela** | `tabela` | `catalogo.tabela` |
| **Particionamento oculto** | ❌ | ✅ |
| **Otimização de leitura** | Z-Order | Posicional (oculto) |

## Começando Rapidamente

### Exemplo com Delta Lake

```python
# Configuração Delta Lake
spark = SparkSession.builder \
    .config("spark.jars.packages", "io.delta:delta-spark_2.12:3.2.0") \
    .config("spark.sql.extensions", "io.delta.sql.DeltaSparkSessionExtension") \
    .getOrCreate()

# Criação e consulta
spark.sql("CREATE TABLE vendas_delta (...) USING delta")
spark.sql("SELECT * FROM vendas_delta").show()
```

### Exemplo com Iceberg

```python
# Configuração Iceberg
spark = SparkSession.builder \
    .config("spark.jars.packages", "org.apache.iceberg:iceberg-spark-runtime-3.5_2.12:1.6.1") \
    .config("spark.sql.extensions", "org.apache.iceberg.spark.extensions.IcebergSparkSessionExtensions") \
    .getOrCreate()

# Criação e consulta
spark.sql("CREATE TABLE local.vendas_iceberg (...) USING iceberg")
spark.sql("SELECT * FROM local.vendas_iceberg").show()
```

## Conteúdo da Documentação

- **Introdução**: Conceitos básicos sobre Apache Spark, Delta Lake e Iceberg
- **Operações CRUD**: Exemplos detalhados de operações Create, Read, Update e Delete
- **Explicação de Comandos**: Análise detalhada dos comandos utilizados nos notebooks
- **Modelo de Dados**: Diagrama ER e relações entre as entidades
- **Exemplos Práticos**: Casos de uso completos com código
- **Referência**: Comandos SQL e APIs para consulta rápida
