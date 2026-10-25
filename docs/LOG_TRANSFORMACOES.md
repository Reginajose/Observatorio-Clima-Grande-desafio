# Log de transformações

Cada etapa do pipeline, na ordem em que roda: o que entra, o que é feito e quantas linhas sobram. Os números abaixo foram conferidos rodando os notebooks do repositório a partir dos arquivos brutos oficiais (21/10/2026). O resultado bate com os arquivos de `data/`: idêntico no SINISA e na CAPAG, e com diferença máxima de R$ 0,02 nos valores em reais do Atlas (arredondamento de ponto flutuante).

Todas as etapas abaixo rodam em sequência no notebook principal, `notebooks/Observatorio_Clima_Pipeline_Completo.ipynb` (partes 1.1 a 3). Os notebooks de cada etapa, citados em cada seção, ficam em `notebooks/etapas/`.

## 1. SINISA 2024: `notebooks/etapas/01_ETL_SINISA.ipynb`

**Entrada (bruto, Ministério das Cidades):** `SINISA_AGUASPLUVIAIS_Informacoes_Formularios_Tecnicos.xlsx`, `SINISA_AGUASPLUVIAIS_Indicadores_AP2024.xlsx` e `GLOSSARIO_INFORMACOES_SINISA_AGUASPLUVIAIS.pdf`
**Saída:** `data/sinisa_limpo.csv` (5.570 linhas, 9 colunas)

| Passo | O que acontece | Linhas |
|---|---|---|
| 1.1 | Lê a aba "Dados sobre Gestão de Riscos" (arquivo 1) e "Indicadores por município" (arquivo 2) | 5.581 em cada |
| 1.2 | Descarta as linhas de título, cabeçalho, fórmula, unidade e código acima dos dados | 5.581 → 5.570 |
| 1.3 | Localiza as colunas pelo código do campo (linha de códigos do formulário): GAP2105 (mapeamento de risco), GAP2110 (domicílios em risco), coluna "Crítico", IAP0002, IFD0010, IGR0001 | 5.570 |
| 1.4 | Padroniza o código IBGE em 7 dígitos; nenhum código inválido | 5.570 |
| 1.5 | Remove linhas sem código IBGE (nenhuma) | 5.570 |
| 1.6 | Converte o texto literal "null" em vazio (725 municípios que não responderam) | 5.570 |
| 1.7 | Converte colunas numéricas; "-" e texto viram vazio (725 em domicílios em risco, 2.102 em % de vias, 1.560 em investimento) | 5.570 |
| 1.8 | Junta os dois arquivos pelo código IBGE (todos os 5.570 estão nos dois) | 5.570 |

Observação registrada: 2.607 municípios declaram zero domicílios em risco; zero costuma significar "não mapeou", não "sem risco".

## 2. CAPAG: `notebooks/etapas/02_Tratamento_CAPAG.ipynb`

**Entrada (bruto, Tesouro Nacional):** `capag-municipios-posicao-2026-set.xlsx`, aba "CAPAG Ano Base 2025" (prévia). O notebook baixa o arquivo direto do Drive da célula.
**Saída:** `data/capag_nacional_tratada.csv` (5.567 linhas, 9 colunas)

| Passo | O que acontece | Linhas |
|---|---|---|
| 2.1 | Lê a aba com cabeçalho na linha 4 | 5.567 |
| 2.2 | Confere as contagens oficiais da posição de set/2026: A+ 418, A 1.096, B+ 200, B 963, C 2.274, D 20, n.d. 426, n.e. 170 | 5.567 |
| 2.3 | Seleciona 6 colunas; confere nulos (0), duplicados (0) e código IBGE de 7 dígitos (todos válidos) | 5.567 |
| 2.4 | Cria `criterio_capag_cd` (C ou D: 2.294), `sem_nota_capag` (n.d. ou n.e.: 596) e `criterio_fiscal_priorizacao` (sem nota A ou B: 2.890) | 5.567 |

## 3. Atlas Digital de Desastres: `notebooks/etapas/03_Exploracao_Atlas_S2iD.ipynb`

**Entrada (bruto, MIDR):** `BD_Atlas_1991_2025_v1.1_2026.08.06_Consolidado.xlsx`, aba **"Atlas Valores Corrigidos"**, exportada como `atlas_geral.csv` (separador `;`, datas no formato dd/mm/aaaa)
**Saída:** `data/S2iD_Desastres_Hidrologicos_Selecao_Auditada.csv` (9.890 linhas, 17 colunas)

| Passo | O que acontece | Linhas |
|---|---|---|
| 3.1 | Lê a base completa | 76.190 |
| 3.2 | Recorte: UFs válidas, eventos de 2021 a 2025 e grupo "Hidrológico" do próprio Atlas (chuvas intensas, inundações, enxurradas, alagamentos e movimento de massa) | 76.190 → 9.890 |
| 3.3 | Completude: marca eventos sem nenhum dano em R$ declarado (`flag_sem_declaracao_financeira`) | 9.890 |
| 3.4 | Consistência: recalcula o total de pessoas afetadas somando 6 categorias (mortos, feridos, enfermos, desabrigados, desalojados e desaparecidos) e confere com o total oficial (0 diferenças); marca 177 eventos reconhecidos sem impacto declarado | 9.890 |
| 3.5 | Unicidade: protocolo S2iD sem repetição; 54 possíveis duplicatas semânticas (mesmo município, data e tipo) marcadas, não removidas | 9.890 |
| 3.6 | Outliers (3 × IQR): 946 em danos materiais e 1.459 em pessoas afetadas, marcados, não removidos | 9.890 |
| 3.7 | Mantém as 17 colunas usadas adiante | 9.890 |

Distribuição por ano: 2021: 1.334 · 2022: 2.777 · 2023: 2.361 · 2024: 1.739 · 2025: 1.679. Status: 4.635 reconhecidos e 5.255 só registrados (os dois entram).

## 4. Cruzamento: `notebooks/etapas/04_Cruzamento_Bases.ipynb`

**Entrada:** as três saídas acima
**Saída:** `data/dataset_mestre_observatorio.csv` (5.570 linhas, 20 colunas)

| Passo | O que acontece | Linhas |
|---|---|---|
| 4.1 | Lê o código IBGE como texto nas três bases (evita perder zeros ou virar decimal) | - |
| 4.2 | Cria `participou_sinisa` (respondeu: 4.845; não respondeu: 725) | 5.570 |
| 4.3 | Agrupa o Atlas por município: número de desastres, danos materiais, prejuízo público e pessoas afetadas (3.111 municípios com registro) | 3.111 |
| 4.4 | Junção à esquerda com a CAPAG a partir do SINISA (5.567 encontram par; sem linha na CAPAG: Calçoene/AP, Fernando de Noronha/PE e Brasília/DF) | 5.570 |
| 4.5 | Junção à esquerda com o Atlas agrupado; município sem registro recebe 0 (2.459) | 5.570 |
| 4.6 | Cria `tem_registro_atlas` (3.111) e `tem_dano_atlas` (danos + prejuízo > 0: 2.702) | 5.570 |

## 5. Funil, ordem e valores: `notebooks/etapas/05_Painel_Final.ipynb`

**Entrada:** `data/dataset_mestre_observatorio.csv`
**Saída:** `data/painel/` (8 arquivos)

| Passo | Regra | Municípios |
|---|---|---|
| 5.1 | Todos | 5.570 |
| 5.2 | Responderam o SINISA 2024 | 4.845 |
| 5.3 | Declaram moradias em risco (> 0) | 2.238 |
| 5.4 | Sem CAPAG A ou B (decisão 1) | 1.031 |
| 5.5 | Têm o risco mapeado | 621 |
| 5.6 | Críticos no SINISA e com dano em R$ no Atlas (decisão 2) | **318 elegíveis** |
| 5.7 | Ordem pelo número de moradias em risco; desempate por desastres e pessoas afetadas (lista A) | 20 |
| 5.8 | Valor de referência: mesmo R$ por moradia (R$ 319,40), piso R$ 3 mi e teto R$ 15 mi, achado por bisseção para somar exatamente R$ 150 mi; o resíduo de centavos vai para o primeiro município proporcional | 20 |
| 5.9 | Suplentes: os outros 298 elegíveis, na mesma ordem | 298 |
| 5.10 | Alternativa estudada (C): nota = 70% do percentil das moradias em risco + 30% do percentil da parte da cidade em risco | 20 |

Conferências automáticas no notebook: o funil termina em 318, a soma dos valores é R$ 150.000.000,00 nas duas listas, as 20 da lista A somam 713.211 moradias e 341.914 pessoas afetadas.
