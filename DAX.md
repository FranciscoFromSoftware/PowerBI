# Snipets Power BI

## Calendário com o dia da semana em português

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

## Indíce e Ranks

- **Adicionar um rank de Classificação**: RANKX fómula para gerar o Rank começando pelas colunas de categoria que o rank vai considerar, no exemplo estou usando o formato Vendedor e Filial ou seja vai criar um RANK com base na filial e vendedor.
Ranking = 
RANKX(
    ALL('Planilha1'[Vendedor],Planilha1[Filial]),
    CALCULATE(SUM('Planilha1'[Valor da Venda (R$)]))
    ,,
    DESC,
    Dense
)



## Formatação de dados

### **Medida com icones de status
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

Ícone Ranking = 
SWITCH(
    TRUE(),
    [Ranking] = 1, "🏆",
    [Ranking] = 2, "🥈",
    [Ranking] = 3, "🥉",
    [Ranking] >= 4, "🏃‍♂️‍➡️",
    BLANK()
)

# symbl.cc tem mais opções para esolher setas


Dias Uteis =
NETWORDAYS(
    SELECTEDVALUE(dCalendario[Date]), -- data inicial
    SELECTEDVALUE(dCalendario[Date]), -- data final
    1, -- 1 = domingo é o primeiro dia da semana
    FERIADOS -- lista de feriados
)
--OBS: 1 = domingo é o primeiro dia da semana
--OBS: 2 = segunda é o primeiro dia da semana
--OBS: 11 = segunda é o primeiro dia da semana
--OBS: 12 = terça é o primeiro dia da semana
--OBS: 13 = quarta é o primeiro dia da semana
--OBS: 14 = quinta é o primeiro dia da semana
--OBS: 15 = sexta é o primeiro dia da semana
--OBS: 16 = sábado é o primeiro dia da semana

Função para tratar dados em branco
TratarDadosEmBranco =
    COALESCE(
        [Vendedor], 
        [Canal de Vendas]
)

-- OBS: COALESCE retorna o primeiro valor não nulo de uma lista de valores.
-- OBS: COALESCE é útil para tratar dados em branco ou nulos em colunas ou medidas.

Raqueando5itens = 
    RANKX(
        ALL(fDados[Vendedor]), -- tabela a ser ranqueada 
        [Vendas], --
        5, -- 5 = número de itens a serem ranqueados
        DESC, -- DESC = ordem decrescente
        DENSE -- DENSE = ranqueia itens com o mesmo valor com o mesmo número
    )

-- OBS: RANKX é útil para ranquear itens em uma tabela ou medida.

ConcatenarColunas =
    CONCATENATEX(
        VALUES(fDados[Vendedor]), -- tabela a ser concatenada
        fDados[Vendedor], -- coluna a ser concatenada
        ", " -- separador entre os itens concatenados
    )
-- OBS: CONCATENATEX é útil para concatenar itens em uma tabela ou medida.

Acumulado Vendas = 
    CALCULATE(
        SUM(fDados[Vendas]), -- soma das vendas
        DATESYTD(dCalendario[Date]), -- calcula o acumulado até a data atual
        ALL(dCalendario[Date]) -- remove o filtro da tabela de calendário
    )
-- OBS: DATESYTD calcula o acumulado até a data atual.
-- OBS: ALL remove o filtro da tabela de calendário para calcular o acumulado.


Valor e Porcetagem = 
VAR TotalGeral = 
    CALCULATE(
        COUNTROWS(DISTINCT('Linkedin_certificacoes_meus_dados'[nome])),
        REMOVEFILTERS(dim_Calendario)
    ) -- Calculo do total geral sem filtros de categoria data, para gerar o valor bruto.
VAR ValorAtual = [Medida 2] -- Calculo normal para rótulo de dados.
VAR Porcentagem = DIVIDE(ValorAtual, TotalGeral) Calculo da Porcetagem 

RETURN
FORMAT(ValorAtual, "#,##0") & " (" & FORMAT(Porcentagem, "0.0%") & ")"  -- Formatação dos  valores

-- OBS: Ranqueando 
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

Ranking = 
RANKX(
    ALL('Aplicativo_fMeuDia'[AppsGamesSoftware]),
    CALCULATE(SUM('Aplicativo_fMeuDia'[duration_seconds]))
    ,,
    DESC,
    Dense
)


# Análise temporal personalizada

Você precisa de uma tabela desconectada para ativar o filtro. Se usar a coluna de mês da própria tabela Calendário, o filtro corta o eixo do gráfico e você perde os 12 pontos.

Filtro Mes =
SUMMARIZE (
    Calendario,
    Calendario[AnoMes],        -- ex.: 202604 (número)
    Calendario[MesAnoTexto],   -- ex.: "abr/2026"
    "FimMes", MAX ( Calendario[Date] )
)
Em MesAnoTexto, use Classificar por coluna → AnoMes. Não crie relacionamento dessa tabela com nenhuma outra. Use Filtro Mes[MesAnoTexto] em um segmentador (slicer)

Data_Ref =
IF (
    ISFILTERED ( 'Filtro Mes'[MesAnoTexto] ),
    MAX ( 'Filtro Mes'[FimMes] ),
    TODAY ()
)

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

No gráfico
Eixo X: D_Calendario[MesAnoTexto], classificado por AnoMes, com o tipo categórico (não hierarquia de datas).
Valores: [Medida].
Segmentador: Filtro Mes[MesAnoTexto].

**Observações**
A tabela Calendário precisa ser marcada como tabela de datas e ter datas contínuas.
