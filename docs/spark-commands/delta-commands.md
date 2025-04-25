# Comandos do Spark com Delta Lake

<span class="badge badge-delta">Delta Lake</span>

O Delta Lake é uma camada de armazenamento de código aberto que traz transações ACID para processamento de dados em grande escala com o Apache Spark. Esta página detalha os comandos e códigos usados no notebook `spark-delta.ipynb`.

## Configuração do Ambiente Delta Lake

Para utilizar o Delta Lake com Spark, é necessário configurar corretamente a sessão Spark:

```python
import os
# Configurando o ambiente Hadoop para Windows
os.environ['HADOOP_HOME'] = 'C:\\hadoop'
os.environ['PATH'] += os.pathsep + os.path.join(os.environ['HADOOP_HOME'], 'bin')

from pyspark.sql import SparkSession
from pyspark.sql.types import StructType, StructField, StringType, FloatType
from delta import *

# Criando a sessão Spark com suporte ao Delta Lake
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

### Explicação da Configuração

| Configuração | Descrição |
|-------------|-----------|
| `master("local[*]")` | Define execução local usando todos os núcleos disponíveis |
| `spark.jars.packages` | Adiciona a dependência do Delta Lake |
| `spark.sql.extensions` | Habilita as extensões SQL específicas do Delta Lake |
| `spark.sql.catalog.spark_catalog` | Configura o catálogo Spark para usar o Delta Lake |

## Operações CRUD no Delta Lake

### 1. Criando uma Tabela (CREATE)

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

Este comando cria uma tabela no formato Delta com a estrutura especificada. O parâmetro `USING delta` é fundamental para indicar que queremos usar o formato Delta Lake.

### 2. Inserindo Dados (INSERT)

```sql
INSERT INTO vendas_delta (id_venda, produto, quantidade, valor_unitario, data_venda, status)
VALUES 
    (1, 'Notebook', 2, 4500.00, '2024-01-15', 'CONCLUIDO'),
    (2, 'Smartphone', 3, 2500.00, '2024-01-16', 'CONCLUIDO'),
    (3, 'Tablet', 1, 1800.00, '2024-01-17', 'PENDENTE'),
    (4, 'Monitor', 2, 1200.00, '2024-01-18', 'CANCELADO')
```

O comando INSERT no Delta Lake permite inserir múltiplas linhas em uma única operação, tornando o processo mais eficiente para cargas em lote.

### 3. Atualizando Dados (UPDATE)

```sql
UPDATE vendas_delta 
SET status = 'CONCLUIDO',
    valor_unitario = 1700.00
WHERE id_venda = 3
```

O Delta Lake suporta atualizações parciais com a sintaxe SQL padrão. Neste exemplo, atualizamos o status e o valor unitário de uma venda específica.

#### Vantagens do UPDATE no Delta Lake:

- Transações ACID garantem consistência
- Suporte a condições complexas
- Capacidade de atualizar múltiplas colunas de uma vez
- Histórico completo de alterações

### 4. Excluindo Dados (DELETE)

```sql
DELETE FROM vendas_delta 
WHERE status = 'CANCELADO'
```

O comando DELETE permite remover registros que atendem a uma condição específica, mantendo a integridade transacional.

### 5. Consultando Dados (SELECT)

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

O Delta Lake suporta todas as operações SQL padrão do Spark, incluindo cálculos em colunas, ordenação, filtragem e agregações.

## Recursos Avançados do Delta Lake

### Time Travel

O Delta Lake permite acessar versões anteriores dos dados:

```sql
-- Consultando uma versão específica
SELECT * FROM vendas_delta VERSION AS OF 1

-- Consultando dados em um timestamp específico
SELECT * FROM vendas_delta TIMESTAMP AS OF '2024-01-15 12:00:00'
```

### Otimização de Arquivos

```sql
-- Compactar arquivos pequenos
OPTIMIZE vendas_delta

-- Compactar e ordenar por coluna específica
OPTIMIZE vendas_delta ZORDER BY (data_venda)
```

### Limpeza de Arquivos Antigos

```sql
-- Limpar arquivos antigos (retém por 7 dias por padrão)
VACUUM vendas_delta

-- Limpar arquivos mais antigos que 3 dias
VACUUM vendas_delta RETAIN 72 HOURS
```

### Histórico de Alterações

```sql
-- Visualizar histórico de alterações
DESCRIBE HISTORY vendas_delta
```

## Melhores Práticas para Delta Lake

1. **Utilize particionamento** para grandes conjuntos de dados:
   ```sql
   CREATE TABLE vendas_delta (...)
   USING delta
   PARTITIONED BY (data_venda)
   ```

2. **Z-Order** para otimizar consultas em colunas frequentemente filtradas.

3. **Vacuum periódico** para gerenciar o tamanho do armazenamento.

4. **Validação de schema** para garantir a consistência dos dados.

5. **Time travel** para debugging e auditoria de dados.