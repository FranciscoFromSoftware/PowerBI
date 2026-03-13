
# Visuais para tabela
## Comparação com o valor da diferença
Visual Vendas Filial Final v2 = 
VAR Actual = [Vendas_AnoAtual]
VAR Target = [Vendas_AnoAnterior_Acumulado]
VAR Diff = Actual - Target

-- 1. Lógica de Abreviatura com R$
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

-- 2. Configurações de Cor e Posição
VAR _CorTexto = IF(Diff < 0, "#E53E3E", "#38A169")
VAR _IdGradiente = IF(Actual < Target, "url(#gradRuim)", "url(#gradBom)")

-- Ajuste de proporção para dar espaço ao texto maior
VAR _PosicaoTarget = 80 -- Posição da linha preta
VAR _FillWidth = DIVIDE(Actual, Target, 0) * _PosicaoTarget
VAR _FillWidthVisual = MIN(_FillWidth, 115) -- Limite da barra para não bater no texto

-- 3. Formatação para o SVG
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
-- 1. Definição de Cores e Estilo
VAR topLineColour = "#38A169" 
VAR bottomLineColour = "#E53E3E"
VAR _StrokeWidth = "4" -- Linha bem grossa

-- 2. Referência de Tempo (Eixo X)
VAR XMinHora = 0
VAR XMaxHora = 23

-- 3. Referência de Valores (Eixo Y)
VAR _TabelaHoras = VALUES(vendas_detalhes[Hora])
VAR YMinValue = MINX(_TabelaHoras, CALCULATE([SUM Profit]))
VAR YMaxValue = MAXX(_TabelaHoras, CALCULATE([SUM Profit]))

-- 4. Construção das Coordenadas
VAR _Largura = 250
VAR _Altura = 60 -- Área do gráfico
VAR _MargemEixo = 30 -- Aumentado para comportar fonte 18
VAR _TotalAltura = _Altura + _MargemEixo

VAR SparklineTable = ADDCOLUMNS(
    SUMMARIZE(vendas_detalhes, vendas_detalhes[Hora]),
        "X", INT(_Largura * DIVIDE(vendas_detalhes[Hora] - XMinHora, XMaxHora - XMinHora)),
        "Y", INT(_Altura * DIVIDE([SUM Profit] - YMinValue, YMaxValue - YMinValue)))

-- 5. Concatena os pontos
VAR Lines = CONCATENATEX(SparklineTable, [X] & "," & _Altura-[Y], " ", vendas_detalhes[Hora])

-- 6. Interseção (Média)
VAR lineIntersection = AVERAGEX(_TabelaHoras, CALCULATE([SUM Profit]))
VAR intersectScaled = _Altura - INT(_Altura * DIVIDE(lineIntersection - YMinValue, YMaxValue - YMinValue))

-- 7. Eixo de Horas (FONTE AUMENTADA PARA 18)
VAR EixoX = 
    CONCATENATEX(
        {0, 6, 12, 18, 23},
        VAR _H = [Value]
        VAR _PosX = INT(_Largura * DIVIDE(_H - XMinHora, XMaxHora - XMinHora))
        -- Ajustado y para _TotalAltura para não cortar
        RETURN "<text x='" & _PosX & "' y='" & _TotalAltura & "' fill='#555' font-family='Segoe UI, Arial' font-size='18' font-weight='bold' text-anchor='middle'>" & _H & "h</text>"
    )

-- 8. Definições de Máscara
VAR Defs = 
"<defs>
    <clipPath id='cut-top-pro'>
      <rect x='0' y='0' width='" & _Largura & "' height='" & intersectScaled & "' />
    </clipPath>
</defs>"

-- 9. Montagem Final
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
