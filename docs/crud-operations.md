# Operações CRUD com Delta Lake e Iceberg

Nesta documentação, vamos explorar as operações CRUD (Create, Read, Update, Delete) usando Apache Spark com Delta Lake e Iceberg. Vamos demonstrar exemplos práticos para cada operação.

## Delta Lake

### INSERT

O comando INSERT é usado para adicionar novos registros a uma tabela Delta Lake. Veja o exemplo:

```python
# Inserindo dados iniciais na tabela vendas_delta
spark.sql("""
    INSERT INTO vendas_delta (id_venda, produto, quantidade, valor_unitario, data_venda, status)
    VALUES
        (1, 'Notebook', 2, 4500.00, '2024-01-15', 'CONCLUIDO'),
        (2, 'Smartphone', 3, 2500.00, '2024-01-16', 'CONCLUIDO'),
        (3, 'Tablet', 1, 1800.00, '2024-01-17', 'PENDENTE'),
        (4, 'Monitor', 2, 1200.00, '2024-01-18', 'CANCELADO')
""")
```

Este exemplo demonstra:

- Inserção de múltiplos registros em uma única operação
- Uso de diferentes tipos de dados (INT, STRING, DECIMAL, DATE)
- Valores formatados corretamente para cada tipo de dado

### UPDATE

O comando UPDATE permite modificar registros existentes com base em uma condição:

```python
# Atualizando status e valor de uma venda
spark.sql("""
    UPDATE vendas_delta
    SET status = 'CONCLUIDO',
        valor_unitario = 1700.00
    WHERE id_venda = 3
""")
```

Este exemplo mostra:

- Atualização de múltiplas colunas simultaneamente
- Uso de condição WHERE para especificar quais registros atualizar
- Modificação de valores numéricos e strings

### DELETE

O comando DELETE remove registros que atendem a uma condição específica:

```python
# Deletando vendas canceladas
spark.sql("""
    DELETE FROM vendas_delta
    WHERE status = 'CANCELADO'
""")
```

Este exemplo ilustra:

- Remoção de registros com base em uma condição
- Uso de operadores de comparação na cláusula WHERE
- Impacto na integridade dos dados

## Iceberg

### INSERT

O comando INSERT no Iceberg segue uma sintaxe similar, mas com o namespace do catálogo:

```python
# Inserindo dados iniciais na tabela funcionarios_iceberg
spark.sql("""
    INSERT INTO local.funcionarios_iceberg
    (id_funcionario, nome, cargo, departamento, salario, data_admissao, ativo)
    VALUES
        (1, 'João Silva', 'Analista', 'TI', 5000.00, '2023-01-15', true),
        (2, 'Maria Santos', 'Gerente', 'RH', 8000.00, '2022-06-20', true),
        (3, 'Pedro Oliveira', 'Desenvolvedor', 'TI', 6000.00, '2023-03-10', true),
        (4, 'Ana Costa', 'Assistente', 'Financeiro', 3500.00, '2023-11-05', false)
""")
```

Principais diferenças:

- Uso do namespace `local.` antes do nome da tabela
- Suporte a tipos de dados específicos como BOOLEAN
- Estrutura de dados mais complexa

### UPDATE

O UPDATE no Iceberg também requer o namespace do catálogo:

```python
# Promovendo funcionário e ajustando salário
spark.sql("""
    UPDATE local.funcionarios_iceberg
    SET cargo = 'Gerente de TI',
        salario = 9000.00
    WHERE id_funcionario = 1
""")
```

Características importantes:

- Atualização de múltiplas colunas
- Uso de identificadores únicos na condição WHERE
- Suporte a tipos de dados complexos

### DELETE

O DELETE no Iceberg segue o mesmo padrão:

```python
# Removendo funcionários inativos
spark.sql("""
    DELETE FROM local.funcionarios_iceberg
    WHERE ativo = false
""")
```

Aspectos importantes:

- Uso de operadores booleanos na condição
- Impacto na consistência dos dados
- Suporte a transações ACID

## Considerações Importantes

1. **Delta Lake vs Iceberg**:

   - Delta Lake usa o catálogo padrão do Spark
   - Iceberg requer especificação do catálogo (ex: `local.`)
   - Ambos suportam transações ACID

2. **Melhores Práticas**:

   - Sempre use condições específicas em UPDATE e DELETE
   - Valide os dados antes de operações de modificação
   - Considere o impacto das operações em consultas existentes

3. **Performance**:
   - Operações em lote são mais eficientes que operações individuais
   - Índices podem melhorar o desempenho de operações de modificação
   - Considere particionamento para grandes volumes de dados
