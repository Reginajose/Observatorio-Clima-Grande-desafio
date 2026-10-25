# Dicionário de dados

## `data/dataset_mestre_observatorio.csv`

Uma linha por município (5.570, todos os do SINISA). Chave: `Cod_IBGE_7`. Gerado por `notebooks/etapas/04_Cruzamento_Bases.ipynb` (parte 2 do notebook principal, `notebooks/Observatorio_Clima_Pipeline_Completo.ipynb`).

| # | Coluna | Tipo | O que é | Fonte | Tratamento | Valores |
|---|---|---|---|---|---|---|
| 1 | `Cod_IBGE_7` | Inteiro (7 dígitos) | Código IBGE do município; chave de junção | SINISA | Padronizado em 7 dígitos (na CAPAG vem como `cod_ibge`; no Atlas, `Cod_IBGE_Mun`) | 5.570, sem repetição |
| 2 | `Nome` | Texto | Nome do município | SINISA | Sem alteração | Há nomes repetidos em UFs diferentes |
| 3 | `UF` | Texto | Sigla da UF | SINISA | Sem alteração | 27 UFs |
| 4 | `mapeamento_area_risco` | Sim / Não / vazio | Se declara ter mapeamento das áreas de risco de inundação | SINISA 2024 · Formulários Técnicos · aba "Dados sobre Gestão de Riscos" · GAP2105 | Texto "null" virou vazio (não respondeu) | Sim 1.460 · Não 3.385 · vazio 725 |
| 5 | `qtd_domicilios_risco` | Número | Domicílios sujeitos a risco de inundação, declarados pela prefeitura | SINISA 2024 · mesma aba · GAP2110 | Convertido para número; "-" e texto viraram vazio; zeros mantidos | 0 a 175.838 · zero 2.607 · vazio 725 |
| 6 | `municipio_critico` | Sim / Não | Se o município é "Crítico" na lista federal usada pelo SINISA | SINISA 2024 · Indicadores AP2024 · coluna "Crítico" | Sem alteração | Sim 1.936 · Não 3.634 |
| 7 | `pct_vias_rede_drenagem` | Número (%) | Vias urbanas com rede de drenagem subterrânea | SINISA 2024 · Indicadores · IAP0002 | Convertido para número | 0 a 100 · vazio 2.102 |
| 8 | `investimento_percapita_drenagem` | Número (R$/hab.) | Investimento em drenagem por habitante | SINISA 2024 · Indicadores · IFD0010 | Convertido para número | 0 a 11.387,48 · vazio 1.560 |
| 9 | `pct_domicilios_risco` | Número (%) | Parte dos domicílios do município em risco de inundação | SINISA 2024 · Indicadores · IGR0001 | Convertido para número | 0 a 92,58 · vazio 725 |
| 10 | `participou_sinisa` | Verdadeiro / Falso | Se respondeu ao SINISA 2024 | Calculada no cruzamento | Verdadeiro quando `mapeamento_area_risco` não é vazio | 4.845 / 725 |
| 11 | `capag` | Texto | Nota de capacidade de pagamento | CAPAG (STN) · prévia ano-base 2025, posição set/2026 · aba "CAPAG Ano Base 2025" | Sem alteração; 3 municípios sem linha ficam vazios (Calçoene/AP, Fernando de Noronha/PE, Brasília/DF) | C 2.274 · A 1.096 · B 963 · n.d. 426 · A+ 418 · B+ 200 · n.e. 170 · D 20 · vazio 3 |
| 12 | `criterio_capag_cd` | Verdadeiro / Falso | Nota C ou D (regra antiga, mantida para comparação) | Calculada no tratamento da CAPAG | `capag` ∈ {C, D} | Verdadeiro 2.294 |
| 13 | `sem_nota_capag` | Verdadeiro / Falso | Sem nota (n.d. ou n.e.) | Calculada no tratamento da CAPAG | `capag` ∈ {n.d., n.e.} | Verdadeiro 596 |
| 14 | `criterio_fiscal_priorizacao` | Verdadeiro / Falso | Sem nota A ou B: sem garantia da União para empréstimo (decisão 1) | Calculada no tratamento da CAPAG | `capag` ∈ {C, D, n.d., n.e.} | Verdadeiro 2.890 |
| 15 | `total_desastres` | Inteiro | Desastres do grupo Hidrológico do Atlas entre 2021 e 2025 | Atlas · BD_Atlas v1.1 · aba "Atlas Valores Corrigidos" | Contagem de protocolos S2iD; sem registro = 0 | 0 a 64 · 3.111 municípios com ao menos 1 |
| 16 | `soma_danos_materiais` | Número (R$) | Soma dos danos materiais declarados (DM) | Atlas · mesma aba · `DM_total_danos_materiais` | Soma por município; sem registro = 0 | 0 a R$ 7,68 bi |
| 17 | `soma_prejuizo_publico` | Número (R$) | Soma dos prejuízos econômicos públicos declarados (PEPL) | Atlas · mesma aba · `PEPL_total_publico` | Soma por município; sem registro = 0 | 0 a R$ 496,6 mi |
| 18 | `danos_humanos` | Número (pessoas) | Pessoas afetadas: mortos, feridos, enfermos, desabrigados, desalojados e desaparecidos | Atlas · `DH_total_calculado` | Soma por evento e depois por município; quem foi afetado em dois eventos conta duas vezes | 0 a 209.747 · total Brasil 3.525.510 (bate com o total oficial) |
| 19 | `tem_registro_atlas` | Verdadeiro / Falso | Tem ao menos um desastre registrado | Calculada no cruzamento | `total_desastres` > 0 | Verdadeiro 3.111 |
| 20 | `tem_dano_atlas` | Verdadeiro / Falso | Teve dano em R$ registrado (decisão 2) | Calculada no cruzamento | DM + PEPL > 0 | Verdadeiro 2.702 |

## `data/painel/painel_top20.csv`

Os 20 da lista recomendada (A). Fonte do painel no Looker Studio.

| Coluna | O que é |
|---|---|
| `posicao` | Posição na lista (1 a 20) |
| `municipio`, `uf`, `municipio_uf`, `rotulo` | Nome, UF, "Nome (UF)" e "1. Nome (UF)" |
| `cod_ibge` | Código IBGE (7 dígitos) |
| `capag` | Nota CAPAG |
| `moradias_em_risco` | = `qtd_domicilios_risco` |
| `parte_da_cidade_em_risco` | = `pct_domicilios_risco` / 100 (fração, para o tipo Percentual do Looker) |
| `desastres_2021_25` | = `total_desastres` |
| `pessoas_afetadas_2021_25` | = `danos_humanos` |
| `danos_materiais`, `prejuizo_economico_publico` | DM e PEPL (R$) |
| `danos_total` | DM + PEPL (R$) |
| `valor_referencia` | Valor de referência da reserva (R$); soma R$ 150 mi. O valor final é definido no plano de trabalho |
| `rs_por_moradia` | `valor_referencia` / `moradias_em_risco` |
| `regra_valor` | teto (R$ 15 mi), piso (R$ 3 mi) ou proporcional |
| `lat_long` | Latitude e longitude do município (base kelvins/municipios-brasileiros), para mapa |

## Outros arquivos de `data/painel/`

| Arquivo | Linhas | Conteúdo |
|---|---|---|
| `painel_funil.csv` | 7 | `ordem`, `etapa`, `municipios`, `destaque` (a última etapa) |
| `painel_dentro_fora.csv` | 4 | `indicador` (Municípios / Moradias em risco), `ordem`, `grupo` (recebem / ficam de fora), `valor` |
| `suplentes.csv` | 298 | Os demais elegíveis na ordem (21º ao 318º): `posicao`, `municipio`, `uf`, `moradias_em_risco`, `parte_da_cidade_em_risco_pct` |
| `alternativa_C_top20.csv` | 20 | Os 20 da alternativa C, com moradias, %, pessoas afetadas, danos e valor de referência |
| `trocas_A_C.csv` | 14 | Os 7 que saem e os 7 que entram na alternativa C |
| `comparacao_A_C.csv` | 4 | Totais das duas listas e a diferença |
| `ranking_318.csv` | 318 | Os 318 elegíveis com `posicao_A` e `posicao_C` |
