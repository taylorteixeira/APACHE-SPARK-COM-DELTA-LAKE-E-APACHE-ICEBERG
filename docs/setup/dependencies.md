# Dependências

Esta seção lista todas as dependências necessárias para trabalhar com Apache Spark, Delta Lake e Iceberg.

## Dependências Principais

### Apache Spark

- **Versão**: 3.5.0 ou superior
- **Pacote**: `pyspark==3.5.3`
- **Documentação**: [Apache Spark Documentation](https://spark.apache.org/docs/latest/)

### Delta Lake

- **Versão**: 3.2.0
- **Pacote**: `delta-spark==3.2.0`
- **Documentação**: [Delta Lake Documentation](https://docs.delta.io/latest/index.html)

### Iceberg

- **Versão**: 1.6.1
- **Pacote**: `iceberg-spark-runtime==1.6.1`
- **Documentação**: [Iceberg Documentation](https://iceberg.apache.org/docs/latest/)

## Dependências de Desenvolvimento

### Python

- **Versão**: 3.8 ou superior
- **Pacotes**:
  ```bash
  pip install jupyterlab
  pip install black
  pip install flake8
  pip install pytest
  ```

### Java

- **Versão**: 8 ou superior
- **JDK**: Oracle JDK ou OpenJDK

## Arquivo requirements.txt

```txt
# Apache Spark
pyspark==3.5.3

# Delta Lake
delta-spark==3.2.0

# Iceberg
iceberg-spark-runtime==1.6.1

# Desenvolvimento
jupyterlab>=4.4.0
black>=24.1.1
flake8>=7.0.0
pytest>=8.0.0
```

## Configuração do Maven (opcional)

Se estiver usando Maven para gerenciar dependências Java:

```xml
<dependencies>
    <!-- Apache Spark -->
    <dependency>
        <groupId>org.apache.spark</groupId>
        <artifactId>spark-core_2.12</artifactId>
        <version>3.5.0</version>
    </dependency>

    <!-- Delta Lake -->
    <dependency>
        <groupId>io.delta</groupId>
        <artifactId>delta-core_2.12</artifactId>
        <version>3.2.0</version>
    </dependency>

    <!-- Iceberg -->
    <dependency>
        <groupId>org.apache.iceberg</groupId>
        <artifactId>iceberg-spark-runtime-3.5_2.12</artifactId>
        <version>1.6.1</version>
    </dependency>
</dependencies>
```

## Verificação de Versões

Para verificar as versões instaladas:

```python
import pyspark
import delta
import iceberg

print(f"PySpark version: {pyspark.__version__}")
print(f"Delta Lake version: {delta.__version__}")
print(f"Iceberg version: {iceberg.__version__}")
```

## Atualização de Dependências

Para atualizar as dependências:

```bash
# Atualizar todas as dependências
pip install --upgrade -r requirements.txt

# Atualizar uma dependência específica
pip install --upgrade pyspark
```
