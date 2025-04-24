# Exemplos com Iceberg

Nesta seção, vamos explorar exemplos práticos de uso do Iceberg com Apache Spark.

## Configuração Inicial

```python
from pyspark.sql import SparkSession

# Inicializando a sessão do Spark com configurações do Iceberg
spark = SparkSession.builder \
    .appName("IcebergLocalDevelopment") \
    .config('spark.jars.packages', 'org.apache.iceberg:iceberg-spark-runtime-3.5_2.12:1.6.1') \
    .config("spark.sql.extensions", "org.apache.iceberg.spark.extensions.IcebergSparkSessionExtensions") \
    .config("spark.sql.catalog.local", "org.apache.iceberg.spark.SparkCatalog") \
    .config("spark.sql.catalog.local.type", "hadoop") \
    .config("spark.sql.catalog.local.warehouse", "spark-warehouse/iceberg") \
    .getOrCreate()
```

## Criando uma Tabela Iceberg

```python
# Criando tabela de funcionários
spark.sql("""
    CREATE TABLE local.funcionarios_iceberg (
        id_funcionario INT,
        nome STRING,
        cargo STRING,
        departamento STRING,
        salario DECIMAL(10,2),
        data_admissao DATE,
        ativo BOOLEAN
    ) USING iceberg
""")
```

## Inserindo Dados

```python
# Inserindo dados iniciais
spark.sql("""
    INSERT INTO local.funcionarios_iceberg
    (id_funcionario, nome, cargo, departamento, salario, data_admissao, ativo)
    VALUES
        (1, 'João Silva', 'Analista', 'TI', 5000.00, '2023-01-15', true),
        (2, 'Maria Santos', 'Gerente', 'RH', 8000.00, '2022-06-20', true),
        (3, 'Pedro Oliveira', 'Desenvolvedor', 'TI', 6000.00, '2023-03-10', true),
        (4, 'Ana Costa', 'Assistente', 'Financeiro', 3500.00, '2023-11-05', false)
""")
```

## Atualizando Dados

```python
# Promovendo funcionário e ajustando salário
spark.sql("""
    UPDATE local.funcionarios_iceberg
    SET cargo = 'Gerente de TI',
        salario = 9000.00
    WHERE id_funcionario = 1
""")
```

## Deletando Dados

```python
# Removendo funcionários inativos
spark.sql("""
    DELETE FROM local.funcionarios_iceberg
    WHERE ativo = false
""")
```

## Consultando Dados

```python
# Consultando funcionários por departamento
spark.sql("""
    SELECT
        departamento,
        COUNT(*) as total_funcionarios,
        AVG(salario) as media_salarial,
        MAX(salario) as maior_salario,
        MIN(salario) as menor_salario
    FROM local.funcionarios_iceberg
    GROUP BY departamento
    ORDER BY media_salarial DESC
""").show()
```

## Time Travel

```python
# Acessando snapshot anterior
spark.sql("""
    SELECT * FROM local.funcionarios_iceberg TIMESTAMP AS OF '2024-01-01 00:00:00'
""").show()
```

## Otimizações

```python
# Compactando arquivos
spark.sql("""
    CALL local.system.rewrite_data_files(
        table => 'funcionarios_iceberg',
        strategy => 'binpack'
    )
""")

# Limpando snapshots antigos
spark.sql("""
    CALL local.system.expire_snapshots(
        table => 'funcionarios_iceberg',
        older_than => TIMESTAMP '2024-01-01 00:00:00'
    )
""")
```
