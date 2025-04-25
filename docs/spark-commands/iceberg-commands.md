# Comandos do Spark com Apache Iceberg

<span class="badge badge-iceberg">Apache Iceberg</span>

O Apache Iceberg é um formato de tabela de alto desempenho para conjuntos de dados analíticos de grande escala. Esta página explica os comandos e códigos utilizados no notebook `spark-icebaerg.ipynb`.

## Configuração do Ambiente Apache Iceberg

Para trabalhar com o Apache Iceberg no Spark, é necessária a seguinte configuração da sessão:

```python
from pyspark.sql import SparkSession

# Inicializando a sessão Spark com configurações do Iceberg
spark = SparkSession.builder \
  .appName("IcebergLocalDevelopment") \
  .config('spark.jars.packages', 'org.apache.iceberg:iceberg-spark-runtime-3.5_2.12:1.6.1') \
  .config("spark.sql.extensions", "org.apache.iceberg.spark.extensions.IcebergSparkSessionExtensions") \
  .config("spark.sql.catalog.local", "org.apache.iceberg.spark.SparkCatalog") \
  .config("spark.sql.catalog.local.type", "hadoop") \
  .config("spark.sql.catalog.local.warehouse", "spark-warehouse/iceberg") \
  .getOrCreate()
```

### Explicação da Configuração

| Configuração | Descrição |
|-------------|-----------|
| `appName("IcebergLocalDevelopment")` | Define o nome da aplicação Spark |
| `spark.jars.packages` | Adiciona a dependência do Apache Iceberg |
| `spark.sql.extensions` | Habilita as extensões SQL específicas do Iceberg |
| `spark.sql.catalog.local` | Define o catálogo "local" para usar o Iceberg |
| `spark.sql.catalog.local.type` | Especifica o tipo de armazenamento como "hadoop" |
| `spark.sql.catalog.local.warehouse` | Define o diretório para armazenar os dados Iceberg |

## Operações CRUD no Apache Iceberg

### 1. Criando uma Tabela (CREATE)

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

Observe a diferença importante: ao contrário do Delta Lake, o Iceberg requer o prefixo do catálogo (neste caso, `local.`) antes do nome da tabela. O parâmetro `USING iceberg` indica o formato de armazenamento.

### 2. Inserindo Dados (INSERT)

```sql
INSERT INTO local.funcionarios_iceberg 
(id_funcionario, nome, cargo, departamento, salario, data_admissao, ativo)
VALUES 
    (1, 'João Silva', 'Analista', 'TI', 5000.00, DATE '2023-01-15', true),
    (2, 'Maria Santos', 'Gerente', 'RH', 8000.00, DATE '2022-06-20', true),
    (3, 'Pedro Oliveira', 'Desenvolvedor', 'TI', 6000.00, DATE '2023-03-10', true),
    (4, 'Ana Costa', 'Assistente', 'Financeiro', 3500.00, DATE '2023-11-05', false)
```

Observe estas particularidades do Iceberg:
- O uso do prefixo `local.` no nome da tabela
- A palavra-chave `DATE` antes dos literais de data
- Os valores booleanos são escritos como `true` ou `false` (em minúsculas)

### 3. Atualizando Dados (UPDATE)

```sql
UPDATE local.funcionarios_iceberg 
SET cargo = 'Gerente de TI',
    salario = 9000.00
WHERE id_funcionario = 1
```

O Iceberg também suporta atualizações parciais com sintaxe SQL padrão, mantendo as propriedades ACID.

#### Vantagens do UPDATE no Iceberg:

- Transações atômicas
- Evolução de schema preservando versões anteriores
- Rastreamento preciso de alterações
- Otimizações de leitura após atualizações

### 4. Excluindo Dados (DELETE)

```sql
DELETE FROM local.funcionarios_iceberg 
WHERE ativo = false
```

No exemplo, estamos removendo funcionários inativos utilizando uma condição booleana.

### 5. Consultando Dados (SELECT)

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

O Iceberg suporta todas as operações SQL padrão do Spark, incluindo funções de agregação como `COUNT`, `AVG`, `MAX` e `MIN`.

## Recursos Avançados do Apache Iceberg

### Time Travel

O Iceberg permite acessar versões anteriores dos dados:

```sql
-- Consultando por snapshot ID
SELECT * FROM local.funcionarios_iceberg.snapshots

-- Consultando uma versão específica
SELECT * FROM local.funcionarios_iceberg VERSION AS OF 1

-- Consultando dados em um timestamp específico
SELECT * FROM local.funcionarios_iceberg TIMESTAMP AS OF '2023-01-15 12:00:00'
```

### Metadados e Snapshots

```sql
-- Listar todos os snapshots (versões) da tabela
SELECT * FROM local.funcionarios_iceberg.snapshots

-- Listar arquivos de dados
SELECT * FROM local.funcionarios_iceberg.files

-- Ver histórico de modificações
SELECT * FROM local.funcionarios_iceberg.history
```

### Manutenção de Tabelas

```sql
-- Compactar arquivos pequenos
CALL local.system.rewrite_data_files(
  table => 'funcionarios_iceberg',
  strategy => 'binpack'
)

-- Remover snapshots antigos
CALL local.system.expire_snapshots(
  table => 'funcionarios_iceberg',
  older_than => TIMESTAMP '2023-01-01 00:00:00'
)
```

### Evolução de Particionamento

Uma vantagem única do Iceberg é a capacidade de alterar o esquema de particionamento sem reescrever os dados:

```sql
-- Alterar a estratégia de particionamento
ALTER TABLE local.funcionarios_iceberg
REPLACE PARTITION FIELD departamento
```

## Melhores Práticas para Apache Iceberg

1. **Sempre use o prefixo do catálogo** (ex: `local.`) ao referenciar tabelas Iceberg.

2. **Considere particionamento oculto** para melhorar o desempenho sem expor a estrutura:
   ```sql
   CREATE TABLE local.funcionarios_iceberg (...)
   USING iceberg
   PARTITIONED BY (departamento)
   ```

3. **Execute manutenção periódica** para compactar arquivos e remover snapshots antigos.

4. **Aproveite a evolução de esquema** para adicionar, renomear ou alterar colunas sem interrupções.

5. **Utilize metadados do Iceberg** para diagnóstico e otimização.