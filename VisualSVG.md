
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