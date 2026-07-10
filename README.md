# Dashboard Power BI — Mercado de Construção EUA (Amorim Cork Solutions)

Projeto **Power BI Project (PBIP)** gerado a partir de `Modelo_Dados_Mercado_EUA_PowerBI.xlsx` segundo o `Guia_Dashboard_PowerBI_ACS.md`. O semantic model está em **TMDL** e o report em **PBIR** (formatos de texto, prontos para git).

```
ACS.pbip                              ← abre isto no Power BI Desktop
ACS.SemanticModel/                    ← modelo: tabelas, relações, medidas (TMDL)
ACS.Report/                           ← report: 5 páginas + visuais (PBIR)
Modelo_Dados_Mercado_EUA_PowerBI.xlsx ← os dados (inalterados)
```

> Os nomes de pastas e ficheiros são curtos (`ACS.Report`, `pages/p1/visuals/p1v1/…`) de propósito: o PBIR cria pastas muito profundas e o Windows corta caminhos com mais de 260 caracteres.

## ⚠️ Descarregar sem cortar ficheiros (importante no Windows)

Se descarregares o ZIP do GitHub e extraíres para uma pasta funda (ex.: `Downloads`), o Windows pode dar **"Destination Path Too Long"** e **saltar ficheiros de visuais** — o dashboard abre com páginas vazias. Para evitar:

- **Melhor opção:** faz `git clone` para uma pasta curta, p. ex. `C:\pbi`:
  `git clone -b claude/powerbi-dashboard-excel-yyggon <url-do-repo> C:\pbi`
- **Se usares o ZIP:** extrai para a **raiz do disco** (`C:\`), não para `Downloads`. E se o Windows perguntar, escolhe **não** duplicar a pasta com o nome do ZIP.
- **Alternativa (uma vez):** ativar caminhos longos no Windows — `regedit` →
  `HKLM\SYSTEM\CurrentControlSet\Control\FileSystem` → `LongPathsEnabled` = `1`, e reiniciar.

Confirma que a pasta extraída tem `ACS.Report`, `ACS.SemanticModel` **e** o `ACS.pbip` lado a lado antes de abrir.

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

## Ronda 2 — melhorias orientadas à tese do capítulo (Â2 Mercado & Procura)

Depois da primeira versão funcional, o dashboard foi redesenhado para responder diretamente à tese tripla do capítulo (onde está o dinheiro · ritmo em inflexão · renovação always-on):

**Tema CPBS** aplicado a todo o report (teal `#009999`, laranja `#C8602A`, navy `#1B3A7A`).

**Novas medidas (8):**
| Medida | O que responde |
|---|---|
| `Despesa Anual ($M)` | Total anual por categoria — usa a linha Anual quando existe, senão soma os 12 meses (as categorias HHL são mensais; era isto que deixava o gráfico do funil vazio) |
| `Quota TAM no Mercado (%)` | Concentração: TAM $380bn ≈ 17% do mercado total (~$2 170bn) |
| `Quota HHL no Total (%)` | Peso de Lodging+Health care+Residential na construção total |
| `Quota Estado (%)` | Peso de cada estado no total nacional de licenças |
| `Indicador Atual` | Valor do indicador macro no último mês disponível (cartões "atual") |
| `Spread Credito-Fed (pp)` | Prémio do crédito habitação sobre a Fed — compressão = destrava procura |
| `CaseShiller Atual` / `CaseShiller YoY %` | Último valor do índice e variação homóloga |

**Mudanças por página:**
- **Funil de Mercado:** 4º cartão "TAM em % do mercado total"; barras e treemap passam à medida `Despesa Anual ($M)` (corrigido o visual vazio); rótulos dos cartões com unidade ($bn/$M).
- **Evolução & Ciclo:** novo gráfico de **variação homóloga (%)** — mostra a aceleração/desaceleração que suporta a leitura de ciclo; pipeline hoteleiro corrigido para usar só "Hotelaria — total" nacional (antes misturava subsegmentos e cidades — era o zigzag); slicer de ano em modo intervalo.
- **Geografia:** novo gráfico **valor de obra por unidade ($k)** (separa estados luxury de estados de volume — Tier 3 vs Tier 1); novo gráfico **pipeline hoteleiro por cidade** (Dallas, Atlanta, Phoenix, Nashville, Austin — o Sun Belt do capítulo); tooltip com quota nacional no top estados; slicer de tier em dropdown.
- **Drivers Macro:** cartões passam a mostrar o **último mês** (não a média do período); novos KPI **ABI <50 (13 meses)** e **Dodge Momentum (+37%)** — os dois números que sustentam "2026 setup → 2027 inflexão"; cartão do spread crédito−Fed.
- **Procura por Segmento:** novo gráfico do **défice de senior housing (156k → 806k até 2030)**; tabela de observações ganha colunas Ano e Unidade e perde a linha de total (somava unidades diferentes).
- **Calendário alargado a 1987** — o histórico completo do Case-Shiller (1987–) e dos permits (1990–) já aparece nos eixos temporais, e o "(Vazio)" desapareceu do slicer de ano.
- Página TESTE removida.

## Ronda 3 — retoques a partir das screenshots

- **Fim do precipício de −100% no gráfico YoY** (Evolução & Ciclo): as três medidas YoY (`Despesa Constr YoY %`, `Permits YoY %`, `CaseShiller YoY %`) devolvem agora BLANK quando não há dados no período atual — o calendário vai até 2031 mas os dados acabam antes, e esses meses apareciam como −100%.
- **Cartões macro com unidade embutida**: novas medidas `ABI Meses Abaixo 50` (mostra "13 meses") e `Dodge Momentum YoY` (mostra "+37%") em vez de "13,00" e "37,00".
- **Título do pipeline por cidade corrigido** — NY lidera em stock; o Sun Belt domina em número de mercados.
- **Eixo X categórico** nos dois gráficos de colunas da página Procura por Segmento — os anos do calendário sem dados (2026, 2027, 2029 no défice) deixam de aparecer como buracos.
- **Top N = 10 serializado** nos três gráficos de estados da página Geografia (licenças totais, multifamily e valor por unidade) — já não é preciso configurar à mão no Desktop; o retoque manual nº 3 abaixo fica obsoleto.
- **Mapa com gradiente de cor** — formatação condicional `dataPoint.fill` pela medida `Permits (milhares)` (teal claro → teal escuro). Se o mapa continuar em branco, falta só ativar **Options → Security → Use Map and Filled Map visuals** no Desktop (isto é uma definição da aplicação, não viaja com o projeto).

## Ronda 4 — componentes da cadeira de Business Analytics

Alinhado com o exercício Financial feito em aula; a serialização de cada componente foi copiada do próprio ficheiro do exercício:

- **Page navigator** no topo das 6 páginas (o conteúdo desceu 40px para lhe dar lugar).
- **Funnel chart** na página Funil de Mercado (substitui o gráfico de barras — mesma query e filtros).
- **azureMap** em vez do filledMap clássico na Geografia — **já não é preciso ativar a opção de segurança dos mapas**; a camada filled vem ligada, bolhas desligadas, gradiente teal pelo volume de licenças, centrado nos EUA continentais.
- **Ribbon chart** na Procura por Segmento — despesa anual 2018-2025 por categoria (mostra trocas de posição entre segmentos).
- **Combo line + clustered column** (2 eixos) na Procura por Segmento — colunas = starts de saúde ($bn), linha = ocupação senior living (%); 2 medidas novas com o filtro embutido (`Starts Saude ($bn)`, `Ocupacao Senior (%)`).
- **Página 6 "Explorar"**: banner de título (textbox), **árvore de decomposição** (licenças por tier → estado → tipo de estrutura, com as linhas Total excluídas para não duplicar), **Q&A** com pergunta guardada ("permits por estado?"), **donut** de licenças por região Census e **matriz** com hierarquia tier → estado expansível.

## Ronda 5 — teste de usabilidade e correção de bugs

- **Corrigido o "erro ao compor o relatório" ao clicar em Manufacturing** (Funil de Mercado): o *cross-highlight* do treemap para o novo funnel disparava um bug do Power BI. Definidas **interações explícitas** (`visualInteractions`) — o treemap e o funnel deixam de se realçar mutuamente e de mexer nos cartões TAM/SAM/SOM (que são referência fixa). Clicar já não dá erro.
- **Combo de saúde desfeito** (Procura por Segmento): as três séries (ocupação 2021-2026, starts 2024-2030, défice 2025/2028/2030) não partilham anos, por isso um combo line+column ficava com a linha e as colunas quase sem sobreposição. Voltou a ser uma **linha de ocupação limpa** — a decisão analítica correta.
- **Audit automático** de todos os 34 visuais contra os dados do Excel: 0 campos ou valores de filtro inexistentes.

## Ronda 6 — robustez (corrige erro do Manufacturing e falha de publicação)

Causa-raiz encontrada e três reversões para componentes comprovadamente estáveis:

- **Erro "ao compor o relatório" ao clicar em Manufacturing** — a causa real era a medida `Despesa Anual ($M)` (`COALESCE(CALCULATE(...Anual), CALCULATE(...Mensal))`): uma medida composta que o motor não consegue decompor para *highlight*. Em 2025 cada categoria tem só uma frequência, por isso os visuais da página 1 passam a usar `Despesa Constr ($M)` (SUM simples) — dá o total anual correto **e** é à prova de clique. Funnel revertido para **gráfico de barras**.
- **Falha ao publicar no Service** — o `azureMap` depende de uma definição do tenant (Azure Maps) e não renderiza em "publish to web", o que fazia o relatório falhar a carregar. Revertido para **filledMap** (mapa coroplético core, publica sempre).
- **Navegador de páginas removido** — era redundante (o Power BI já mostra os separadores em baixo) e em modo de edição só reagia a Ctrl+clique, dando a impressão de "não muda de tab". Navegação normal pelos separadores em baixo.
- **`visualInteractions` removidas** — a tentativa anterior não resolvia e acrescentava risco.

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
| 3 | Geografia — top estados | ~~Top N à mão~~ **Resolvido na Ronda 3**: o filtro Top N = 10 já vai serializado nos três gráficos de estados. |
| 4 | Geografia — mapa | Ativa **Options → Security → Use Map and Filled Map visuals** (definição da aplicação; não viaja com o projeto). O gradiente de cor já vai configurado na Ronda 3. |
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
