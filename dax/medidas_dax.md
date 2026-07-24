## Criação tabela "Calendario" para ser utilizada como tabela de datas:

´´´DAX
Calendario = CALENDAR(MIN(fato_vendas[data_id]), MAX(fato_vendas[data_id]))
´´´

### Criando Coluna "Ano" na tabela "Calendario":

´´´DAX
Ano = YEAR(Calendario[Date])
´´´

### Criando Coluna "Mes" na tabela "Calendario":

´´´DAX
Mes = MONTH(Calendario[Date])
´´´

### Criando Coluna "NomeMes" na tabela "Calendario":

´´´DAX
NomeMes = FORMAT(Calendario[Date], "mmmm")
´´´

### Criando Coluna "Trimestre" na tabela "Calendario":

´´´DAX
Trimestre = QUARTER(Calendario[Date])
´´´

## Medida "Total vendas" com a soma do total de vendas realizadas em todos os periodos da base de dados:

´´´DAX
Total vendas = SUM(fato_vendas[valor_venda])
´´´

## "Vendas Eletrônicos" somando todas as vendas de produtos da categoria Eltrônicos:

´´´DAX
Vendas Eletrônicos = CALCULATE([Total vendas],dim_produto[categoria]="Eletrônicos")
´´´

## "Vendas Mes Anterior" para calcular o valor de vendas do mês anterior:

´´´DAX
Vendas Mes Anterior = CALCULATE([Total vendas], PREVIOUSMONTH(Calendario[Date]))
´´´

## "% do Total" utilizando as fórmulas DIVIDE para realizar a divisão de maneira segura sem sobras, e ALL para retirar a relação da fórmula com qualquer filtro:

´´´DAX
% do Total = DIVIDE([Total vendas], CALCULATE([Total vendas], ALL(fato_vendas)))
´´´

## "Variacao MoM" calculando variação nominal de um mês em relação ao mês anterior:

´´´DAX
Variacao MoM = [Total vendas] - [Vendas Mes Anterior]
´´´

## "Variacao MoM" calculando a variação percentual das vendas de um mês em relação ao mês anterior, utilizando Variáveis (VAR e RETURN):

´´´DAX
% Variacao MoM = 
VAR VendasAtual = [Total vendas]
VAR VendasAnterior = [Vendas Mes Anterior]
VAR Diferenca = VendasAtual - VendasAnterior
RETURN
    DIVIDE(Diferenca, VendasAnterior)
´´´
