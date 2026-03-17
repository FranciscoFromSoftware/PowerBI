
# Visuais para tabela

## Comparação com o valor da diferença


Visual Vendas Filial Final v2 = 
VAR Actual = [Vendas_AnoAtual]
VAR Target = [Vendas_AnoAnterior_Acumulado]
VAR Diff = Actual - Target

 1. Lógica de Abreviatura com R$
VAR _AbsDiff = ABS(Diff)
VAR _ValorAbreviado = 
    SWITCH(TRUE(),
        _AbsDiff >= 1000000, FORMAT(_AbsDiff/1000000, "#,0.0M"),
        _AbsDiff >= 1000, FORMAT(_AbsDiff/1000, "#,0.0K"),
        FORMAT(_AbsDiff, "#,0")
    )
VAR _Sinal = IF(Diff > 0, "+", IF(Diff < 0, "-", ""))
-- Texto final com R$ e o sinal
VAR _TextoFinal = "R$ " & _Sinal & _ValorAbreviado

2. Configurações de Cor e Posição
VAR _CorTexto = IF(Diff < 0, "#E53E3E", "#38A169")
VAR _IdGradiente = IF(Actual < Target, "url(#gradRuim)", "url(#gradBom)")
VAR _PosicaoTarget = 80 -- Posição da linha preta
VAR _FillWidth = DIVIDE(Actual, Target, 0) * _PosicaoTarget
VAR _FillWidthVisual = MIN(_FillWidth, 115) -- Limite da barra para não bater no texto

3. Formatação para o SVG
VAR _FillWidthText = SUBSTITUTE(FORMAT(_FillWidthVisual, "0.00"), ",", ".")
VAR _TargetText = SUBSTITUTE(FORMAT(_PosicaoTarget, "0.00"), ",", ".")

RETURN
IF(ISBLANK(Actual) && ISBLANK(Target), BLANK(),
    "data:image/svg+xml;utf8," & 
    "<svg width='220' height='40' viewBox='0 0 220 40' xmlns='http://www.w3.org/2000/svg' display='block'>
        <defs>
            <linearGradient id='gradBom' x1='0%' y1='0%' x2='100%' y2='0%'>
                <stop offset='0%' stop-color='#F6E05E'/>
                <stop offset='100%' stop-color='#38A169'/>
            </linearGradient>
            <linearGradient id='gradRuim' x1='0%' y1='0%' x2='100%' y2='0%'>
                <stop offset='0%' stop-color='#F6E05E'/>
                <stop offset='100%' stop-color='#E53E3E'/>
            </linearGradient>
        </defs>

        <rect x='0' y='10' width='120' height='20' rx='10' fill='#F2F2F2' />
        
        <rect x='0' y='10' width='" & _FillWidthText & "' height='20' rx='10' fill='" & _IdGradiente & "' />
        
        <rect x='" & _TargetText & "' y='5' width='2' height='30' rx='1' fill='black' />

        <text x='130' y='26' fill='" & _CorTexto & "' font-family='Segoe UI, Arial' font-size='18' font-weight='bold'>" & _TextoFinal & "</text>
    </svg>"
)


# Visual Temporal
## Gráfico de Linhas por Horas
Sparkline Horas Pro Max = 
1. Definição de Cores e Estilo
VAR topLineColour = "#38A169" 
VAR bottomLineColour = "#E53E3E"
VAR _StrokeWidth = "4" -- Linha bem grossa

2. Referência de Tempo (Eixo X)
VAR XMinHora = 0
VAR XMaxHora = 23

-- 3. Referência de Valores (Eixo Y)
VAR _TabelaHoras = VALUES(vendas_detalhes[Hora])
VAR YMinValue = MINX(_TabelaHoras, CALCULATE([SUM Profit]))
VAR YMaxValue = MAXX(_TabelaHoras, CALCULATE([SUM Profit]))

4. Construção das Coordenadas
VAR _Largura = 250
VAR _Altura = 60 -- Área do gráfico
VAR _MargemEixo = 30 -- Aumentado para comportar fonte 18
VAR _TotalAltura = _Altura + _MargemEixo

VAR SparklineTable = ADDCOLUMNS(
    SUMMARIZE(vendas_detalhes, vendas_detalhes[Hora]),
        "X", INT(_Largura * DIVIDE(vendas_detalhes[Hora] - XMinHora, XMaxHora - XMinHora)),
        "Y", INT(_Altura * DIVIDE([SUM Profit] - YMinValue, YMaxValue - YMinValue)))

5. Concatena os pontos
VAR Lines = CONCATENATEX(SparklineTable, [X] & "," & _Altura-[Y], " ", vendas_detalhes[Hora])

6. Interseção (Média)
VAR lineIntersection = AVERAGEX(_TabelaHoras, CALCULATE([SUM Profit]))
VAR intersectScaled = _Altura - INT(_Altura * DIVIDE(lineIntersection - YMinValue, YMaxValue - YMinValue))

7. Eixo de Horas (FONTE AUMENTADA PARA 18)
VAR EixoX = 
    CONCATENATEX(
        {0, 6, 12, 18, 23},
        VAR _H = [Value]
        VAR _PosX = INT(_Largura * DIVIDE(_H - XMinHora, XMaxHora - XMinHora))
        -- Ajustado y para _TotalAltura para não cortar
        RETURN "<text x='" & _PosX & "' y='" & _TotalAltura & "' fill='#555' font-family='Segoe UI, Arial' font-size='18' font-weight='bold' text-anchor='middle'>" & _H & "h</text>"
    )

8. Definições de Máscara
VAR Defs = 
"<defs>
    <clipPath id='cut-top-pro'>
      <rect x='0' y='0' width='" & _Largura & "' height='" & intersectScaled & "' />
    </clipPath>
</defs>"

9. Montagem Final
VAR SVGImageURL = 
"data:image/svg+xml;utf8," & 
"<svg xmlns='http://www.w3.org/2000/svg' viewBox='-15 -10 " & _Largura + 30 & " " & _TotalAltura + 15 & "' >" & Defs &
    -- Eixo horizontal
    "<line x1='0' y1='" & _Altura & "' x2='" & _Largura & "' y2='" & _Altura & "' stroke='#D1D1D1' stroke-width='1.5' />" &
    
    -- Linha de Baixo (Vermelha)
    "<polyline fill='none' stroke='" & bottomLineColour & "' stroke-width='" & _StrokeWidth & "' points='" & Lines & "' stroke-linecap='round' stroke-linejoin='round' />" &
    
    -- Linha de Cima (Verde - Clipada)
    "<polyline fill='none' stroke='" & topLineColour & "' stroke-width='" & _StrokeWidth & "' points='" & Lines & "' stroke-linecap='round' stroke-linejoin='round' clip-path='url(#cut-top-pro)' />" &
    
    -- Linha da Média
    "<line x1='0' y1='" & intersectScaled & "' x2='" & _Largura & "' y2='" & intersectScaled & "' stroke='grey' stroke-width='1.5' stroke-dasharray='4,3' opacity='0.5' />" &
    
    -- Rótulos das Horas
    EixoX &
"</svg>"

RETURN 
IF(HASONEVALUE(vendas_detalhes[Filial]), SVGImageURL, BLANK())



## Visual de Calendario
HeatmapVenda1s SVG = 
-- 1. CONFIGURAÇÕES
VAR vCor0 = "#F0F9FF" 
VAR vCor1 = "#E4F8FF" 
VAR vCor2 = "#90E0EF" 
VAR vCor3 = "#0077B6" 
VAR vCor4 = "#03045E" 

VAR vTamanho = 26   
VAR vEspacoX = 35   
VAR vEspacoY = 35   
VAR vRaio    = 4    
VAR vOffsetY = 80   
VAR vOffsetX = 30   
VAR vSVGLargura = 300 

-- 2. TABELA DE DADOS
VAR vTabelaDias =
    ADDCOLUMNS(
        SUMMARIZE(
            'dCalendario',
            'dCalendario'[Date],
            'dCalendario'[Dia],
            'dCalendario'[DiaSemana] -- Certifique-se que Dom=0, Seg=1... Sáb=6
        ),
        "@Vendas", [ValorVenda],
        -- AJUSTE LOGICO: Criamos um índice de semana que só muda APÓS o sábado
        "@SemanaID", 
            VAR vData = 'dCalendario'[Date]
            VAR vInicioPeriodo = MINX(ALLSELECTED('dCalendario'), 'dCalendario'[Date])
            RETURN QUOTIENT(DATEDIFF(vInicioPeriodo, vData, DAY) + WEEKDAY(vInicioPeriodo, 1) - 1, 7)
    )

VAR vVendasComValor = FILTER( vTabelaDias, [@Vendas] > 0 )
VAR vMaxVendas = MAXX( vVendasComValor, [@Vendas] )
VAR vTotalPeriodo = SUMX( vVendasComValor, [@Vendas] )
VAR vSemanaMin = MINX( vTabelaDias, [@SemanaID] )
VAR vSemanaMax = MAXX( vTabelaDias, [@SemanaID] )
VAR vQtdSemanas = vSemanaMax - vSemanaMin + 1
VAR vSVGAltura = vOffsetY + (vQtdSemanas * vEspacoY) + 60

-- 3. CABEÇALHO
VAR vNomesDiasTopo = 
    "<g font-family='Arial' font-size='10' fill='#666' font-weight='bold' text-anchor='middle'>" &
        "<text x='" & vOffsetX + (0 * vEspacoX) + 13 & "' y='" & vOffsetY - 15 & "'>D</text>" &
        "<text x='" & vOffsetX + (1 * vEspacoX) + 13 & "' y='" & vOffsetY - 15 & "'>S</text>" &
        "<text x='" & vOffsetX + (2 * vEspacoX) + 13 & "' y='" & vOffsetY - 15 & "'>T</text>" &
        "<text x='" & vOffsetX + (3 * vEspacoX) + 13 & "' y='" & vOffsetY - 15 & "'>Q</text>" &
        "<text x='" & vOffsetX + (4 * vEspacoX) + 13 & "' y='" & vOffsetY - 15 & "'>Q</text>" &
        "<text x='" & vOffsetX + (5 * vEspacoX) + 13 & "' y='" & vOffsetY - 15 & "'>S</text>" &
        "<text x='" & vOffsetX + (6 * vEspacoX) + 13 & "' y='" & vOffsetY - 15 & "'>S</text>" &
    "</g>"

-- 4. QUADRADOS
VAR vQuadrados =
    CONCATENATEX(
        vTabelaDias,
        VAR vX = vOffsetX + ('dCalendario'[DiaSemana] * vEspacoX)
        VAR vSemanaRelativa = [@SemanaID] - vSemanaMin
        VAR vY = vOffsetY + (vSemanaRelativa * vEspacoY)
        
        VAR vVendas = [@Vendas]
        VAR vPercentual = DIVIDE(vVendas, vMaxVendas, 0)
        VAR vCorFundo = SWITCH(TRUE(), ISBLANK(vVendas) || vVendas = 0, vCor0, vPercentual <= 0.25, vCor1, vPercentual <= 0.50, vCor2, vPercentual <= 0.75, vCor3, vCor4 )
        VAR vCorTexto = IF(vPercentual > 0.5, "white", "#555")
        
        RETURN
            "<g>" &
                "<rect x='" & vX & "' y='" & vY & "' width='" & vTamanho & "' height='" & vTamanho & "' rx='" & vRaio & "' fill='" & vCorFundo & "'>" &
                "<title>" & FORMAT('dCalendario'[Date], "dd/MM") & " | R$ " & ROUND(vVendas, 0) & "</title></rect>" &
                "<text x='" & vX + 13 & "' y='" & vY + 17 & "' font-family='Arial' font-size='10' font-weight='bold' text-anchor='middle' fill='" & vCorTexto & "' pointer-events='none'>" & 'dCalendario'[Dia] & "</text>" &
            "</g>",
        "",
        'dCalendario'[Date], ASC
    )

RETURN
"data:image/svg+xml;utf8,<svg width='" & vSVGLargura & "' height='" & vSVGAltura & "' viewBox='0 0 " & vSVGLargura & " " & vSVGAltura & "' xmlns='http://www.w3.org/2000/svg'>" &
    "<text x='20' y='30' font-family='Arial' font-size='18' font-weight='bold' fill='#111'>Vendas</text>" &
    vNomesDiasTopo &
    vQuadrados &
    "<text x='20' y='" & vSVGAltura - 20 & "' font-family='Arial' font-size='12' font-weight='bold' fill='#03045E'>Total: R$ " & FORMAT(vTotalPeriodo, "#,##0") & "</text>" &
"</svg>"