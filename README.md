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

Os exemplos de código pyspark/python para instanciar o Spark, bem como criar e manipular uma tabela Delta Lake, está no arquivo `spark-delta.ipynb` e `spark-iceberg.ipynb`.
