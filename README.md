# Dashboard Power BI — Mercado de Construção EUA (Amorim Cork Solutions)

Projeto **Power BI Project (PBIP)** gerado a partir de `Modelo_Dados_Mercado_EUA_PowerBI.xlsx` segundo o `Guia_Dashboard_PowerBI_ACS.md`. O semantic model está em **TMDL** e o report em **PBIR** (formatos de texto, prontos para git).

```
DashboardAmorim.pbip                  ← abre isto no Power BI Desktop
DashboardAmorim.SemanticModel/        ← modelo: tabelas, relações, medidas (TMDL)
DashboardAmorim.Report/               ← report: 5 páginas e visuais (PBIR)
Modelo_Dados_Mercado_EUA_PowerBI.xlsx ← os dados (inalterados)
```

## Como abrir

1. Instala um **Power BI Desktop recente** (Microsoft Store; versão ≥ mar/2024).
2. Se for uma versão mais antiga, ativa em **File → Options → Preview features**:
   - *Store semantic model using TMDL format*
   - *Store reports using enhanced metadata format (PBIR)*
   e reinicia o Desktop.
3. Faz duplo clique em **`DashboardAmorim.pbip`**.

## Primeiro passo obrigatório: apontar para o Excel

O Power Query não suporta caminhos relativos, por isso o caminho do Excel vive num **único parâmetro**:

1. Ao abrir, se aparecer erro de atualização, ignora e vai a **Home → Transform data → Edit parameters**.
2. Em **`CaminhoExcel`** cola o caminho completo do ficheiro no teu PC, p. ex.
   `C:\Users\joao\Documents\dashboardAmorim\Modelo_Dados_Mercado_EUA_PowerBI.xlsx`
   (o valor por omissão é `C:\dashboardAmorim\Modelo_Dados_Mercado_EUA_PowerBI.xlsx`).
3. **Home → Refresh**. Todas as 16 tabelas carregam deste único caminho.

## Atualizar dados quando o Excel mudar

Substitui o `.xlsx` (mesmo nome, mesmas folhas e colunas) e faz **Home → Refresh**. Nada mais é preciso — as medidas e visuais recalculam sozinhos. Se renomeares folhas ou colunas no Excel, o Power Query dá erro (os nomes estão fixados no código M de cada tabela).

## O que está 100% implementado e validado

**Modelo (a parte crítica) — validado com o parser oficial da Microsoft (AMO/TMDL):**

- **17 tabelas**: as 10 `Dim_` + 6 `Facts_` importadas do Excel (partições Power Query) e a `Dim_Calendario` como *calculated table* com o DAX exato do guia, marcada como **date table** (coluna `Date` como chave, `Mes` ordenado por `MesNum`).
- **21 relações**, todas *One-to-many* (dimensão no lado 1), *cross-filter Single*, ativas:
  - as 9 relações dimensão→facto do guia;
  - `Dim_Fonte[FonteID]` → cada uma das 6 tabelas de factos;
  - `Dim_Calendario[Date]` → coluna `Data` de cada uma das 6 tabelas de factos.
- **As 10 medidas DAX do guia**, com formato: `Valor Mercado`, `Despesa Constr ($M)`, `Permits (milhares)`, `Valor Permits ($M)`, `Indicador Macro`, `Indice CaseShiller`, `Preco Medio Unidade ($k)`, `Valorizacao desde 2000 (%)`, `Despesa Constr YoY %` e `Permits YoY %` (as YoY em percentagem `0.0%`, as monetárias com separador de milhares).
- `Dim_Estado[Estado]` com **Data category = State or Province** (para o mapa).
- Auto date/time desligado no modelo (usa-se sempre a `Dim_Calendario`).
- Integridade verificada nos dados: chaves únicas em todas as dimensões e **zero valores órfãos** nas 15 relações dimensão↔facto.

**Report:** as 5 páginas do guia — *Funil de Mercado, Evolução & Ciclo, Geografia, Drivers Macro, Procura por Segmento* — com 21 visuais, cada um já com os filtros de `Dim_Metrica`/`Dim_Segmento`/`Frequência`/`TipoEstrutura` indicados no guia. Todos os JSON foram validados contra os JSON Schemas oficiais (schemas `visualContainer 2.0.0`, `page 2.0.0`, `report 3.1.0`).

## O que precisa de retoque manual (visuais = best-effort)

| # | Onde | O quê |
|---|------|-------|
| 1 | Funil de Mercado — barras | O guia pede Despesa Constr por `Dim_Segmento`, mas não existe relação entre `Dim_Segmento` e `Facts_ConstrSpend` (unidades diferentes do star schema). Implementei com `Dim_Categoria[Categoria]` filtrada a *Residential / Health care / Lodging* — o mesmo resultado visual (Residencial vs Saúde vs Hotelaria), filtrado a 2025 + Frequência Anual. |
| 2 | Funil — treemap | Exclui os agregados *Total Construction, Residential, Nonresidential* e *Health care (private)* para evitar dupla contagem. Ajusta o filtro do visual se quiseres outra seleção. |
| 3 | Geografia — top estados | O filtro **Top N = 10** tem de ser concluído à mão: seleciona o visual → painel Filtros → `Estado` (tipo Top N) → escreve 10 e arrasta `Permits (milhares)` para "Por valor" → Aplicar. (A serialização automática deste filtro é frágil em PBIR, preferi deixá-lo preparado.) |
| 4 | Geografia — mapa | Se aparecer aviso de mapas, ativa **Options → Security → Use Map and Filled Map visuals**. Se a cor não aparecer, arrasta `Permits (milhares)` para o poço de cor do Filled map. |
| 5 | Drivers Macro — linhas | As duas taxas (crédito habitação 30 anos + taxa diretora Fed) estão no mesmo gráfico de linhas via legenda `Dim_Indicador` — o indicador "Fed Funds" do guia chama-se **"Taxa diretora Fed"** nos dados. A variante "Line and stacked column" com a despesa sobreposta fica para fazer no Desktop se a quiseres (troca o tipo de visual e arrasta `Despesa Constr ($M)` para as colunas). |
| 6 | Drivers Macro — cartões | Mostram a **média do período visível** (a medida do guia é AVERAGE). Para leres o valor "atual", filtra o cartão aos últimos meses (filtro de data relativa) ou usa um slicer de ano. |
| 7 | Calendário | O DAX do guia começa em **2000**: dados anteriores (Case-Shiller desde 1987, permits desde 1990, despesa desde 1993) não aparecem em eixos baseados na `Dim_Calendario`. Para os incluir, edita a tabela `Dim_Calendario` e troca `DATE(2000,1,1)` por `DATE(1987,1,1)`. |
| 8 | Medida `Valorizacao desde 2000 (%)` | Devolve pontos percentuais (índice 448 → 348, leia-se +348%). Ficou com formato numérico simples — **não** apliques formato *Percentage* (multiplicaria por 100). |
| 9 | Tema | O tema base é o standard. Para as cores CPBS cria um tema personalizado em **View → Themes** com navy `#1B3A7A`, laranja `#C8602A`, teal `#009999`. |
| 10 | SOM | O cartão SOM filtra a métrica `SOM (target 5 anos)` (nome exato nos dados) e está em **$M** — os cartões TAM/SAM estão em $bn; os títulos dos cartões já o indicam. |

Nota de dupla contagem: `Facts_Permits` e `Facts_PermitsState` têm linhas *Total* **e** por tipo de estrutura — qualquer visual novo sobre estas tabelas deve filtrar `TipoEstrutura` (os visuais gerados já o fazem). O mesmo para `Facts_ConstrSpend`, que mistura `Frequência` *Mensal* e *Anual*.

## Como foi validado (e como revalidar)

- **TMDL**: desserializado com o parser oficial `TmdlSerializer` (NuGet `Microsoft.AnalysisServices.NetCore.retail.amd64` 19.84.1, .NET 8) — 17 tabelas, 21 relações e 10 medidas reconhecidas sem erros. Para revalidar: instala o .NET SDK 8, cria um projeto de consola com esse pacote e chama `TmdlSerializer.DeserializeDatabaseFromFolder("DashboardAmorim.SemanticModel/definition")`.
- **PBIR/PBIP**: todos os 34 ficheiros JSON validados contra os JSON Schemas oficiais publicados em `github.com/microsoft/json-schemas`.
- `pbi-tools` não foi usado: só corre em Windows com o Power BI Desktop instalado. Em alternativa, no Windows, o **Tabular Editor 2** (grátis, ≥ 2.21) abre diretamente a pasta `definition/` do semantic model para inspeção.
- A validação final de sempre: abrir o `.pbip` no Power BI Desktop — na primeira gravação o Desktop pode reescrever/normalizar os ficheiros, o que é esperado.
