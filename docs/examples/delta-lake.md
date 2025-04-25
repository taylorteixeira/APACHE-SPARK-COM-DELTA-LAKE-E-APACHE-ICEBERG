# Exemplos com Delta Lake

Este documento apresenta exemplos práticos de uso do Delta Lake com Apache Spark.

## Configuração Inicial

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import StructType, StructField, StringType, FloatType
from delta import *

# Criação da SparkSession com configurações do Delta Lake
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

## Criação de Tabela

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

Este comando cria uma tabela Delta Lake com os seguintes campos:

- `id_venda`: Identificador único da venda
- `produto`: Nome do produto vendido
- `quantidade`: Quantidade de itens vendidos
- `valor_unitario`: Preço unitário do produto
- `data_venda`: Data em que a venda foi realizada
- `status`: Status atual da venda (CONCLUIDO, PENDENTE, CANCELADO)

## Inserção de Dados

```sql
INSERT INTO vendas_delta (id_venda, produto, quantidade, valor_unitario, data_venda, status)
VALUES
    (1, 'Notebook', 2, 4500.00, '2024-01-15', 'CONCLUIDO'),
    (2, 'Smartphone', 3, 2500.00, '2024-01-16', 'CONCLUIDO'),
    (3, 'Tablet', 1, 1800.00, '2024-01-17', 'PENDENTE'),
    (4, 'Monitor', 2, 1200.00, '2024-01-18', 'CANCELADO')
```

## Atualização de Dados

```sql
UPDATE vendas_delta
SET status = 'CONCLUIDO',
    valor_unitario = 1700.00
WHERE id_venda = 3
```

Este comando demonstra a capacidade do Delta Lake de realizar atualizações ACID (Atomicidade, Consistência, Isolamento e Durabilidade).

## Deleção de Dados

```sql
DELETE FROM vendas_delta
WHERE status = 'CANCELADO'
```

## Consultas

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

Esta consulta demonstra:

- Cálculo de valores totais
- Ordenação por data
- Seleção de múltiplos campos

## Benefícios do Delta Lake

1. **ACID Transactions**: Garante a integridade dos dados durante operações de escrita
2. **Time Travel**: Permite acessar versões anteriores dos dados
3. **Schema Evolution**: Suporte a evolução do esquema de dados
4. **Audit History**: Mantém histórico de todas as alterações
5. **Optimized Performance**: Otimizações automáticas para melhor performance
