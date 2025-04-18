## Projeto Apache Spark com Delta Lake

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
Deve apontar para algo como:

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
