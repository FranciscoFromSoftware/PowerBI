# ESTRUTURA DO TEMA DARK - Power BI
## Guia de Personalização por Seção

---

## 🎨 **1. CONFIGURAÇÕES BÁSICAS DO TEMA**

### **1.1 Informações Gerais**
```json
"name": "Dark Theme by Alison Pezzott v001",
"$schema": "reportThemeSchema-2.145.json"
```
- **O que altera**: Nome do tema e versão do schema
- **Cards afetados**: Todos os visuais

---

## 🌈 **2. PALETA DE CORES PRINCIPAL**

### **2.1 Cores de Dados (dataColors)**
```json
"dataColors": [
    "#FF8C00",    // Laranja principal
    "#dc7000",    // Laranja escuro
    "#8e3000",    // Laranja muito escuro
    "#00BFFF",    // Azul claro
    "#00619a",    // Azul escuro
    "#FFD8A8",    // Laranja claro
    "#292929",    // Cinza escuro
    "#404040"     // Cinza médio
]
```
- **O que altera**: Cores das séries de dados em gráficos
- **Cards afetados**: Gráficos de linha, coluna, pizza, área, etc.

### **2.2 Cores de Status**
```json
"good": "#48E552",      // Verde para indicadores positivos
"neutral": "#E5BB48",   // Amarelo para indicadores neutros
"bad": "#E55248"        // Vermelho para indicadores negativos
```
- **O que altera**: Cores dos indicadores de performance (KPIs)
- **Cards afetados**: Cartões KPI, indicadores de status

### **2.3 Cores de Gradiente**
```json
"maximum": "#404040",   // Cor máxima do gradiente
"center": "#00619a",    // Cor central do gradiente
"minimum": "#00BFFF"    // Cor mínima do gradiente
```
- **O que altera**: Cores dos gradientes e mapas de calor
- **Cards afetados**: Mapas de calor, gráficos com gradiente

---

## 🎯 **3. HIERARQUIA DE ELEMENTOS**

### **3.1 Níveis de Elementos**
```json
"firstLevelElements": "#FFFFFF",    // Texto principal (branco)
"secondLevelElements": "#BDC8DB",   // Texto secundário (cinza claro)
"thirdLevelElements": "#1A1A1A",    // Fundo terciário (preto)
"fourthLevelElements": "#B3B0AD"    // Elementos de menor importância
```
- **O que altera**: Hierarquia visual dos textos e elementos
- **Cards afetados**: Todos os visuais com texto

### **3.2 Cores de Fundo**
```json
"background": "#1A1A1A",           // Fundo principal dos visuais
"secondaryBackground": "#404040",  // Fundo secundário
"tableAccent": "#404040"           // Cor de destaque em tabelas
```
- **O que altera**: Fundos dos visuais e tabelas
- **Cards afetados**: Todos os visuais

---

## 📝 **4. CLASSES DE TEXTO (textClasses)**

### **4.1 Textos de Destaque**
```json
"callout": {
    "fontSize": 30,
    "fontFace": "wf_standard-font, wf_segoe-ui_semibold, helvetica, arial, sans-serif",
    "color": "#FFFFFF"
}
```
- **O que altera**: Números grandes e destaques
- **Cards afetados**: Cartões com números grandes, KPIs

### **4.2 Cabeçalhos e Títulos**
```json
"header": {
    "fontSize": 16,
    "fontFace": "wf_standard-font, wf_segoe-ui_semibold, helvetica, arial, sans-serif",
    "color": "#BDC8DB"
},
"title": {
    "fontSize": 12,
    "fontFace": "wf_standard-font, wf_segoe-ui_semibold, helvetica, arial, sans-serif",
    "color": "#BDC8DB"
}
```
- **O que altera**: Cabeçalhos e títulos dos visuais
- **Cards afetados**: Todos os visuais com títulos

### **4.3 Rótulos e Textos Gerais**
```json
"label": {
    "fontSize": 11,
    "fontFace": "DIN Light, wf_segoe-ui_normal, helvetica, arial, sans-serif",
    "color": "#FFFFFF"
},
"lightLabel": {
    "fontSize": 11,
    "fontFace": "DIN Light, wf_segoe-ui_normal, helvetica, arial, sans-serif",
    "color": "#BDC8DB"
}
```
- **O que altera**: Rótulos e textos menores
- **Cards afetados**: Todos os visuais com rótulos

---

## 🎨 **5. ESTILOS GLOBAIS DOS VISUAIS**

### **5.1 Cabeçalho do Visual**
```json
"visualHeader": [{"show": true}]
```
- **O que altera**: Exibição do cabeçalho dos visuais
- **Cards afetados**: Todos os visuais

### **5.2 Quebra de Linha**
```json
"wordWrap": [{"show": false}]
```
- **O que altera**: Quebra automática de linha em textos
- **Cards afetados**: Todos os visuais com texto

### **5.3 Proporção Fixa**
```json
"lockAspect": [{"show": true}]
```
- **O que altera**: Opção de travar proporção ao redimensionar
- **Cards afetados**: Todos os visuais

---

## 🎯 **6. RÓTULOS DE DADOS**

### **6.1 Rótulos de Dados**
```json
"datalabels": [{
    "color": {"solid": {"color": "#FFFFFF"}}
}]
```
- **O que altera**: Cor dos rótulos de dados nos gráficos
- **Cards afetados**: Gráficos com rótulos de dados

### **6.2 Rótulos de Total**
```json
"totalLabels": [{"show": true}]
```
- **O que altera**: Exibição dos rótulos de total
- **Cards afetados**: Gráficos com totais

---

## 🎨 **7. APARÊNCIA DOS VISUAIS**

### **7.1 Título do Visual**
```json
"title": [{
    "show": true,
    "fontColor": {"solid": {"color": "#BDC8DB"}},
    "alignment": "left"
}]
```
- **O que altera**: Título dos visuais
- **Cards afetados**: Todos os visuais com título

### **7.2 Fundo do Visual**
```json
"background": [{
    "show": true,
    "color": {"solid": {"color": "#292929"}},
    "transparency": 0
}]
```
- **O que altera**: Fundo dos visuais
- **Cards afetados**: Todos os visuais

### **7.3 Borda do Visual**
```json
"border": [{
    "show": true,
    "color": {"solid": {"color": "#404040"}},
    "radius": 8
}]
```
- **O que altera**: Borda dos visuais
- **Cards afetados**: Todos os visuais

### **7.4 Sombra do Visual**
```json
"dropShadow": [{
    "show": true,
    "color": {"solid": {"color": "#010204"}},
    "preset": "Custom",
    "shadowSpread": 1,
    "shadowBlur": 8,
    "angle": 90,
    "shadowDistance": 4,
    "transparency": 50
}]
```
- **O que altera**: Sombra dos visuais
- **Cards afetados**: Todos os visuais

---

## 📊 **8. LEGENDA**

### **8.1 Configuração da Legenda**
```json
"legend": [{
    "show": true,
    "position": "Top",
    "showTitle": false,
    "color": {"solid": {"color": "#BDC8DB"}},
    "legendMarkerRendering": "lineAndMarker",
    "matchLineColor": true
}]
```
- **O que altera**: Legenda dos gráficos
- **Cards afetados**: Gráficos com legenda

---

## 🏷️ **9. RÓTULOS E EIXOS**

### **9.1 Rótulos Gerais**
```json
"labels": [{
    "show": true,
    "labelOrientation": "horizontal",
    "labelDisplayUnits": 1,
    "labelPrecision": 0,
    "labelDensity": 30,
    "enableBackground": true,
    "backgroundColor": {"solid": {"color": "#0B0F1B"}},
    "backgroundTransparency": 50,
    "color": {"solid": {"color": "#FFFFFF"}},
    "fillcolor": {"solid": {"color": "#FFFFFF"}}
}]
```
- **O que altera**: Rótulos dos dados
- **Cards afetados**: Gráficos com rótulos

### **9.2 Eixo Categórico**
```json
"categoryAxis": [{
    "axisType": "Categorical",
    "showAxisTitle": false,
    "axisStyle": "showTitleOnly",
    "concatenateLabels": false,
    "labelColor": {"solid": {"color": "#BDC8DB"}},
    "preferredCategoryWidth": 20,
    "maxMarginFactor": 50,
    "innerPadding": 22,
    "gridlineShow": false,
    "gridlineColor": {"solid": {"color": "#181B2A"}},
    "gridlineThickness": 0.5,
    "gridlineStyle": "solid"
}]
```
- **O que altera**: Eixo X (categorias) dos gráficos
- **Cards afetados**: Gráficos com eixo categórico

### **9.3 Eixo de Valores**
```json
"valueAxis": [{
    "showAxisTitle": false,
    "titleFontSize": 10,
    "fontSize": 10,
    "axisStyle": "showTitleOnly",
    "labelDisplayUnits": 1,
    "labelPrecision": 0,
    "labelColor": {"solid": {"color": "#BDC8DB"}},
    "gridlineShow": false,
    "gridlineColor": {"solid": {"color": "#EAEAEA"}},
    "gridlineThickness": 0,
    "gridlineStyle": "dashed"
}]
```
- **O que altera**: Eixo Y (valores) dos gráficos
- **Cards afetados**: Gráficos com eixo de valores

---

## 💬 **10. TOOLTIP (DICA DE FERRAMENTA)**

### **10.1 Configuração do Tooltip**
```json
"visualTooltip": [{
    "show": true,
    "type": "Default",
    "color": {"solid": {"color": "#FFFFFF"}},
    "backgroundColor": {"solid": {"color": "#0B0F1B"}},
    "labelColor": {"solid": {"color": "#FFFFFF"}},
    "valueColor": {"solid": {"color": "#FFFFFF"}},
    "titleFontColor": {"solid": {"color": "#FFFFFF"}},
    "valueFontColor": {"solid": {"color": "#FFFFFF"}},
    "background": {"solid": {"color": "#292929"}},
    "transparency": 15
}]
```
- **O que altera**: Dicas de ferramenta ao passar o mouse
- **Cards afetados**: Todos os visuais

---

## 📄 **11. PÁGINA DO RELATÓRIO**

### **11.1 Fundo da Página**
```json
"page": {
    "*": {
        "background": [{
            "color": {"solid": {"color": "#1A1A1A"}},
            "transparency": 0
        }]
    }
}
```
- **O que altera**: Fundo da página do relatório
- **Cards afetados**: Página inteira

### **11.2 Cartões de Filtro**
```json
"filterCard": [
    {
        "$id": "Applied",
        "backgroundColor": {"solid": {"color": "#292929"}},
        "foregroundColor": {"solid": {"color": "#FFFFFF"}},
        "borderColor": {"solid": {"color": "#181B2A"}},
        "inputBoxColor": {"solid": {"color": "#1A1A1A"}}
    },
    {
        "$id": "Available",
        "backgroundColor": {"solid": {"color": "#1A1A1A"}},
        "foregroundColor": {"solid": {"color": "#FFFFFF"}},
        "borderColor": {"solid": {"color": "#181B2A"}},
        "inputBoxColor": {"solid": {"color": "#1A1A1A"}}
    }
]
```
- **O que altera**: Aparência dos filtros
- **Cards afetados**: Painel de filtros

---

## 📊 **12. VISUAIS ESPECÍFICOS**

### **12.1 Gráfico de Linha (lineChart)**
```json
"lineChart": {
    "lineStyles": [{
        "strokeWidth": 3,
        "strokeLineJoin": "bevel",
        "lineStyle": "solid",
        "showMarker": true,
        "markerShape": "circle",
        "markerSize": 7
    }],
    "plotArea": [{"transparency": 20}]
}
```
- **O que altera**: Estilo das linhas e marcadores
- **Cards afetados**: Gráficos de linha

### **12.2 Caixa de Texto (textbox)**
```json
"textbox": {
    "*": {
        "*": [{
            "fontSize": 11,
            "fontFamily": "DIN Light, wf_segoe-ui_normal, helvetica, arial, sans-serif",
            "wordWrap": true
        }]
    }
}
```
- **O que altera**: Estilo do texto nas caixas de texto
- **Cards afetados**: Caixas de texto

### **12.3 Filtro (slicer)**
```json
"slicer": {
    "selection": [{
        "selectAllCheckboxEnabled": false,
        "singleSelect": true,
        "strictSingleSelect": false
    }],
    "slider": [{
        "secondaryLineColor": {"solid": {"color": "#181B2A"}},
        "handleBorderColor": {"solid": {"color": "#181B2A"}},
        "handleFillColor": {"solid": {"color": "#181B2A"}},
        "color": {"solid": {"color": "#BDC8DB"}}
    }]
}
```
- **O que altera**: Aparência dos filtros
- **Cards afetados**: Filtros e slicers

### **12.4 Gráfico de Rosquinha (donutChart)**
```json
"donutChart": {
    "slices": [{"innerRadiusRatio": 80}],
    "labels": [{
        "labelStyle": "Category, percent of total",
        "percentageLabelPrecision": 1
    }]
}
```
- **O que altera**: Espessura da rosquinha e rótulos
- **Cards afetados**: Gráficos de pizza/rosquinha

### **12.5 Tabela (tableEx)**
```json
"tableEx": {
    "grid": [{
        "gridVertical": false,
        "gridHorizontal": true,
        "gridHorizontalColor": {"solid": {"color": "#1A1A1A"}},
        "outlineColor": {"solid": {"color": "#5046E5"}},
        "outlineWeight": 1
    }],
    "columnHeaders": [{
        "fontColor": {"solid": {"color": "#FFFFFF"}},
        "backColor": {"solid": {"color": "#0B0F1B"}},
        "alignment": "Center"
    }],
    "values": [{
        "fontColorPrimary": {"solid": {"color": "#FFFFFF"}},
        "backColorPrimary": {"solid": {"color": "#0B0F1B"}}
    }],
    "total": [{
        "fontColor": {"solid": {"color": "#00B8DD"}},
        "backColor": {"solid": {"color": "#00AA22"}}
    }]
}
```
- **O que altera**: Aparência das tabelas
- **Cards afetados**: Tabelas

---

## 🎯 **COMO USAR ESTE GUIA**

1. **Para alterar cores**: Procure a seção correspondente e modifique os valores hex
2. **Para alterar fontes**: Modifique as propriedades `fontFace` nas seções de texto
3. **Para alterar tamanhos**: Modifique as propriedades `fontSize`
4. **Para alterar visuais específicos**: Procure a seção do visual desejado
5. **Para alterar comportamento**: Modifique propriedades como `show`, `transparency`, etc.

---

## 📝 **NOTAS IMPORTANTES**

- **Cores hex**: Use formato `#RRGGBB` (ex: `#FF8C00`)
- **Transparência**: 0 = opaco, 100 = transparente
- **Tamanhos de fonte**: Valores em pontos (pt)
- **Posições**: "Top", "Bottom", "Left", "Right"
- **Alinhamentos**: "left", "center", "right"
