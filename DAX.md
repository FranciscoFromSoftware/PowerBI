# Snippets de Power BI

## Calendário com o dia da semana em português

```DAX
dcalendario =
ADDCOLUMNS (
    CALENDAR (
        DATE(2001, 01,01),
        MAX ( 'fDeputadoDespesa'[data_documento] )
    ),
    "Ano", YEAR ( [Date] ),
    "Mes", FORMAT ( [Date], "MMMM" ),
    "Mes_Num", MONTH ( [Date] ),
    "AnoMesNum", FORMAT ( [Date], "YYYY/MMM" ),
    "Trimestre", "T" & FORMAT ( [Date], "Q" ),
    "Dia", DAY ( [Date] ),
    "DiaSemana", FORMAT ( [Date], "dddd" ),
    "DiaSemana_Num", WEEKDAY ( [Date], 1 ) - 1,
    "SemanaAno", WEEKNUM ( [Date], 2 ),
    "SemanaAno_Num", YEAR( [Date] ) & " - " & WEEKNUM ( [Date], 2 ),
    "MesAno", UPPER( FORMAT ( [Date], "mmm/yyyy" ) ),
    "SemanaMes", INT ( ( DAY ( [Date] ) - 1 ) / 7 ) + 1,
    "SemanaMes_Nome", "Sem - " & INT ( ( DAY ( [Date] ) - 1 ) / 7 ) + 1,
    "MesAnoNum", CONCATENATE(YEAR([Date]),MONTH([Date])),
    "IndiceMes", YEAR ( [Date] ) * 12 + MONTH ( [Date] )
)
```

## Índices e rankings

### Rank por vendedor e filial

A fórmula abaixo gera um rank considerando as colunas de categoria informadas. Neste exemplo, o rank é calculado com base na filial e no vendedor.

```DAX
Ranking =
RANKX(
    ALL('Planilha1'[Vendedor],Planilha1[Filial]),
    CALCULATE(SUM('Planilha1'[Valor da Venda (R$)]))
    ,,
    DESC,
    Dense
)
```

### Rank por aplicativo

```DAX
Ranking =
RANKX(
    ALL('Aplicativo_fMeuDia'[AppsGamesSoftware]),
    CALCULATE(SUM('Aplicativo_fMeuDia'[duration_seconds]))
    ,,
    DESC,
    Dense
)
```

### Rank por classe

```DAX
RankClasse =
VAR ClasseAtual = SELECTEDVALUE(Vendas[Coluna])

VAR TabelaParaRank =
    -- 1. Cria a tabela de iteração: Todas as combinações de Usuário e Classe.
    FILTER(
        ALL(Vendas[Usuario], Vendas[Coluna]),
        -- 2. Filtra a tabela de iteração apenas para a Classe atual.
        Vendas[Coluna] = ClasseAtual
    )

VAR RankCalculado =
    RANKX(
        TabelaParaRank,

        -- 3. Expressão para Classificar (A Chave):
        -- Calcula o Total Vendas removendo o filtro da coluna Filial.
        CALCULATE(
            [Vendas],
            REMOVEFILTERS(Vendas[Filial]) -- Ignora qualquer filtro aplicado à coluna Filial
        ),

        -- 4. Ordem e Empates
        , DESC, DENSE
    )

RETURN
    RankCalculado
```

## Formatação de dados

### Medida com ícones de status por data

```DAX
medidasStatusIcon =
ADDCOLUMNS(
    dCalendario,
    "Status",
    SWITCH(
        TRUE(),
        [Date] < TODAY(), "✔️",
        [Date] = TODAY(), "⏳",
        [Date] > TODAY(), "❌"
    )
)
```

### Medida com ícone de status por duração

```DAX
medidasStatusIcon =
VAR DATA_DURAÇÃO= MAX(fDados_Curva[Atributo]) - MIN(fDados_Curva[Atributo])
VAR PERCENTUAL= FORMAT(DATA_DURAÇÃO, "#%")
RETURN IF(
    DATA_DURAÇÃO < 30, "❌",
    IF(
        DATA_DURAÇÃO = 30, "✔️",
        IF(
            DATA_DURAÇÃO > 30, "⏳",
            BLANK()
        )
    )
)
```

### Ícone de ranking

```DAX
Ícone Ranking =
SWITCH(
    TRUE(),
    [Ranking] = 1, "🏆",
    [Ranking] = 2, "🥈",
    [Ranking] = 3, "🥉",
    [Ranking] >= 4, "🏃‍♂️‍➡️",
    BLANK()
)
```

> O site [symbl.cc](https://symbl.cc) tem mais opções de setas.

## Dias úteis

```DAX
Dias Uteis =
NETWORDAYS(
    SELECTEDVALUE(dCalendario[Date]), -- data inicial
    SELECTEDVALUE(dCalendario[Date]), -- data final
    1, -- 1 = domingo é o primeiro dia da semana
    FERIADOS -- lista de feriados
)
```

O parâmetro de início da semana pode usar os seguintes valores:

- `1`: domingo é o primeiro dia da semana.
- `2` ou `11`: segunda-feira é o primeiro dia da semana.
- `12`: terça-feira é o primeiro dia da semana.
- `13`: quarta-feira é o primeiro dia da semana.
- `14`: quinta-feira é o primeiro dia da semana.
- `15`: sexta-feira é o primeiro dia da semana.
- `16`: sábado é o primeiro dia da semana.

## Tratamento de dados em branco

```DAX
TratarDadosEmBranco =
    COALESCE(
        [Vendedor],
        [Canal de Vendas]
    )
```

> `COALESCE` retorna o primeiro valor não nulo de uma lista e é útil para tratar dados em branco ou nulos em colunas ou medidas.

## Ranking dos 5 itens

```DAX
Raqueando5itens =
    RANKX(
        ALL(fDados[Vendedor]), -- tabela a ser ranqueada
        [Vendas], -- expressão a ser avaliada
        5, -- 5 = número de itens a serem ranqueados
        DESC, -- DESC = ordem decrescente
        DENSE -- DENSE = ranqueia itens com o mesmo valor com o mesmo número
    )
```

> `RANKX` é útil para ranquear itens em uma tabela ou medida.

## Concatenação de colunas

```DAX
ConcatenarColunas =
    CONCATENATEX(
        VALUES(fDados[Vendedor]), -- tabela a ser concatenada
        fDados[Vendedor], -- coluna a ser concatenada
        ", " -- separador entre os itens concatenados
    )
```

> `CONCATENATEX` é útil para concatenar itens em uma tabela ou medida.

## Acumulado de vendas

```DAX
Acumulado Vendas =
    CALCULATE(
        SUM(fDados[Vendas]), -- soma das vendas
        DATESYTD(dCalendario[Date]), -- calcula o acumulado até a data atual
        ALL(dCalendario[Date]) -- remove o filtro da tabela de calendário
    )
```

- `DATESYTD` calcula o acumulado até a data atual.
- `ALL` remove o filtro da tabela de calendário para calcular o acumulado.

## Valor e porcentagem

```DAX
Valor e Porcetagem =
VAR TotalGeral =
    CALCULATE(
        COUNTROWS(DISTINCT('Linkedin_certificacoes_meus_dados'[nome])),
        REMOVEFILTERS(dim_Calendario)
    ) -- Calculo do total geral sem filtros de categoria data, para gerar o valor bruto.
VAR ValorAtual = [Medida 2] -- Calculo normal para rótulo de dados.
VAR Porcentagem = DIVIDE(ValorAtual, TotalGeral) Calculo da Porcetagem

RETURN
FORMAT(ValorAtual, "#,##0") & " (" & FORMAT(Porcentagem, "0.0%") & ")" -- Formatação dos valores
```

## Análise temporal personalizada

É necessária uma tabela desconectada para ativar o filtro. Se usar a coluna de mês da própria tabela Calendário, o filtro corta o eixo do gráfico e você perde os 12 pontos.

### Tabela de filtro de mês

```DAX
Filtro Mes =
SUMMARIZE (
    Calendario,
    Calendario[AnoMes],        -- ex.: 202604 (número)
    Calendario[MesAnoTexto],   -- ex.: "abr/2026"
    "FimMes", MAX ( Calendario[Date] )
)
```

Em `MesAnoTexto`, use **Classificar por coluna → AnoMes**. Não crie relacionamento dessa tabela com nenhuma outra. Use `Filtro Mes[MesAnoTexto]` em um segmentador (slicer).

### Data de referência

```DAX
Data_Ref =
IF (
    ISFILTERED ( 'Filtro Mes'[MesAnoTexto] ),
    MAX ( 'Filtro Mes'[FimMes] ),
    TODAY ()
)
```

### Medida personalizada de 12 meses

```DAX
Medida_12m_Personalizada =
VAR DataRef = [DataRef]
VAR FimPonto = MIN ( MAX ( D_Calendario[Date] ), DataRef )
VAR Visivel =
    MAX ( D_Calendario[Date] ) > EDATE ( EOMONTH ( DataRef, 0 ), -12 )
    && MIN ( D_Calendario[Date] ) <= DataRef
RETURN
    IF (
        Visivel,
        CALCULATE (
            [Medida],
            DATESINPERIOD ( D_Calendario[Date], FimPonto, -12, MONTH )
        )
    )
```

### Configuração do gráfico

- **Eixo X:** `D_Calendario[MesAnoTexto]`, classificado por `AnoMes`, com o tipo categórico (não use hierarquia de datas).
- **Valores:** `[Medida]`.
- **Segmentador:** `Filtro Mes[MesAnoTexto]`.

### Observações

- A tabela Calendário precisa ser marcada como tabela de datas e ter datas contínuas.
