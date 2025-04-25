# Apache Spark com Delta Lake e Iceberg

Esta documentação fornece um guia completo sobre o uso do Apache Spark com os formatos de armazenamento Delta Lake e Apache Iceberg.

<div class="grid">
<div class="card">
<h3><span class="badge delta">Delta Lake</span> Formato aberto com transações ACID</h3>
<p>Descubra como o Delta Lake traz consistência, confiabilidade e desempenho para seus data lakes usando operações ACID, time travel e muito mais.</p>
<a href="intro/delta-lake/" class="more">Saiba mais →</a>
</div>

<div class="card">
<h3><span class="badge iceberg">Iceberg</span> Formato de tabela de alto desempenho</h3>
<p>Explore o Apache Iceberg, um formato de tabela de código aberto que oferece evolução de esquema, gerenciamento de partições e recursos avançados de consulta.</p>
<a href="intro/iceberg/" class="more">Saiba mais →</a>
</div>

<div class="card">
<h3><span class="badge spark">Spark</span> Engine de processamento unificado</h3>
<p>Aprenda sobre o Apache Spark e como ele integra-se perfeitamente com formatos de armazenamento modernos para processamento de dados em larga escala.</p>
<a href="intro/spark/" class="more">Saiba mais →</a>
</div>
</div>

## Novidades na documentação

!!! info "Conteúdo Novo"
    Adicionamos novas seções explicativas que detalham os comandos do Spark Delta e Iceberg, além de um diagrama ER detalhado!

- [Comandos Delta Lake](commands/delta-commands.md) - Lista completa de comandos para trabalhar com Delta Lake
- [Comandos Iceberg](commands/iceberg-commands.md) - Lista detalhada de comandos para trabalhar com Apache Iceberg
- [Diagrama ER](data-model/er-diagram.md) - Modelo de dados completo com diagrama ER dos exemplos

## Por que usar formatos de tabela modernos?

Os formatos de tabela modernos como Delta Lake e Iceberg resolvem vários problemas comuns encontrados em data lakes tradicionais:

| Problema | Solução Delta Lake | Solução Iceberg |
|---------|-------------------|-----------------|
| Inconsistência de dados | Transações ACID | Transações atômicas |
| Problemas de desempenho | Z-Ordering, indexação | Ordenação, otimização de consultas |
| Evolução de esquema limitada | Schema enforcement e evolution | Schema evolution flexível |
| Dados desatualizados | Time travel, snapshots | Viagem no tempo baseada em snapshots |
| Gerenciamento complexo | Operações de limpeza automáticas | Sistema de metadados avançado |

## Operações CRUD

Ambos os formatos suportam operações CRUD (Create, Read, Update, Delete) completas, permitindo que você trabalhe com seus dados da mesma forma que faria com um banco de dados tradicional:

```python
# Exemplo de operação CRUD com Delta Lake
spark.sql("""
    -- Criação (Create)
    CREATE TABLE pessoas USING delta AS SELECT * FROM dados_origem;
    
    -- Leitura (Read)
    SELECT * FROM pessoas WHERE idade > 18;
    
    -- Atualização (Update)
    UPDATE pessoas SET status = 'ATIVO' WHERE id = 1;
    
    -- Exclusão (Delete)
    DELETE FROM pessoas WHERE status = 'INATIVO';
""")
```

Para mais detalhes sobre operações CRUD, consulte a [seção de operações CRUD](crud-operations.md).

## Começando

Para começar a usar o Apache Spark com Delta Lake e Iceberg, siga estes passos:

1. [Configure seu ambiente](setup/environment.md)
2. [Instale as dependências necessárias](setup/dependencies.md)
3. Explore os exemplos para [Delta Lake](examples/delta-lake.md) e [Iceberg](examples/iceberg.md)
4. Consulte a [referência de comandos SQL](reference/sql-commands.md) ou a [API Python](reference/python-api.md)

## Recursos Adicionais

- [Documentação oficial do Apache Spark](https://spark.apache.org/docs/latest/)
- [Documentação do Delta Lake](https://docs.delta.io/latest/index.html)
- [Documentação do Iceberg](https://iceberg.apache.org/docs/latest/)
