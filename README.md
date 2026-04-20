# 📺 Friends Analytics — Engenharia e Análise de Dados

> Projeto de portfólio desenvolvido para demonstrar habilidades em engenharia de dados, análise exploratória e consumo de APIs REST, utilizando como base os dados da série **Friends (1994–2004)**.

---

## 📌 Sobre o projeto

Este projeto aplica técnicas reais de engenharia e análise de dados sobre um dataset público da série Friends. O objetivo é demonstrar, de forma prática, o pipeline completo de um projeto de dados, desde a coleta e limpeza até a análise e visualização dos resultados.

A série foi escolhida por ser uma das minhas preferidas, além de possuir um dataset rico, com mais de 60 mil falas distribuídas em 10 temporadas e 236 episódios, o que permite análises variadas e com resultados visualmente interessantes.

---

## 🗂️ Estrutura do projeto

```
friends/
│
├── data/
│   ├── raw/                        ← Dados originais, sem nenhuma limpeza
│   │   ├── friends_quotes.csv      ← 60.291 falas da série
│   │   ├── friends_episodes_v2.csv ← Metadados de 235 episódios
│   │   └── friends_episodes_v3.csv ← Versão atualizada com número do episódio
│   │
│   └── processed/                  ← Dados limpos e transformados
│       ├── episodios_omdb.csv      ← Dados coletados via OMDb API
│       └── sentimento_falas.csv    ← Resultado da análise de sentimento
│
├── notebooks/
│   ├── 01_exploracao_inicial.ipynb ← Carregamento e inspeção dos dados
│   ├── 02_tempo_de_fala.ipynb      ← Análise de falas por personagem
│   ├── 03_coleta_api.ipynb         ← Coleta de dados via OMDb API
│   ├── 04_analises.ipynb           ← Ranking de falas e rating por temporada
│   ├── 05_feriados_vs_rating.ipynb ← Episódios de feriado vs rating IMDb
│   └── 06_palavras_mais_usadas.ipynb ← Nuvem de palavras por personagem
│
├── outputs/                        ← Gráficos e visualizações gerados
├── .env                            ← Chave da API (não versionado)
├── .gitignore
└── README.md
```

---

## 🗃️ Fontes de dados

### Datasets estáticos (Kaggle)

| Arquivo | Descrição | Linhas | Colunas |
|---|---|---|---|
| `friends_quotes.csv` | Falas de todos os personagens | 60.291 | 6 |
| `friends_episodes_v2.csv` | Metadados dos episódios | 235 | 8 |
| `friends_episodes_v3.csv` | Metadados com número do episódio | 236 | 9 |

**Colunas do `friends_quotes.csv`:**

| Coluna | Tipo | Descrição |
|---|---|---|
| `author` | string | Nome do personagem que falou |
| `episode_number` | float | Número do episódio na temporada |
| `episode_title` | string | Título do episódio |
| `quote` | string | Texto da fala |
| `quote_order` | float | Ordem da fala no episódio |
| `season` | float | Número da temporada |

**Colunas do `friends_episodes_v3.csv`:**

| Coluna | Tipo | Descrição |
|---|---|---|
| `Year_of_prod` | int | Ano de produção |
| `Season` | int | Temporada |
| `Episode Number` | int | Número do episódio |
| `Episode_Title` | string | Título do episódio |
| `Duration` | int | Duração em minutos |
| `Summary` | string | Resumo do episódio |
| `Director` | string | Nome do diretor |
| `Stars` | float | Rating IMDb |
| `Votes` | int | Número de votos no IMDb |

---

### API REST (OMDb API)

Além dos datasets estáticos, o projeto consome a **OMDb API** (Open Movie Database) para coletar dados atualizados de rating e votos diretamente do IMDb para cada episódio.

**Endpoint utilizado:**
```
GET https://www.omdbapi.com/?t=Friends&Season={s}&Episode={e}&apikey={key}
```

**Campos coletados via API:**

| Campo | Descrição |
|---|---|
| `Title` | Título do episódio |
| `Released` | Data de estreia |
| `imdbRating` | Rating IMDb (0–10) |
| `imdbVotes` | Número de votos |
| `Runtime` | Duração |
| `Plot` | Sinopse |

O resultado foi salvo em `data/processed/episodios_omdb.csv` com 235 episódios coletados.

---

## 🔧 Tecnologias utilizadas

| Tecnologia | Uso no projeto |
|---|---|
| Python 3.13 | Linguagem principal |
| Pandas | Manipulação e limpeza de dados |
| Matplotlib | Geração de gráficos |
| Seaborn | Visualizações estatísticas |
| Requests | Consumo da OMDb API |
| python-dotenv | Gerenciamento seguro da chave da API |
| Jupyter Notebook | Ambiente de desenvolvimento e documentação |

---

## 🧹 Etapa de engenharia de dados

Antes de qualquer análise, os dados passaram por um processo de limpeza e transformação:

**1. Correção de tipos de dados**

As colunas `season` e `episode_number` foram lidas pelo Pandas como `float64` (`1.0`, `2.0`...) em vez de `int64` (`1`, `2`...), então foi corrigido explicitamente antes de qualquer agrupamento ou visualização.

```python
quotes['season'] = quotes['season'].astype(int)
quotes['episode_number'] = quotes['episode_number'].astype(int)
```

**2. Tratamento de dados da API**

Os dados retornados pela OMDb API chegam todos como texto. A coluna `rating_imdb` foi convertida para `float` usando `pd.to_numeric` com `errors='coerce'` — que substitui valores inválidos como `"N/A"` por `NaN` em vez de travar o código. A coluna `votos_imdb` exigiu remoção da vírgula antes da conversão (`"7,440"` → `7440`).

**3. Identificação e tratamento de dados incompletos**

Durante a análise de falas por temporada, foi identificado que a **temporada 2 possui dados inconsistentes** no dataset original, enquanto as demais temporadas têm entre 750 e 1.100 falas por personagem, a temporada 2 registra apenas 43 a 81 falas por personagem. Isso representa menos de 10% do esperado, indicando falha na extração dos dados originais.

**Decisão tomada:** a temporada 2 foi excluída das análises de falas e sinalizada visualmente nos gráficos com uma anotação explicativa. Essa limitação está documentada na seção abaixo.

---

## 📊 Análises realizadas

### 1. Falas por personagem por temporada
**Notebook:** `02_tempo_de_fala.ipynb`

Contagem de falas dos 6 personagens principais ao longo das 10 temporadas. Permite visualizar quais personagens dominaram cada temporada e como o protagonismo se distribuiu ao longo da série.

**Principais descobertas:**
- Rachel é a personagem com mais falas no total da série.
- Ross teve pico de falas na temporada 3. Esse motivo é explicado pelo falo de na terceira temporada, o romance de Ross e Rachel atingir o seu maior pico e o ponto mais crítico.
- A distribuição de falas é relativamente equilibrada entre os 6 personagens.

---

### 2. Ranking geral de falas
**Notebook:** `04_analises.ipynb`

Ranking total de falas de cada personagem principal considerando todas as temporadas (exceto a 2, por dados incompletos).

---

### 3. Rating médio por temporada
**Notebook:** `04_analises.ipynb`

Cruzamento dos dados da OMDb API com as temporadas para calcular o rating médio IMDb por temporada. Permite identificar o arco de qualidade percebida pelo público ao longo dos anos.

---

### 4. Episódios de feriado vs rating
**Notebook:** `05_feriados_vs_rating.ipynb`

Análise investigativa que cruza a data de estreia de cada episódio com o calendário de feriados americanos (Thanksgiving, Natal, Halloween, Valentine's Day, Réveillon) para verificar se episódios temáticos têm rating diferente dos episódios comuns.

A biblioteca `holidays` foi utilizada para mapear automaticamente todos os feriados americanos de 1994 a 2004. Uma janela de 7 dias foi aplicada para capturar episódios próximos às datas comemorativas.


---

## ⚠️ Limitações conhecidas do dataset

| Limitação | Impacto | Decisão tomada |
|---|---|---|
| Temporada 2 com dados incompletos no `friends_quotes.csv` | Análises de falas por temporada distorcidas | Temporada 2 excluída e sinalizada nos gráficos |
| OMDb API retorna 235 episódios (o piloto pode estar agrupado) | Diferença de 1 episódio em relação ao total oficial de 236 | Documentado, sem impacto nas análises |
| Ratings do IMDb refletem avaliações atuais, não da época de exibição | Viés de nostalgia pode inflar ratings de episódios antigos | Considerado na interpretação dos resultados |

---

## 🚀 Como executar o projeto

**1. Clone o repositório**
```bash
git clone https://github.com/seu-usuario/friends-analytics.git
cd friends-analytics
```

**2. Instale as dependências**
```bash
pip install pandas numpy matplotlib seaborn nltk wordcloud requests python-dotenv holidays
```

**3. Configure a chave da API**

Crie um arquivo `.env` na raiz do projeto:
```
OMDB_KEY=sua_chave_aqui
```

Obtenha sua chave gratuita em [omdbapi.com](https://www.omdbapi.com/).

**4. Baixe os datasets**

Baixe os arquivos do Kaggle e coloque em `data/raw/`:
- [friends_quotes.csv](https://www.kaggle.com/datasets/ryanstonebraker/friends-transcript)
- [friends_episodes_v3.csv](https://www.kaggle.com/datasets/rezaghari/friends-series-dataset)

**5. Execute os notebooks em ordem**
```
01_exploracao_inicial.ipynb
02_tempo_de_fala.ipynb
03_coleta_api.ipynb
04_analises.ipynb
05_feriados_vs_rating.ipynb
```

---

## 👩‍💻 Autora

**Isadora**
Estudante de Sistemas de Informação — em busca de estágio em dados