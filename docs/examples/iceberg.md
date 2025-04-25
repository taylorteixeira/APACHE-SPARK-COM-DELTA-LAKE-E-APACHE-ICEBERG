# Exemplos com Apache Iceberg

Este documento apresenta exemplos práticos de uso do Apache Iceberg com Apache Spark.

## Configuração Inicial

```python
from pyspark.sql import SparkSession

# Inicialização da SparkSession com configurações do Iceberg
spark = SparkSession.builder \
    .appName("IcebergLocalDevelopment") \
    .config('spark.jars.packages', 'org.apache.iceberg:iceberg-spark-runtime-3.5_2.12:1.6.1') \
    .config("spark.sql.extensions", "org.apache.iceberg.spark.extensions.IcebergSparkSessionExtensions") \
    .config("spark.sql.catalog.local", "org.apache.iceberg.spark.SparkCatalog") \
    .config("spark.sql.catalog.local.type", "hadoop") \
    .config("spark.sql.catalog.local.warehouse", "spark-warehouse/iceberg") \
    .getOrCreate()
```

## Criação de Tabela

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

Este comando cria uma tabela Iceberg com os seguintes campos:

- `id_funcionario`: Identificador único do funcionário
- `nome`: Nome completo do funcionário
- `cargo`: Cargo atual do funcionário
- `departamento`: Departamento ao qual o funcionário pertence
- `salario`: Salário atual do funcionário
- `data_admissao`: Data em que o funcionário foi admitido
- `ativo`: Indica se o funcionário está ativo na empresa

## Inserção de Dados

```sql
INSERT INTO local.funcionarios_iceberg
(id_funcionario, nome, cargo, departamento, salario, data_admissao, ativo)
VALUES
    (1, 'João Silva', 'Analista', 'TI', 5000.00, DATE '2023-01-15', true),
    (2, 'Maria Santos', 'Gerente', 'RH', 8000.00, DATE '2022-06-20', true),
    (3, 'Pedro Oliveira', 'Desenvolvedor', 'TI', 6000.00, DATE '2023-03-10', true),
    (4, 'Ana Costa', 'Assistente', 'Financeiro', 3500.00, DATE '2023-11-05', false)
```

## Atualização de Dados

```sql
UPDATE local.funcionarios_iceberg
SET cargo = 'Gerente de TI',
    salario = 9000.00
WHERE id_funcionario = 1
```

Este comando demonstra a capacidade do Iceberg de realizar atualizações atômicas.

## Deleção de Dados

```sql
DELETE FROM local.funcionarios_iceberg
WHERE ativo = false
```

## Consultas Analíticas

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

Esta consulta demonstra:

- Agregações por departamento
- Cálculo de estatísticas salariais
- Ordenação por média salarial

## Benefícios do Iceberg

1. **Schema Evolution**: Suporte robusto a evolução de esquema
2. **Partition Evolution**: Permite alterar a estratégia de particionamento
3. **Hidden Partitioning**: Particionamento automático e otimizado
4. **Time Travel**: Acesso a snapshots históricos dos dados
5. **Optimized Planning**: Otimizações automáticas de consultas
6. **Multi-table Transactions**: Suporte a transações entre múltiplas tabelas
