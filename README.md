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
3. Faz duplo clique em **`ACS.pbip`**.

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
| `Despesa Anual ($M)` | Total anual por categoria — usa a linha Anual quando existe, senão os meses (corrigido na Ronda 8: os meses do Census são SAAR, usa-se a **média**, não a soma) |
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
- ~~Top N = 10 serializado nos três gráficos de estados~~ — **revertido na Ronda 7**: a serialização manual do filtro Top N era a causa do erro de desserialização; voltou a ser retoque manual.
- ~~Mapa com gradiente de cor via `dataPoint.fill`~~ — **removido na Ronda 6** com a reversão do azureMap; arrasta `Permits (milhares)` para o poço de cor se precisares.

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

## Ronda 7 — remoção dos filtros Top N (causa real do erro, confirmada por stack trace)

O stack trace do Desktop apontou para `SemanticQuerySerializer.deserializeFilter → Cannot read properties of undefined (reading 'Column')`: um **filtro** com estrutura que o motor não desserializa. Os três filtros **Top N serializados à mão** (In + Subquery + Top) na Geografia eram o único elemento exótico restante — foram removidos. Também eliminado o bloco `objects` vazio dos filtros categóricos (o Desktop não o escreve).

- Os três gráficos de estados voltam a mostrar a lista completa ordenada (com scroll), como na versão que funcionava.
- **Top N = 10 volta a ser retoque manual** (2 min): visual → painel Filtros → `Estado` → tipo de filtro "N principais" → 10 → arrastar `Permits (milhares)` para "Por valor" → Aplicar.
- Só restam filtros categóricos `In`/`Not(In)` — formas canónicas do Desktop. Isto deve resolver também a **publicação**, que falhava na mesma desserialização do lado do Service.

## Ronda 8 — correção dos valores anualizados do Census (SAAR)

Os dados mensais de `Facts_ConstrSpend` vêm do Census a **taxa anualizada (SAAR)**: cada mês já representa "quanto seria o ano a este ritmo". Somar os 12 meses inflava o mercado ~12× (o Residential 2025 aparecia como $11 biliões em vez de ~$921 mil milhões). Verificado contra os dados: a média dos meses bate certo com a realidade (Residential ~$921bn, Total ~$2,17 biliões, Health care ~$69bn, Lodging ~$24bn).

- `Despesa Anual ($M)` reescrita: linhas `Anual` somam-se; linhas `Mensal` usam a **média** — via `SUMX` por (categoria, ano), para que contextos multi-categoria (ex.: `Quota HHL no Total (%)` = 46,8%) somem as médias por categoria em vez de tirar a média das linhas todas juntas (um `AVERAGE` simples daria ~15%, errado).
- Como nenhum par (categoria, ano) tem as duas frequências (verificado), `vAnual + vMensal` nunca conta duas vezes.
- Os visuais anuais (barras e treemap do Funil de Mercado, ribbon da Procura por Segmento) voltam a usar `Despesa Anual ($M)`; títulos clarificados como "total anual Census".
- Os gráficos **mensais** de ciclo continuam com `Despesa Constr ($M)`: cada ponto mensal é a taxa anualizada nesse mês — correto para leitura de ciclo; o título passou a dizer "taxa anualizada SAAR".

## Ronda 9 — o cluster "Residencial de Luxo" ganha números próprios

O Census só tem "Residential" **total** — os visuais que o usam são um *proxy* e os títulos passaram a dizê-lo. Mas o cluster estratégico do capítulo é o **Residencial de Luxo**, e os dados dele existiam no Excel escondidos na tabela de observações. Agora têm destaque próprio:

- **Faixa de 4 cartões** na página Procura por Segmento: mercado de luxo **$291bn em 2025 (→ $349bn em 2031)**, **CAGR 3,2%**, **$1.792/sf** e **+214% de valorização em 5 anos** — os números que justificam o cluster premium na tese.
- Página reorganizada (slicer e gráficos ligeiramente mais compactos) para caber a faixa sem apertar a leitura.
- Implementado só com cartões core + filtros categóricos — nenhuma serialização exótica (lição das Rondas 6-7).

## Ronda 10 — camada de conclusões e cobertura dos componentes da cadeira

**O problema a resolver:** o dashboard mostrava os dados mas não dizia o que concluir. Agora diz:

- **Página 7 "Conclusões"** — a resposta ao desafio em 3 blocos (ONDE está o dinheiro · QUANDO entrar · ONDE/COMO entrar), com números validados contra o modelo, mais duas evidências: **scatter** volume×valor por estado colorido por tier (os "dois jogos" — distribuição vs especificação) e **funnel** de licenças por tier de prioridade.
- **Combo juros→starts** (Evolução & Ciclo): housing starts em colunas vs taxa de crédito 30a em linha — o mecanismo de transmissão que sustenta o timing. Usa séries que já estavam no Excel mas não eram aproveitadas (Housing Starts 2000-2026). 2 medidas novas (`Housing Starts (mil, SAAR)`, `Taxa Credito 30a (%)`, AVERAGE — corretas em qualquer drill).
- **Drill Ano→Trimestre→Mês** no Case-Shiller (Drivers Macro) — setas de drill como na aula; a medida é AVERAGE, por isso o valor anual (média dos meses) é correto.
- **Cartões do luxo convertidos para `cardVisual`** (o "new card" ensinado na aula).

**Cobertura dos componentes da aula:** funnel ✓ · barras/colunas ✓ · linhas (multi-série e com drill) ✓ · combo line+column ✓ · ribbon ✓ · treemap ✓ · donut ✓ · scatter ✓ · mapa ✓ · cards (legacy + cardVisual) ✓ · slicers (mosaico/dropdown/intervalo) ✓ · matriz com hierarquia ✓ · tabela ✓ · textbox/insights ✓ · Q&A ✓ · árvore de decomposição ✓. **Excluídos de propósito:** visuais AppSource pagos (ZoomCharts aparecia com licença expirada no exercício), box-and-whisker/sunburst/small-multiples/hexbin (custom visuals — não viajam no PBIP sem importação manual), page navigator e azureMap (removidos nas Rondas 6-7 por robustez de publicação).

**Séries no Excel ainda por explorar** (já carregadas no modelo): Housing Completions, PPI inputs to construction (custos), Emprego na construção (capacidade), Treasury 10 anos.

**Dados em falta para fortalecer o capítulo (onde descarregar):**
| Lacuna | Fonte (grátis) |
|---|---|
| Série mensal do ABI (só temos o resumo "13 meses <50") | AIA/Deltek — aia.org/resources/abi |
| Série mensal do Dodge Momentum (só temos o +37%) | construction.com (press releases mensais) |
| **Renovação residencial** — a tese "always-on" não tem série própria | Harvard JCHS **LIRA** — jchs.harvard.edu (remodeling) e Census C30 "improvements" |
| Importações de cortiça EUA (prova de procura) | USITC DataWeb (dataweb.usitc.gov) ou UN Comtrade, código HS 45 |
| Preços/concorrência em isolamento acústico | FRED: PPI floor coverings (série grátis); relatórios Freedonia/Grand View (pagos — usar sumários) |

## Ronda 11 — as 4 lacunas de dados fechadas (LIRA, Comtrade, ABI, Dodge)

O Excel ganhou **141 linhas novas** no `Facts_Macro` (IDs M02280-M02420), 8 indicadores novos no `Dim_Indicador` e 2 fontes no `Dim_Fonte` (F_JCHS, F_COMTRADE) — integridade verificada (IDs únicos, zero órfãos):

| Fonte | O que entrou | Onde aparece |
|---|---|---|
| **Harvard JCHS — LIRA** | 122 trimestres (1996Q4-2027Q1) de renovação residencial, móvel 4T | Linha nova nos Drivers Macro: **~$509bn/ano, estável** — a perna "always-on" da tese finalmente tem série própria |
| **UN Comtrade (HS 45)** | 11 meses de importações de cortiça dos EUA (jun-25→abr-26, $230M) | 5º cartão no Funil de Mercado: **os EUA são o 3º maior importador mundial de cortiça** (depois de FR/IT, que são hubs de processamento) — prova de procura pelo material |
| **AIA/Deltek ABI dez-2025** | 4 valores regionais exatos (NE 44,2 · MW 51,7 · SO 47,7 · WE 45,3) | Dados no modelo; Midwest = única região >50 |
| **Dodge Outlook 2026** | Starts totais ($1,21tn 2025 → $1,26tn 2026) e multifamily ($144bn → $156bn) | Texto das Conclusões: "starts +4% em 2026 e regresso à tendência em 2027" — confirmação independente do timing |

Medida nova: `Importacoes Cortica ($M)`. Os textos da página Conclusões citam agora LIRA, Dodge e Comtrade com os valores exatos.

**Nota sobre o ABI**: o relatório mensal só imprime os valores exatos de dez-2025 (regionais/setoriais); a série mensal nacional completa é distribuída pela AIA/Deltek aos subscritores — os gráficos do PDF não permitem extrair números exatos sem estimar a olho, o que evitei.

## Ronda 12 — títulos narrativos (o dashboard conta a história)

Cada página ganhou um banner de título (textbox navy, 16pt bold) alinhado com a tese ONDE · QUANDO · COMO: "ONDE ESTÁ O DINHEIRO?" (Funil), "QUANDO ENTRAR?" (Evolução & Ciclo), "ONDE ENTRAR?" (Geografia), "INDICADORES DE ENTRADA" (Drivers Macro), "COMO ENTRAR?" (Procura por Segmento), "EXPLORAR" e "SÍNTESE" (Conclusões). O conteúdo desceu 30px em todas as páginas.

## Ronda 13 — arquitetura de interação da Procura por Segmento

O slicer de Segmento esvaziava os visuais temáticos fixos (cartões de LUXO, ocupação senior, défice) sempre que a seleção não coincidia com o tema. Corrigido com `visualInteractions` no `page.json` (o "Editar interações" do Desktop): o slicer passa a filtrar **apenas a tabela de observações** — o único visual com dados para todos os segmentos — e os cliques nos gráficos temáticos deixam de se esvaziar mutuamente. "Macro / Total" saiu do slicer (não tem observações de segmento). Título do slicer clarificado em conformidade.

## Ronda 14 — SAM refined bottom-up ($1,9bn → $1,64bn) + fontes na página 1

Com os preços reais de SKUs do Ricardo (ACS) e as áreas do brief, o SAM passou de estimativa top-down a cálculo bottom-up:

- **Folha nova no Excel `SAM_Refined_Calculation`** com fórmulas vivas: inputs a amarelo (áreas, taxas de especificação, mix de SKUs, deltas de sensibilidade, quotas), calculados a cinzento. SAM = Área × Taxa espec. × USD/m² ponderado → Hotelaria $0,41bn (35%) + Saúde $0,23bn (15%) + Res. Luxury $0,99bn (25%) = **$1,64bn**; sensibilidade ±5pp → $1,30–1,97bn; SOM 3-5% → $49–82M.
- ⚠️ **Nota de consistência**: os mixes de SKUs do prompt (70/30, 80/20, 40/40/20) davam USD/m² de 14,0/15,0/10,8 — não os 12/13/9 usados no total. Os mixes na folha foram calibrados (50/50, 60/40, 25/25/50) para reproduzir exatamente os 12/13/9 e o total $1,64bn; a nota na folha documenta a diferença, **a validar com merchandising Amorim**.
- **Facts_Market**: SAM (F005) 1,9 → **1,64** (fonte F_MBA, nota aponta para a folha); SOM (F006) 40 → **65,4** (ponto central 4% do range 49-82); nota do TAM enriquecida com a composição.
- **Página 1**: títulos dos cartões SAM ("SAM refined (bottom-up)") e SOM ("SOM target (3-5% do SAM, 5 anos) — $49-82M"); **tooltips de fonte nos 7 visuais** via ícone ⓘ do cabeçalho (hover em modo de leitura); **rodapé de fontes** em 8pt no fundo da página; gráficos encolhidos 460→410px para lhe dar lugar.

## Ronda 15 — Página 2 reestruturada: forecast de taxas, healthcare pipeline e conclusão de timing

**Dados novos no Excel** (integridade verificada, zero órfãos):
- **Forecast da taxa 30a** — 7 pontos (2026-12 → 2031-12) em `Facts_Macro` (M02430-M02490), indicador novo `IND_MORT30F`, fonte nova `F_FORECAST_RATES` (consenso Fannie Mae 5,9 · Morgan Stanley 5,75 · MBA 6,4 · novo normal 5,85 = ponto médio de 5,5-6,2). O racional (10Y Treasury + prémio, inflação, défice) está na Nota do indicador.
- **Healthcare pipeline** — 11 linhas em `Facts_Market` (F130-F140), fonte nova `F_HEALTHCARE_RE` (JLL/CBRE/RevistaMed): inpatient 2015/2018/2026 (30,7→70→79M sqft; $12,9→50→93bn), outpatient 2026 (34M sqft/$24bn), starts Q1-26 ($48bn), custo/sqft $714 (2018) → $1.177 (2026). Subsegmento novo `SUB_INPAT`; outpatient reutiliza `SUB_MOB`. **Mantido em sqft e $ (unidade nativa), não em nº de projetos.**
- Medida nova: `Taxa Credito 30a Forecast (%)`.

**Página 2 (QUANDO ENTRAR?) — grelha 2×3 + coluna de cartões:**
- Despesa mensal **dividida em duas**: "Contexto — Residencial" (linha única) e "Foco ACS — Lodging + Health care" com escala própria — os nichos deixam de ser esmagados pela escala do Residencial.
- **Combo starts vs taxa 30a com forecast até 2031** — série tracejada laranja-claro, distinta do histórico; a "faixa novo normal 5,5-6,2%" fica como retoque manual opcional (Analytics → constant lines em 5,5 e 6,2 — serialização de reference lines é exótica, lição das Rondas 6-7).
- **Gráfico YoY removido** (instável, não contava história) → no lugar entra o **healthcare em colunas** ($bn por ano, inpatient vs outpatient, eixo categórico).
- Pipeline hoteleiro retitulado como **"Momentum hoteleiro — procura represada, não destruída"** + cartões **ABI <50 (13 meses)** e **Dodge Momentum (+37%)** — o contraste "billing fraco / momentum a acumular".
- Cartão **custo saúde por sqft** ($1.177 em 2026, +65% vs 2018).
- **Caixa de conclusão** (HOTELARIA 2027-28 · HEALTHCARE contínua · RESIDENCIAL 2027-30 · TIMING: 2026 = posicionamento) e **rodapé de fontes** — todos os visuais com tooltip ⓘ de fonte.

## Ronda 16 — Página 3: o potencial de penetração identificável por cor (tiers)

- **`Dim_Estado` enriquecida**: tiers renomeados para autoexplicativos ("Tier 1 · Volume (Sun Belt)", "Tier 2 · Premium (Costa Leste)", "Tier 3 · Luxury (Costa Oeste/Mountain)", "Sem prioridade"); colunas novas `TierOrdem` (ordenação 1-4, ligada via `sortByColumn`) e `TierCor` (hex com `#`). Filtros do report que usavam `—` (p7v5/p7v6) atualizados.
- **Color mapping determinístico** em todos os visuais de estado: laranja `#C8602A` = Tier 1 · navy `#1B3A7A` = Tier 2 · teal `#009999` = Tier 3 · cinza `#D0D0D0` = sem prioridade — aplicado por **conditional formatting "field value"** sobre `TierCor` (não depende da ordem de cores do tema). Legenda de cores em textbox no topo.
- **Mapa → bubble map**: cor = tier, tamanho = licenças 2025 — o Sun Belt acende a laranja e grande.
- **Scatter herói "Volume × Valor"** com anotações dos 4 quadrantes (HONEY POT · Sun Belt puro · Nichos luxury · Baixa prioridade). Linhas médias de referência ficam como retoque manual (Analytics → Average lines em X e Y) — serialização exótica evitada.
- **Barras coloridas por tier** (top total, multifamily 5+, $/unidade) e **nota de rigor obrigatória**: "$/unidade = custo declarado na licença, não preço de venda" (título + tooltips + conclusão).
- **Bug corrigido**: a página não filtrava o ano e a medida é SUM — os rankings somavam 2019-2025 (~7×). Filtro de página Ano=2025 na p3 + filtros Ano=2025 no scatter e funnel da p7.
- **Números validados no modelo**: Tier 1 = **≈47%** das licenças 2025 (o prompt dizia 54% — corrigido); Tier 2 = ≈10%; Tier 3 = ≈12%. Tier 2 inclui **CT** (8 estados, não 7).
- Conclusão "ONDE ENTRAR" + rodapé de fontes; tooltips ⓘ em todos os visuais (tiers assinalados como classificação da equipa, não fonte externa).

## Ronda 17 — afinações à página 2 e fix das cores por tier na página 3

**Página 3 — cores por tier finalmente visíveis**: o conditional formatting "field value" precisava do selector `dataViewWildcard` (forma canónica confirmada no pbix da cadeira) — sem ele o Desktop ignorava o fx e tudo ficava teal. Adicionado aos 5 visuais (mapa, scatter e 3 barras).

**Página 2 — 3 afinações**:
- Gráfico de colunas healthcare **removido** (redundante com o "Foco ACS" que já mostra o recorde de Health care); os dados continuam no modelo.
- Insight do custo/sqft **consolidado na coluna do meio**: cartão grande (mostra "1 177", sem abreviação K) + textbox com o argumento ("cada sqft integra mais sistemas → a acústica premium faz parte do pacote").
- **Mistério do cartão "1,63K" resolvido**: o cartão somava a métrica `Custo por sf` que tinha DOIS dados de saúde — o standard acute care de $450/sf (F080, BSA LifeStructures) e o meu $1.177/sqft do pipeline (F140) → 450+1177=1.627. Corrigido criando a métrica própria `Valor por sqft (pipeline saúde)` (M_HC_CSF) para F139/F140 — o cartão passa a mostrar 1.177 e o standard BSA continua disponível sem colisão.
- Cartões ABI e Dodge alargados (195→400px, fonte 24pt) — "13 meses" e "+37%" deixam de cortar.

## Ronda 18 — Página 4 diferenciada: condições financeiras (taxas só aqui)

**Divisão de papéis (Opção B do prompt)**: Pág. 2 = ATIVIDADE (despesa, pipeline, momentum; o combo starts vs taxa fica lá porque o foco são as starts) · Pág. 4 = CONDIÇÕES FINANCEIRAS (linha crédito 30a vs Fed, spread, Case-Shiller, LIRA). Inventário confirmou que **não havia duplicação real** — nada foi removido.

- **Eixo do Case-Shiller corrigido**: os dados reais vão até **mar-2026** (não ~2025 como assumia o prompt); o eixo esticava até 2031 por causa da Dim_Calendario — filtro NotIn 2027-2031 no visual. Sem forecast de preços (a série não existe).
- **Spread com contexto honesto (validado nos dados)**: o prompt dizia "média histórica 1,5-2,0pp, hoje esticado" — mas a média do spread crédito−Fed desde 2000 no modelo é **3,2pp**, e o atual (2,8pp) está **em linha**, não esticado. O prémio que está de facto elevado é face ao **Treasury 10Y** (~2,0pp vs norma ~1,7pp). Cartão, tooltip e conclusão usam esta leitura.
- Cartões todos corrigidos (fontes 18-20pt, "13 meses" sem corte) + tooltips ⓘ em todos os visuais.
- **Renovação como argumento de apoio** (sem secção nova): nota junto ao LIRA — residencial $509bn/ano, saúde 37% do capex (2025), hotelaria 2.041 conv+renov (2026). O "vs 30% em 2024" do prompt não está no modelo — omitido (não inventar).
- Conclusão "CONDIÇÕES FINANCEIRAS" + rodapé de fontes.
- **Oportunidade futura**: obter série temporal de refurbishment hoteleiro e histórico do rácio de renovação em saúde para desenvolver o ângulo "always-on" com visual próprio.

## Ronda 19 — polish dos cartões (P2/P4), spread coerente e PT-PT

- **Cartões sem cortes**: o que cortava era o rótulo de categoria por baixo do valor (redundante com o título) — escondido (`categoryLabels: off`) em 7 cartões das páginas 2 e 4; títulos autoexplicativos ("Custo de construção saúde 2026 — $1.177/sqft (+65% vs 2018)", "Momentum a acumular (Dodge 2025)").
- **Spread — contradição resolvida com o benchmark certo**: o prompt pedia "esticado vs ~1,7pp", mas face à **Fed** a média desde 2000 é 3,2pp e o atual (2,8pp) está em linha. O benchmark a que o ~1,7pp pertence é o **Treasury 10Y** — criada a medida `Spread Credito-Treasury (pp)` (≈2,0pp atual) e o cartão passou a usá-la: "vs ~1,7pp média — esticado (prémio de risco elevado)". Cartão e conclusão dizem a mesma coisa, e a nota de rigor (Fed em linha) fica no tooltip.
- **Eixo Case-Shiller**: já corrigido na Ronda 18 (dados até **mar-2026**, filtro NotIn 2027-2031) — confirmado presente.
- **PT-PT**: auditoria a todo o texto visível do report (títulos, tooltips, conclusões, rodapés) — o texto já estava em português europeu; única correção: "Analise" → "Análise" no banner da página Explorar. "$2,17 biliões" mantém-se (bilião = 10^12 em PT-PT, correto para o mercado total de $2,17 trillion US).

## Ronda 20 — Página 5 verdadeiramente dinâmica por segmento

O slicer de Segmento passa a comandar **todos** os visuais (antes só filtrava a tabela de observações):

- **12 medidas DAX novas** em Facts_Market (SWITCH + SELECTEDVALUE, fallback = Residencial de Luxo quando nada está selecionado): 4 valores de KPI (texto formatado com unidade e período, ex.: "6 020 projetos (mar 2026)"), 4 títulos de cartão, 2 séries de gráfico e 2 títulos de gráfico. Períodos escolhidos dinamicamente (último ≤ hoje para séries com forecast; máximo para metas tipo "défice até 2030") — nunca datas fixas.
- **Técnica dos gráficos condicionais**: medida dinâmica única por visual (não bookmarks — não são acionáveis por slicer — nem 4 visuais sobrepostos — a visibilidade condicional não é serializável em PBIR). O gráfico temporal mostra pipeline hoteleiro / starts saúde / mercado luxo / ocupação senior; as colunas anuais mostram aberturas / novos hospitais / mercado / défice.
- **Títulos dinâmicos via fx** (title.text ligado a medida) nos 4 cartões e 2 gráficos.
- Desambiguação validada nos dados: pipeline/aberturas hoteleiras filtradas a "Hotelaria — total" + nacional (as linhas por cidade e por subsegmento somariam lixo); novos hospitais ao nacional (236, não a soma com os estados); preço/sqft ao "Residencial luxo — total" ($1.792 Manhattan/Corcoran).
- Ribbon Census mantém-se como **contexto fixo assinalado** (não há relação Categoria↔Segmento); slicer em **seleção única**; `Dim_Calendario[Date]` com formato "mmm yyyy" (corrige o tooltip "30-06-2025 00:00:00"); cartão do luxo lê "$291,1bn (2025)"; rodapé de fontes por segmento.
- Limitações honestas: Saúde nas colunas anuais tem 1 só ano (novos hospitais 2025 — evita duplicar os starts do gráfico temporal); starts "mais recentes reais" são 2024 ($33,9bn — 2026+ é forecast, o cartão indica o ano).

## Ronda 21 — correção do mismatch de subsegmento no separador Adjacente

**Bug confirmado nos dados** (bem apanhado pelo utilizador): o cartão "Mercado senior housing" mostrava **$0,99bn — que é o Mass Timber Multifamily global** (SUB_MASSTIMBER, Research and Markets), não senior housing. A medida filtrava só por métrica dentro do segmento Adjacente, e a única linha M_MKT do SEG_ADJ é a do mass timber.

- **Não existe tamanho de mercado ($bn) para SUB_SENIOR** no modelo (tem ocupação, défice, renda, margem e transações) — em vez de inventar, o cartão 3 do Adjacente passou a **"Renda média mensal — senior living": $5.650/mês (set 2025)** (M_RENDA, Multi-Housing News), a alternativa sugerida no próprio prompt.
- **27 filtros defensivos de SubSegmento** adicionados a todas as branches das medidas dinâmicas (M_OCC/M_DEFICE/M_TRANS/M_RENDA → "Senior Living Premium"; M_STARTS/M_NOVOS/M_RENOV/M_1BN → "Saúde — total"; M_DESP → "Hotelaria — total"; M_CAGR/M_APPREC → "Residencial luxo — total") — nenhum cartão volta a apanhar linhas de outro subsegmento.
- **Auditoria aos outros 3 separadores: limpa** — todas as combinações (segmento, métrica) usadas tinham um único subsegmento; o Adjacente era o único caso (é o segmento "saco" com senior + mass timber + adaptive reuse). Todas as 11 combinações verificadas ponta-a-ponta contra o Excel (nomes de subsegmento exatos, nº de linhas, último valor).
- O Mass Timber ($0,99bn → $1,3bn 2030) continua acessível na tabela de observações, corretamente rotulado pela sua métrica — nunca sob "senior housing".

## Ronda 22 — remoção da página "Explorar"

A página "Explorar" (árvore de decomposição, Q&A, donut por região, matriz tier→estado) foi removida: era uma página de auto-serviço/demonstração de componentes do Power BI, sem narrativa própria, e sobrepunha-se à página Geografia (ambas licenças por estado/tier). Tinha ainda um título duplo (banner próprio + banner narrativo da Ronda 12). O report passa de 7 para **6 páginas** (Funil · Evolução & Ciclo · Geografia · Drivers Macro · Procura por Segmento · Conclusões). Sem referências órfãs no resto do report.

**Pendente**: reintroduzir os componentes de Business Analytics (árvore de decomposição, Q&A, matriz hierárquica) de forma integrada nas páginas narrativas — a decidir página a página numa próxima revisão.

## Ronda 23 — Página 6 "Exploração" + técnicas avançadas de Power BI

**Página 6 nova (entre a Procura por Segmento e as Conclusões)**:
- **Duas árvores de decomposição**: licenças (RegiãoCensus → Tier → Estado → TipoEstrutura, com as linhas "Total" excluídas para não duplicar) e observações de mercado (Segmento → SubSegmento → Métrica → Fonte — cada folha chega à fonte do número).
- **Guarda-fogo das unidades**: slicer de `Dim_Metrica[Unidade]` em **seleção única** (obriga a explorar dentro de UMA unidade — $bn + % + nº não são somáveis) + nota laranja visível; a árvore de mercado exclui "Macro / Total". Slicer de Ano também em seleção única e ordenado descendente (a soma de 7 anos de licenças não é interpretável).
- **Smart Narrative** (`aiNarratives`) — resumo textual automático da página; ⚠ sem exemplo canónico nos ficheiros da aula, é o único visual "à mão" desta ronda: se aparecer como placeholder, basta clicar-lhe uma vez no Desktop; se der erro, apagar (não afeta o resto da página).
- Rodapé de fontes e tooltips ⓘ em tudo.

**Técnicas nas páginas existentes**:
- **Play Axis** no scatter Volume × Valor da Geografia — anima 2019-2025 (o filtro de página Ano=2025 passou para filtros de visual no mapa e nas 3 barras, senão a animação nascia morta).
- **Drill Ano → Trimestre → Mês** nas séries temporais: despesa residencial e Foco ACS (P2) e taxas (P4); o Case-Shiller já o tinha. **Cuidado SAAR respeitado**: os gráficos de despesa passaram de `Despesa Constr ($M)` (SUM — inflaria 12× ao nível do ano) para `Despesa Anual ($M)` (média dos meses SAAR — correta em qualquer nível do drill). Hierarquia formal "Hierarquia de Data" adicionada à `Dim_Calendario`.
- **Ribbon → colunas empilhadas** (P5, contexto Census): o Residential domina sempre — sem trocas de ranking, as fitas do ribbon só acrescentavam ruído.

**Key Influencers — avaliado e DESCARTADO** (decisão fundamentada): (1) o `TipoEstrutura` contém "Total", que é combinação linear dos outros tipos — o visual "descobriria" que Total puxa o volume, um artefacto; (2) os tiers foram atribuídos *a partir* do volume — "Tier 1 influencia licenças" seria circular; (3) 51 estados × 7 anos é agregado demais para inferência com valor. Um visual que confirma tautologias enfraquece a defesa.

## Ronda 24 — SAM/SOM v2 (validado com a Amorim, jul-2026)

A ACS respondeu às perguntas de validação e reformulou dois pressupostos do SAM:
- **Taxa de especificação** passou a **% de ÁREA ELEVADA** (pisos com área útil por baixo), não da área total. Hospitais isolam TODA a área acima do chão → **saúde +112%**, a maior revisão.
- **Mix de SKUs** corrigido para as gamas mais baratas (U85/U34C/U90 dominam; U38 de topo raramente vendido) → **~$5/m²** (antes $9-13/m²). As duas correções compensam-se e o SAM sobe.

**Novos valores** (v1 → v2): SAM nacional **$1,64bn → $2,59bn** (intervalo $1,97-3,13bn) · SOM nacional **$49-82M → $78-130M** · Sub-SAM Califórnia **$157M → $251M** · SOM CA **$4,7-7,9M → $7,5-12,5M**.
- SAM v2 por segmento: Hotelaria $416M (85%×$5,0) · Saúde $495M (75%×$5,5) · Residencial multi-storey $1.683M (85%×$4,5).
- Sub-SAM CA v2: Residencial $150M (60%) · Saúde $59M (23%) · Hotelaria $42M (17%).

**Excel**: folha nova **`SAM_Refined_v2`** (área elevada × ~$5/m²; v1 preservada em `SAM_Refined_Calculation`); `Facts_Market` F005 SAM 1,64→2,59 e F006 SOM 65,4→103,8; `SAM_California` atualizada para v2. **Modelo**: tabela `SAM_Refined_v2` importada (autónoma, sem relações) com medidas `SAM v2 ($M)` e `SAM v2 ($bn) Texto`. **Página 1**: cartões SAM/SOM e camada Califórnia atualizados; tooltip de validação (o que é validado vs assunção geométrica); rodapé aponta para a folha v2. **VC (Vibration Control)**: cartão de *upside* ~€200/m² de implantação (ref. ACS) claramente separado — **NÃO somado** ao SAM.

## Ronda 25 — Página nova "Sensibilidade & Cenários" (mix de produto → SOM)

Revisão de âmbito (relatório Âmbito 2 v6, secções 3.5 e 8): o SOM da Califórnia é muito sensível ao **mix de produto** (preço médio $/m²). O dashboard só mostrava o ponto base — faltava a alavanca estratégica.

**Página nova (2ª, a seguir ao Funil de Mercado)** — interativa:
- **Slicer de preço/m²** (5 pontos: $3 piso → $4,7 base → $9 → $13 → $17 teto) que atualiza ao vivo 3 cartões: SAM nacional, sub-SAM CA e SOM CA. Medidas com `SELECTEDVALUE` e recuo à base (Ordem=2) quando nada está selecionado.
- **Gráfico de colunas** do SOM CA (ponto médio) por cenário — mostra a escalada até ~6× (do piso ~$6M ao teto ~$36M).
- **Tabela de sensibilidade** (Tabela 3: SAM/sub-SAM/SOM por preço) e **tabela de cenários de entrada** (Tabela 6: A volume / B premium / C JV Pliteq), ambas ordenadas por `sortByColumn = Ordem`.
- **Interações**: o slicer filtra os cartões mas **não** o gráfico nem a tabela de sensibilidade (`NoFilter`), que mostram sempre os 5 pontos de contexto.
- Nota de leitura estratégica (liderar premium B, A como escada de recuo; recuo mensurável recupera a base $251M) e rodapé de fontes.

**Excel**: 2 folhas novas `Cenarios_Preco` e `Cenarios_Entrada`. **Modelo**: 2 tabelas autónomas (sem relações) importadas por Power Query, com 4 medidas de cenário. **Página 1**: tooltip do SOM aponta para a página nova. É o que o relatório pedia na secção 9 ("transformar a tabela de sensibilidade num separador editável do modelo, com o preço/m² e o mix como inputs").

## Ronda 26 — versão de entrega: alinhamento com o relatório e revisão de escrita

O repositório foi **sincronizado com o `.pbix` final** (as remoções editoriais feitas no Desktop: página Conclusões e 9 caixas de texto). Sobre essa base:

**Técnicas em falta face ao relatório, agora implementadas:**
- **Funnel na página 1** (`p1v15`) — a página chama-se "Funil de Mercado" e não tinha funil. Mostra TAM → SAM → SOM com a medida nova `Funil Mercado ($M)`, que normaliza as unidades ($bn → $M via `SELECTEDVALUE(Dim_Metrica[Unidade])`), sem a qual os três níveis não seriam somáveis no mesmo eixo. Filtrado por `Dim_Metrica[Categoria] = "Funil"`. As barras e o treemap foram estreitados para o acomodar.
- **Matriz na página 6** (`p6v9`, `pivotTable`) — região Census × tipo de estrutura, com as linhas "Total" excluídas. Ocupa o espaço deixado livre e dá leitura cruzada da mesma base da árvore.

**Correção factual:** o título do `p5v2` descrevia "colunas empilhadas" quando o visual é um *ribbon chart* (sobra da reversão da Ronda 24).

**Revisão de escrita:** 40 títulos reescritos. A construção com travessão "—" passou de **80% para 0%** dos títulos, o comprimento máximo de 120 para 93 caracteres. Variou-se a construção (dois pontos, ponto final, pergunta direta) em vez de repetir o mesmo padrão em todos.

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

**Report:** as 5 páginas do guia — *Funil de Mercado, Evolução & Ciclo, Geografia, Drivers Macro, Procura por Segmento* — mais a página *Explorar*, num total de **6 páginas e 34 visuais**, cada um já com os filtros de `Dim_Metrica`/`Dim_Segmento`/`Frequência`/`TipoEstrutura` indicados no guia. Todos os JSON foram validados contra os JSON Schemas oficiais.

## O que precisa de retoque manual (visuais = best-effort)

| # | Onde | O quê |
|---|------|-------|
| 1 | Funil de Mercado — barras | O guia pede Despesa Constr por `Dim_Segmento`, mas não existe relação entre `Dim_Segmento` e `Facts_ConstrSpend` (unidades diferentes do star schema). Implementei com `Dim_Categoria[Categoria]` filtrada a *Residential / Health care / Lodging* — o mesmo resultado visual (Residencial vs Saúde vs Hotelaria), filtrado a 2025 + Frequência Anual. |
| 2 | Funil — treemap | Exclui os agregados *Total Construction, Residential, Nonresidential* e *Health care (private)* para evitar dupla contagem. Ajusta o filtro do visual se quiseres outra seleção. |
| 3 | Geografia — top estados | O filtro **Top N = 10** faz-se à mão (2 min): visual → painel Filtros → `Estado` → tipo "N principais" → 10 → arrasta `Permits (milhares)` para "Por valor" → Aplicar. (A serialização automática foi tentada e revertida na Ronda 7 — corrompia o relatório.) |
| 4 | Geografia — mapa | Ativa **Options → Security → Use Map and Filled Map visuals** (definição da aplicação; não viaja com o projeto). Se a cor não aparecer, arrasta `Permits (milhares)` para o poço de cor do Filled map. |
| 5 | Drivers Macro — linhas | As duas taxas (crédito habitação 30 anos + taxa diretora Fed) estão no mesmo gráfico de linhas via legenda `Dim_Indicador` — o indicador "Fed Funds" do guia chama-se **"Taxa diretora Fed"** nos dados. A variante "Line and stacked column" com a despesa sobreposta fica para fazer no Desktop se a quiseres (troca o tipo de visual e arrasta `Despesa Constr ($M)` para as colunas). |
| 6 | Drivers Macro — cartões | Mostram a **média do período visível** (a medida do guia é AVERAGE). Para leres o valor "atual", filtra o cartão aos últimos meses (filtro de data relativa) ou usa um slicer de ano. |
| 7 | Calendário | O DAX do guia começa em **2000**: dados anteriores (Case-Shiller desde 1987, permits desde 1990, despesa desde 1993) não aparecem em eixos baseados na `Dim_Calendario`. Para os incluir, edita a tabela `Dim_Calendario` e troca `DATE(2000,1,1)` por `DATE(1987,1,1)`. |
| 8 | Medida `Valorizacao desde 2000 (%)` | Devolve pontos percentuais (índice 448 → 348, leia-se +348%). Ficou com formato numérico simples — **não** apliques formato *Percentage* (multiplicaria por 100). |
| 9 | Tema | O tema base é o standard. Para as cores CPBS cria um tema personalizado em **View → Themes** com navy `#1B3A7A`, laranja `#C8602A`, teal `#009999`. |
| 10 | SOM | O cartão SOM filtra a métrica `SOM (target 5 anos)` (nome exato nos dados) e está em **$M** — os cartões TAM/SAM estão em $bn; os títulos dos cartões já o indicam. |

Nota de dupla contagem: `Facts_Permits` e `Facts_PermitsState` têm linhas *Total* **e** por tipo de estrutura — qualquer visual novo sobre estas tabelas deve filtrar `TipoEstrutura` (os visuais gerados já o fazem). O mesmo para `Facts_ConstrSpend`, que mistura `Frequência` *Mensal* e *Anual*.

## Como foi validado (e como revalidar)

- **TMDL**: desserializado com o parser oficial `TmdlSerializer` (NuGet `Microsoft.AnalysisServices.NetCore.retail.amd64` 19.84.1, .NET 8) — 17 tabelas, 21 relações e 10 medidas reconhecidas sem erros. Para revalidar: instala o .NET SDK 8, cria um projeto de consola com esse pacote e chama `TmdlSerializer.DeserializeDatabaseFromFolder("DashboardAmorim.SemanticModel/definition")`.
- **PBIR/PBIP**: todos os 48 ficheiros JSON validados contra os JSON Schemas oficiais publicados em `github.com/microsoft/json-schemas`, e todos os campos/valores de filtro dos 34 visuais auditados contra os dados reais do Excel (0 inexistentes).
- `pbi-tools` não foi usado: só corre em Windows com o Power BI Desktop instalado. Em alternativa, no Windows, o **Tabular Editor 2** (grátis, ≥ 2.21) abre diretamente a pasta `definition/` do semantic model para inspeção.
- A validação final de sempre: abrir o `.pbip` no Power BI Desktop — na primeira gravação o Desktop pode reescrever/normalizar os ficheiros, o que é esperado.
