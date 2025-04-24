# Configuração do Ambiente

Esta seção descreve como configurar o ambiente para trabalhar com Apache Spark, Delta Lake e Iceberg.

## Pré-requisitos

- Java 8 ou superior
- Python 3.8 ou superior
- Apache Spark 3.5.0 ou superior
- Git

## Instalação do Java

1. Baixe o JDK mais recente do [site oficial da Oracle](https://www.oracle.com/java/technologies/downloads/)
2. Instale seguindo as instruções do instalador
3. Configure a variável de ambiente JAVA_HOME

## Instalação do Python

1. Baixe o Python do [site oficial](https://www.python.org/downloads/)
2. Instale seguindo as instruções do instalador
3. Verifique se o Python está no PATH do sistema

## Instalação do Apache Spark

1. Baixe o Apache Spark do [site oficial](https://spark.apache.org/downloads.html)
2. Extraia o arquivo para um diretório de sua escolha
3. Configure as variáveis de ambiente:
   - SPARK_HOME: caminho para o diretório do Spark
   - PATH: adicione $SPARK_HOME/bin

## Instalação das Dependências Python

```bash
pip install pyspark==3.5.3
pip install delta-spark==3.2.0
pip install iceberg-spark-runtime==1.6.1
```

## Configuração do Ambiente de Desenvolvimento

### VS Code

1. Instale as extensões:

   - Python
   - Pylance
   - Jupyter

2. Configure o interpretador Python:
   - Ctrl+Shift+P
   - "Python: Select Interpreter"
   - Escolha o ambiente virtual

### Jupyter Notebook

```bash
pip install jupyterlab
jupyter lab
```

## Verificação da Instalação

```python
from pyspark.sql import SparkSession

# Verificar Delta Lake
spark = (
    SparkSession
    .builder
    .config("spark.jars.packages", "io.delta:delta-spark_2.12:3.2.0")
    .getOrCreate()
)

# Verificar Iceberg
spark = (
    SparkSession
    .builder
    .config("spark.jars.packages", "org.apache.iceberg:iceberg-spark-runtime-3.5_2.12:1.6.1")
    .getOrCreate()
)
```

## Solução de Problemas Comuns

1. **Erro de Java não encontrado**

   - Verifique se JAVA_HOME está configurado corretamente
   - Reinicie o terminal após configurar as variáveis

2. **Erro de dependências Python**

   - Use um ambiente virtual
   - Atualize o pip: `python -m pip install --upgrade pip`

3. **Erro de memória insuficiente**
   - Ajuste as configurações de memória do Spark
   - Reduza o tamanho dos dados de teste
