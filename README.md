# Observatório do Clima
## Priorização de municípios para investimentos em prevenção de inundações

Projeto desenvolvido pela **Célula 4 no Grande Desafio DataConnect Solutions**, com foco no uso integrado de dados públicos para apoiar decisões de investimento em prevenção de desastres.

**Questão central:** diante de um orçamento de **R$ 150 milhões a fundo perdido**, quais municípios brasileiros devem ser priorizados para obras de proteção contra inundações, considerando a presença de moradias em risco, a capacidade de pagamento e o mapeamento de áreas vulneráveis?

---

## Visão geral

O projeto integra bases públicas de saneamento, capacidade fiscal e registros de desastres para estruturar um processo transparente de seleção e priorização municipal. O fluxo contempla tratamento dos dados, cruzamento por código IBGE, aplicação de critérios de elegibilidade, construção de rankings e preparação dos arquivos utilizados no painel de apoio à decisão.

O conjunto nacional de referência reúne **5.570 municípios**. Aplicados os critérios definidos para o estudo, o funil final identifica **318 municípios elegíveis**, dos quais são priorizados 20 para a proposta de alocação dos recursos.

## Objetivos

- Integrar bases públicas com diferentes estruturas e finalidades.
- Organizar indicadores relacionados a moradias em risco, capacidade fiscal e histórico de desastres.
- Definir critérios explícitos e reproduzíveis de elegibilidade e priorização.
- Apoiar a análise comparativa dos municípios e a comunicação dos resultados por meio de um painel.
- Documentar limitações dos dados e uma alternativa de ranking para avaliar a sensibilidade da seleção.

## Fontes de dados

| Base | Informação utilizada | Arquivo tratado |
|---|---|---|
| **SINISA 2024 — Águas Pluviais** (Ministério das Cidades) | Moradias em risco, mapeamento de áreas de risco, criticidade municipal e indicadores de drenagem | [`sinisa_limpo.csv`](data/sinisa_limpo.csv) |
| **CAPAG — prévia do ano-base 2025, posição de setembro de 2026** (Tesouro Nacional) | Capacidade de pagamento dos municípios | [`capag_nacional_tratada.csv`](data/capag_nacional_tratada.csv) |
| **Atlas Digital de Desastres / S2iD — 2021 a 2025** (MIDR) | Registros de desastres associados a chuvas, pessoas afetadas, danos materiais e prejuízos econômicos públicos | [`S2iD_Desastres_Hidrologicos_Selecao_Auditada.csv`](data/S2iD_Desastres_Hidrologicos_Selecao_Auditada.csv) |

As bases são relacionadas pelo **código IBGE do município**. O resultado consolidado está disponível em [`dataset_mestre_observatorio.csv`](data/dataset_mestre_observatorio.csv).

## Metodologia de priorização

### 1. Preparação e integração
Tratamento das bases, padronização dos campos relevantes e cruzamento dos registros pelo código IBGE, formando uma base nacional única.

### 2. Aplicação dos critérios de elegibilidade
O funil considera municípios que:

1. responderam ao SINISA 2024;
2. declararam moradias em risco de inundação;
3. não possuem classificação CAPAG A ou B;
4. possuem mapeamento de áreas de risco;
5. são classificados como críticos no SINISA e apresentam danos monetários registrados no Atlas.

**Resultado do funil: 318 municípios elegíveis.**

### 3. Ordenação e seleção
A seleção principal prioriza os **20 municípios com maior quantidade de moradias em risco**. Em caso de empate, são considerados o número de desastres e a quantidade de pessoas afetadas.

### 4. Referência para distribuição dos recursos
A proposta considera R$ 150 milhões no total, com valores de referência por município sujeitos a um piso de **R$ 3 milhões** e um teto de **R$ 15 milhões**. Os valores finais de repasse dependem da elaboração e análise dos planos de trabalho municipais.

### 5. Análise de alternativa
Também foi estudada uma classificação alternativa que combina **70% da quantidade de moradias em risco** e **30% da parcela da cidade em risco**. Essa comparação altera sete municípios em relação à seleção principal e permite examinar como a escolha dos indicadores influencia o resultado.

> **Importante:** a classificação é uma ferramenta de apoio à decisão baseada nos critérios definidos para o projeto; não substitui avaliações técnicas de engenharia, análise de viabilidade ou os procedimentos formais de seleção e repasse de recursos.

## Principais resultados e entregáveis

- Base mestre nacional com **5.570 municípios**.
- Funil de elegibilidade com **318 municípios**.
- Ranking principal e seleção dos **20 municípios prioritários**.
- Relação ordenada dos demais municípios elegíveis como suplentes.
- Arquivos estruturados para alimentar o painel no Looker Studio.
- Comparação entre o ranking principal e uma alternativa de priorização.
- Dicionário de dados ([`docs/DICIONARIO_DE_DADOS.md`](docs/DICIONARIO_DE_DADOS.md)) e registro das transformações de cada etapa ([`docs/LOG_TRANSFORMACOES.md`](docs/LOG_TRANSFORMACOES.md)), com as regras e limitações adotadas.

Os arquivos derivados estão organizados em [`data/painel/`](data/painel/).

| Arquivo | Finalidade |
|---|---|
| `painel_top20.csv` | Dados dos 20 municípios priorizados |
| `painel_funil.csv` | Etapas do processo de elegibilidade |
| `painel_dentro_fora.csv` | Comparação entre os 20 municípios priorizados e os 298 elegíveis que ficam de fora |
| `suplentes.csv` | Demais 298 municípios elegíveis, ordenados após a seleção principal |
| `ranking_318.csv` | Posições no ranking principal e na classificação alternativa |
| `alternativa_C_top20.csv`, `trocas_A_C.csv`, `comparacao_A_C.csv` | Resultados da análise de sensibilidade |

## Como reproduzir

O fluxo completo está em **um único notebook**, que refaz a análise desde os arquivos brutos e gera os arquivos do painel:

[![Abrir no Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Reginajose/Observatorio-Clima-Grande-desafio/blob/main/notebooks/Observatorio_Clima_Pipeline_Completo.ipynb)

**[notebooks/Observatorio_Clima_Pipeline_Completo.ipynb](notebooks/Observatorio_Clima_Pipeline_Completo.ipynb)**

- **Executar tudo** baixa do Google Drive os arquivos brutos (SINISA, CAPAG e Atlas) e refaz o tratamento, o cruzamento e o painel. Exige acesso aos arquivos no Drive e uma autorização na primeira execução.
- Para rodar **só a partir dos CSVs tratados** de [`data/`](data/), sem Drive, defina `PIPELINE_COMPLETA = False` na seção 0.2 do notebook.
- Ao final, a seção 2.1 compara os arquivos gerados com os versionados em `data/`.

As mesmas etapas também estão separadas em notebooks individuais, na ordem do pipeline, em [`notebooks/etapas/`](notebooks/etapas/). As fases anteriores e os materiais de validação ficam em [`notebooks/historico/`](notebooks/historico/), e as versões substituídas em [`notebooks/antigos/`](notebooks/antigos/).

## Tecnologias e competências aplicadas

- **Python** para tratamento e análise de dados.
- **Pandas** para limpeza, transformação, integração e validação de bases.
- **Integração de dados públicos** por código municipal do IBGE.
- **Análise exploratória e construção de indicadores** para apoiar critérios de priorização.
- **Ranking multicritério e análise de sensibilidade** para comparar cenários.
- **Looker Studio** para organização dos dados destinados à visualização dos resultados.
- **Documentação e rastreabilidade** das regras de seleção e das limitações das fontes.

## Limitações e cuidados de interpretação

- Os dados de moradias em risco são declarados pelos municípios ao SINISA 2024 e não equivalem, necessariamente, a uma medição de campo.
- Os registros do Atlas/S2iD dependem das informações reportadas pelos municípios; pode haver subnotificação.
- A CAPAG utilizada corresponde à prévia disponível na posição de setembro de 2026 e pode sofrer alterações.
- A ausência de registro não deve ser interpretada automaticamente como ausência de risco ou de ocorrência de desastre.
- A priorização depende dos critérios adotados. A análise alternativa evidencia que mudanças na ponderação dos indicadores podem alterar a lista selecionada.

## Estrutura do repositório

```text
.
├── README.md
├── requirements.txt                 bibliotecas Python usadas nos notebooks
├── LICENSE
├── data/                            bases tratadas, base mestre e arquivos de saída
│   └── painel/                      arquivos para o Looker Studio e comparação de cenários
├── docs/
│   ├── DICIONARIO_DE_DADOS.md       descrição das colunas da base mestre
│   └── LOG_TRANSFORMACOES.md        transformações aplicadas em cada etapa
└── notebooks/
    ├── Observatorio_Clima_Pipeline_Completo.ipynb   notebook principal (pipeline completo)
    ├── etapas/                      as etapas do pipeline, uma por notebook
    │   ├── 01_ETL_SINISA.ipynb
    │   ├── 02_Tratamento_CAPAG.ipynb
    │   ├── 03_Exploracao_Atlas_S2iD.ipynb
    │   ├── 04_Cruzamento_Bases.ipynb
    │   ├── 05_Painel_Final.ipynb
    │   └── 06_Grafico_Verba_vs_Danos.ipynb
    ├── historico/                   fases anteriores e validação (consulta)
    └── antigos/                     versões substituídas
```

---

**Projeto de análise de dados públicos para apoiar decisões de prevenção de desastres e priorização de investimentos.**

*Grande Desafio DataConnect Solutions — Célula 4.*
