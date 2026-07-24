# Análise de Vendas — SQL Avançado, Star Schema e Power BI no Azure

Projeto ponta a ponta: modelagem dimensional em Azure SQL Database, construção de um modelo Power BI com relacionamentos corretos, medidas DAX e segurança em nível de linha (RLS), publicado no Power BI Service.

🔗 **[Ver relatório publicado no Power BI Service](https://app.powerbi.com/groups/me/reports/71614ce1-2cd4-4af3-8a79-a0a12b3efea5/0db36fc545e7de11cbdb?redirectedFromSignup=1&experience=power-bi)**

## Stack utilizada
- **Banco de dados:** Azure SQL Database (T-SQL)
- **Modelagem:** Star Schema (1 tabela Fato + 3 Dimensões)
- **Visualização:** Power BI Desktop + Power BI Service
- **Linguagens:** SQL, DAX

## O que este projeto demonstra

### 1. SQL avançado
- Window functions (`ROW_NUMBER`, `RANK`, `DENSE_RANK`, `LAG`, `SUM() OVER`, `ROWS BETWEEN`, `NTILE`)
- CTEs simples, encadeadas e recursivas (hierarquia de funcionários)
- Análise de performance (Scan vs Seek, seletividade, `SET STATISTICS IO`)
- Scripts completos em [`/sql`](./sql)

### 2. Modelagem dimensional (Star Schema)
Transformação de uma tabela flat (`vendas`) em um modelo dimensional:

```
              dim_vendedor
                   │
dim_produto ── fato_vendas ── dim_tempo -> (Calendario)
```

- Separação em tabela Fato (eventos numéricos) e Dimensões (contexto descritivo)
- Correção de cardinalidade de relacionamento (1:1 incorreto → N:1 correto) — ver [`/screenshots/relacionamentos.png`](./screenshots)
- Tabela calendário construída via DAX (`CALENDAR()`) para garantir continuidade de datas, resolvendo limitação de uma dimensão de tempo esparsa

### 3. DAX — do básico ao intermediário
- `CALCULATE` para adicionar contexto de filtro
- `CALCULATE` + `ALL` para cálculo de % do total
- Time intelligence (`PREVIOUSMONTH`) para variação mês a mês
- `VAR`/`RETURN` para organizar e otimizar fórmulas complexas
- Medidas documentadas em [`/dax`](./dax)

### 4. Segurança — Row-Level Security (RLS)
Implementação de RLS restringindo a visualização de dados por vendedor, com propagação automática do filtro por todo o modelo relacional (Cartões, gráficos e tabelas). Ver [`/screenshots/rls-teste.png`](./screenshots).

### 5. Design e usabilidade
- Escolha de visual alinhada ao tipo de pergunta (comparação → barras, composição → rosca, tendência → linha)
- Formatação condicional (escala de cor divergente) para variação percentual

## Estrutura do repositório
```
/sql            → scripts de window functions, CTEs e criação do star schema
/dax            → medidas DAX documentadas com explicação
/screenshots    → modelo relacional, dashboard final, teste de RLS
```

## Principais decisões técnicas (e por quê)
- **Import em vez de DirectQuery:** dataset pequeno e sem necessidade de dado em tempo real, priorizando performance de navegação.
- **Tabela calendário via DAX, não via SQL:** garante continuidade de datas sem depender de todas as datas existirem na tabela fato — prática comum mesmo com fonte de dados relacional.
- **RLS ao nível de Dimensão, não de Fato:** filtro aplicado em `dim_vendedor` se propaga automaticamente para `fato_vendas` via relacionamento, evitando duplicar regras de segurança em múltiplas tabelas.
