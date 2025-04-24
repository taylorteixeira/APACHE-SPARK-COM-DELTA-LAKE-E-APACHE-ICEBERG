## 📂 Projeto Apache Spark com Delta Lake

Projeto desenvolvido para demonstração do Apache Spark Local (pyspark) gravando arquivos no formato Delta Lake também de forma local criando modelo ER, imagens e códigos DDL - e da fonte de dados utilizada (dados públicos) e evidenciando e explicando, 
com exemplos, os comandos de INSERT, UPDATE e DELETE.

Projeto python inicializado com o [UV](https://github.com/astral-sh/uv).

Comandos utilizados para setup do ambiente:

#### Usando `uv` (recomendado):

```bash
uv pip install -r requirements.txt
```

#### Ou, usando `pip` diretamente:

```bash
pip install -r requirements.txt
```

#### Ou, usando `poetry` (caso utilize o `pyproject.toml`):

```bash
poetry install
```

```bash copy
uv init
uv venv
source .venv/bin/activate
uv add pyspark==3.5.3 delta-spark==3.2.0 jupyterlab ipykernel
```

#### Usando `uv` (recomendado):

```bash
uv pip freeze
```
Atualizar o requirements.txt

**Nota:** Antes de executar o arquivo citado acima, não esqueça de selecionar o seu ambiente virtual (.venv) como Kernel do seu jupyter notebook.

![image](https://github.com/user-attachments/assets/6394e5b6-c51e-4245-bad2-450d864e422a)

Os exemplos de código pyspark/python para instanciar o Spark, bem como criar e manipular uma tabela Delta Lake, está no arquivo `spark-delta.ipynb` e `spark-iceberg.ipynb`.

#### Usando `uv` (recomendado):

```bash
uv add mkdocs-material
```
```bash
python -m mkdocs build
```

## ❗Erros possoveis: 


####  Se o java não estiver configurado: (recomendado):

Baixe o JDK 11:
https://adoptium.net/temurin/releases/?version=11

```bash
C:\Program Files\Java\jdk-11

```
No PowerShell (temporário):
```bash
$env:JAVA_HOME="C:\Program Files\Java\jdk-11"
$env:PATH="$env:JAVA_HOME\bin;$env:PATH"
```

Ou defina permanentemente nas variáveis de ambiente do Windows.

⚠️ Use preferencialmente o JDK 8, 11 ou 17, pois são compatíveis com Spark. Evite o JDK 21.



## Corrigindo o Erro `Py4JJavaError` com `HADOOP_HOME` no Windows


Ao iniciar uma sessão do **Apache Spark** no Windows, você pode encontrar o seguinte erro:

```text
Py4JJavaError: An error occurred while calling None.org.apache.spark.api.java.JavaSparkContext.
java.io.FileNotFoundException: HADOOP_HOME and hadoop.home.dir are unset.
```

Esse erro ocorre porque o Spark tenta executar comandos do Hadoop que são dependentes do sistema operacional, como `chmod`. No Windows, ele precisa do utilitário `winutils.exe`, que não está presente por padrão.

---


### 1. Baixe o `winutils.exe`

Baixe o executável `winutils.exe` correspondente à versão do Hadoop usada pelo Spark (geralmente Hadoop 2.7 é suficiente para uso local):

📁 Link sugerido:  
➡️ https://github.com/cdarlint/winutils

Faça o download da pasta `hadoop-2.7.1` e salve em um diretório local. Por exemplo:

```
C:\hadoop\bin\winutils.exe
```

---

### 2. Configure a variável de ambiente `HADOOP_HOME`

#### Opção A – No código Python (ideal para notebooks ou scripts isolados)

Adicione antes de iniciar o `SparkSession`:

```python
import os

os.environ['HADOOP_HOME'] = 'C:\\hadoop'
os.environ['PATH'] += os.pathsep + os.path.join(os.environ['HADOOP_HOME'], 'bin')
```

#### Opção B – Via configurações do sistema

1. Acesse: **Painel de Controle > Sistema > Configurações Avançadas > Variáveis de Ambiente**
2. Crie as seguintes variáveis:
   - `HADOOP_HOME = C:\hadoop`
   - Adicione `%HADOOP_HOME%\bin` à variável `PATH`

---

### 3. Exemplo de Código Corrigido

```python
import os
from pyspark.sql import SparkSession

# Corrigir erro HADOOP_HOME no Windows
os.environ['HADOOP_HOME'] = 'C:\\hadoop'
os.environ['PATH'] += os.pathsep + os.path.join(os.environ['HADOOP_HOME'], 'bin')

# Criar SparkSession com suporte ao Apache Iceberg
spark = SparkSession.builder \
    .appName("IcebergLocalDevelopment") \
    .config('spark.jars.packages', 'org.apache.iceberg:iceberg-spark-runtime-3.5_2.12:1.6.1') \
    .config("spark.sql.extensions", "org.apache.iceberg.spark.extensions.IcebergSparkSessionExtensions") \
    .config("spark.sql.catalog.local", "org.apache.iceberg.spark.SparkCatalog") \
    .config("spark.sql.catalog.local.type", "hadoop") \
    .config("spark.sql.catalog.local.warehouse", "spark-warehouse/iceberg") \
    .getOrCreate()
```

---

## Observações

- Esse processo é necessário apenas no **Windows**.
- O `winutils.exe` não realiza nada de importante em ambientes locais — ele apenas evita falhas nas chamadas nativas do Spark para o Hadoop.
- Para ambientes Linux/Mac, isso **não é necessário**.

---

## Requisitos

- Apache Spark (ex.: 3.5.x)
- Java (JDK 8 ou superior)
- Python (3.x)
- Apache Iceberg (via `spark.jars.packages`)
- `winutils.exe` no Windows

---

## Referências

- [Problemas comuns do Hadoop no Windows](https://wiki.apache.org/hadoop/WindowsProblems)
- [GitHub: cdarlint/winutils](https://github.com/cdarlint/winutils) 

