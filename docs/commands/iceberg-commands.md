# Comandos do Spark com Apache Iceberg

Esta página detalha os principais comandos usados para trabalhar com o Apache Iceberg no Apache Spark.

## Configuração da Sessão Spark

Para começar a trabalhar com o Apache Iceberg, primeiro é necessário configurar corretamente a sessão Spark:

```python
from pyspark.sql import SparkSession

# Configurando a sessão Spark para trabalhar com Iceberg
spark = SparkSession.builder \
  .appName("IcebergLocalDevelopment") \
  .config('spark.jars.packages', 'org.apache.iceberg:iceberg-spark-runtime-3.5_2.12:1.6.1') \
  .config("spark.sql.extensions", "org.apache.iceberg.spark.extensions.IcebergSparkSessionExtensions") \
  .config("spark.sql.catalog.local", "org.apache.iceberg.spark.SparkCatalog") \
  .config("spark.sql.catalog.local.type", "hadoop") \
  .config("spark.sql.catalog.local.warehouse", "spark-warehouse/iceberg") \
  .getOrCreate()
```

### Explicação das Configurações:

- `appName("IcebergLocalDevelopment")`: Define o nome da aplicação Spark
- `config('spark.jars.packages', ...)`: Adiciona a dependência do Apache Iceberg
- `config("spark.sql.extensions", ...)`: Habilita as extensões SQL do Iceberg
- `config("spark.sql.catalog.local", ...)`: Define o catálogo "local" para usar o SparkCatalog do Iceberg
- `config("spark.sql.catalog.local.type", ...)`: Especifica o tipo de armazenamento como "hadoop"
- `config("spark.sql.catalog.local.warehouse", ...)`: Define o local dos arquivos Iceberg

## Criação de Tabelas

Para criar uma tabela no formato Iceberg:

```sql
CREATE TABLE local.funcionarios_iceberg (
    id_funcionario INT,
    nome STRING,
    cargo STRING,
    departamento STRING,
    salario DECIMAL(10,2),
    data_admissao DATE,
    ativo BOOLEAN
) USING iceberg
```

Observe que é necessário usar o prefixo do catálogo `local.` antes do nome da tabela.

Também é possível criar tabelas a partir de DataFrames:

```python
# Criando tabela a partir de DataFrame
df.writeTo("local.nome_tabela").using("iceberg").create()

# Especificando particionamento
df.writeTo("local.nome_tabela").using("iceberg").partitionedBy("coluna1", "coluna2").create()
```

## Operações CRUD

### Inserção de Dados (INSERT)

```sql
INSERT INTO local.funcionarios_iceberg 
(id_funcionario, nome, cargo, departamento, salario, data_admissao, ativo)
VALUES 
    (1, 'João Silva', 'Analista', 'TI', 5000.00, DATE '2023-01-15', true),
    (2, 'Maria Santos', 'Gerente', 'RH', 8000.00, DATE '2022-06-20', true),
    (3, 'Pedro Oliveira', 'Desenvolvedor', 'TI', 6000.00, DATE '2023-03-10', true),
    (4, 'Ana Costa', 'Assistente', 'Financeiro', 3500.00, DATE '2023-11-05', false)
```

Note o uso de `DATE` antes de literais de data e `true`/`false` para valores booleanos.

### Atualização de Dados (UPDATE)

```sql
UPDATE local.funcionarios_iceberg 
SET cargo = 'Gerente de TI',
    salario = 9000.00
WHERE id_funcionario = 1
```

Utilizando a API Python:

```python
spark.sql("""
    UPDATE local.funcionarios_iceberg 
    SET cargo = 'Gerente de TI',
        salario = 9000.00
    WHERE id_funcionario = 1
""")
```

### Exclusão de Dados (DELETE)

```sql
DELETE FROM local.funcionarios_iceberg 
WHERE ativo = false
```

Utilizando a API Python:

```python
spark.sql("""
    DELETE FROM local.funcionarios_iceberg 
    WHERE ativo = false
""")
```

### Consulta de Dados (SELECT)

```sql
SELECT 
    departamento,
    COUNT(*) as total_funcionarios,
    AVG(salario) as media_salarial,
    MAX(salario) as maior_salario,
    MIN(salario) as menor_salario
FROM local.funcionarios_iceberg
GROUP BY departamento
ORDER BY media_salarial DESC
```

## Recursos Avançados

### Time Travel (Viagem no Tempo)

O Iceberg também permite acessar versões anteriores dos dados:

```sql
-- Consultar dados como estavam em um timestamp específico
SELECT * FROM local.funcionarios_iceberg TIMESTAMP AS OF '2023-01-01 12:00:00'

-- Consultar dados por ID de snapshot
SELECT * FROM local.funcionarios_iceberg VERSION AS OF 123456789
```

### Listar Snapshots

```sql
-- Ver todos os snapshots (versões anteriores)
SELECT * FROM local.funcionarios_iceberg.snapshots
```

### Compactação de Arquivos

```sql
-- Reescrever arquivos pequenos para compactação
CALL local.system.rewrite_data_files(
  table => 'funcionarios_iceberg',
  strategy => 'binpack'
)

-- Reordenar por uma coluna específica
CALL local.system.rewrite_data_files(
  table => 'funcionarios_iceberg',
  strategy => 'sort',
  sort_order => 'data_admissao'
)
```

### Expirar Snapshots Antigos

```sql
-- Remover snapshots mais antigos que um timestamp específico
CALL local.system.expire_snapshots(
  table => 'funcionarios_iceberg',
  older_than => TIMESTAMP '2023-01-01 00:00:00'
)

-- Reter apenas um número específico de snapshots
CALL local.system.expire_snapshots(
  table => 'funcionarios_iceberg',
  retain_last => 5
)
```

### Gerenciamento de Partições

O Iceberg permite evolução de particionamento sem reescrever dados:

```sql
-- Alterar a estratégia de particionamento
ALTER TABLE local.funcionarios_iceberg 
REPLACE PARTITION FIELD departamento
```

## Melhores Práticas

1. **Catálogos**: Sempre use o prefixo do catálogo (ex: `local.`) ao referenciar tabelas Iceberg.

2. **Evolução de Schema**: O Iceberg permite adicionar, renomear ou alterar o tipo de colunas sem reescrever dados.

3. **Particionamento**: Use particionamento para melhorar o desempenho de consultas, aproveitando a capacidade de evolução de partições do Iceberg.

4. **Snapshots**: Mantenha um número adequado de snapshots para permitir time travel, mas expire os mais antigos para economizar espaço.

5. **Compactação**: Execute operações de compactação periodicamente para melhorar o desempenho das consultas.