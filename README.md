# Observatório do Clima — prioridade de verba contra inundação

Projeto da Célula 4 no Grande Desafio DataConnect.

**Pergunta de decisão:** com R$ 150 milhões a fundo perdido, a empenhar até 31/12/2026, quais 20 municípios brasileiros priorizar para obras de proteção contra inundação, entre os que têm moradias em risco, não conseguem crédito com garantia da União e já têm o risco mapeado?

## Fontes de dados

| Base | O que traz | Arquivo tratado |
|---|---|---|
| SINISA 2024 — Águas Pluviais (Ministério das Cidades) | Moradias em risco de inundação, mapeamento de risco, municípios críticos | `data/sinisa_limpo.csv` |
| CAPAG prévia ano-base 2025, posição set/2026 (Tesouro Nacional) | Capacidade de pagamento (A, B, C, D ou sem nota) | `data/capag_nacional_tratada.csv` |
| Atlas Digital de Desastres / S2iD 2021–2025 (MIDR) | Desastres ligados a chuvas (chuvas intensas, inundações, enxurradas, alagamentos e movimentos de massa), reconhecidos ou só registrados; pessoas afetadas, danos materiais e prejuízo econômico público | `data/S2iD_Desastres_Hidrologicos_Selecao_Auditada.csv` |

As três bases são unidas pelo código IBGE em `data/dataset_mestre_observatorio.csv` (5.570 municípios).

## Ordem dos notebooks

| # | Notebook | O que faz |
|---|---|---|
| 1 | `Notebook/ETL_SINISA.ipynb` | Limpeza do SINISA |
| 2 | `Notebook/Tratamento_CAPAG.ipynb` | Tratamento e validação da CAPAG |
| 3 | `Notebook/Cópia_de_Exploração_Atlas_S2iD.ipynb` | Diagnóstico e seleção dos desastres ligados a chuvas no Atlas |
| 4 | `Notebook/Cruzamento_Bases_Observatorio.ipynb` | Junta as três bases no dataset mestre |
| 5 | **`Notebook/Painel_Final.ipynb`** | **Resultado final:** funil, lista dos 20, valores de referência e os arquivos do painel (`data/painel/`) |
| — | `Notebook/Validacao_Independente_Celula4.ipynb` | Conferência: refaz o caminho a partir dos arquivos brutos oficiais |

Os demais notebooks (`Transformacao_Filtros`, `Transformacao-Filtros`, `Analise_de_Raking_Funil_Robustez`, `Gráfico_de_visualização…`, `analise_exploratória`) são etapas exploratórias anteriores às decisões finais e ficam como histórico.

## Critérios finais

1. **Funil:** respondeu o SINISA 2024 → declara moradias em risco → sem CAPAG A ou B → tem o risco mapeado → é crítico no SINISA e tem dano registrado no Atlas. Resultado: 318 municípios elegíveis.
2. **Ordem:** os 20 com mais moradias em risco (desempate por número de desastres e pessoas afetadas).
3. **Valores de referência:** o mesmo valor por moradia em risco para todos, com piso de R$ 3 mi e teto de R$ 15 mi, somando R$ 150 mi. O valor final de cada repasse é definido no plano de trabalho do município.
4. **Danos:** danos materiais + prejuízo econômico público.
5. **Alternativa estudada:** uma nota com 70% do número de moradias em risco e 30% da parte da cidade em risco troca 7 dos 20 municípios. Os resultados dela estão em `data/painel/alternativa_C_top20.csv` e `data/painel/trocas_A_C.csv`.

## Saídas (`data/painel/`)

| Arquivo | Uso |
|---|---|
| `painel_top20.csv`, `painel_funil.csv`, `painel_dentro_fora.csv` | Fontes de dados do painel no Looker Studio |
| `suplentes.csv` | Os outros 298 elegíveis, na ordem (21º ao 318º): a fila caso algum dos 20 não apresente o plano de trabalho |
| `alternativa_C_top20.csv`, `trocas_A_C.csv`, `comparacao_A_C.csv` | Alternativa estudada |
| `ranking_318.csv` | Posição de cada elegível nas duas ordens |

## Limites dos dados

- Moradias em risco são declaradas pela prefeitura ao SINISA 2024, não medidas em campo.
- O Atlas reúne desastres informados pelos municípios, reconhecidos ou não pela União, e há subnotificação.
- A nota CAPAG é a prévia de set/2026 e pode mudar.
