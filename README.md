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
