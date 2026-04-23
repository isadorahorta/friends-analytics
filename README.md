
# 📺 Friends Analytics — Engenharia e Análise de Dados

Projeto de portfólio desenvolvido para demonstrar habilidades em engenharia de dados, análise exploratória e consumo de APIs REST, utilizando dados da série Friends (1994–2004).


## 🚀 About 
Este projeto aplica técnicas reais de engenharia e análise de dados sobre um dataset público da série Friends.

O objetivo é demonstrar, de forma prática, o pipeline completo de um projeto de dadosm, desde a coleta e limpeza até a análise e visualização.

O dataset possui mais de 60 mil falas distribuídas em 10 temporadas e 236 episódios, permitindo análises relevantes e visualmente interessantes.
## 🗂️ Estrutura do projeto

friends/  
│
├── data/  
│ ├── raw/  
│ │ ├── friends_quotes.csv  
│ │ ├── friends_episodes_v2.csv  
│ │ └── friends_episodes_v3.csv  
│ │
│ └── processed/  
│ ├── episodios_omdb.csv  
│ └── sentimento_falas.csv  
│
├── notebooks/  
│ ├── 01_exploracao_inicial.ipynb  
│ ├── 02_tempo_de_fala.ipynb  
│ ├── 03_coleta_api.ipynb  
│ ├── 04_analises.ipynb  
│ ├── 05_feriados_vs_rating.ipynb  
│ └── 06_palavras_mais_usadas.ipynb  
│
├── outputs/  
├── .env  
├── .gitignore  
└── README.md  
##  🗃️ Fontes de dados

### 📊 Datasets estáticos (Kaggle)


| Arquivo                    | Descrição                         | Linhas | Colunas |
|---------------------------|----------------------------------|--------|---------|
| friends_quotes.csv        | Falas dos personagens            | 60.291 | 6       |
| friends_episodes_v2.csv   | Metadados dos episódios          | 235    | 8       |
| friends_episodes_v3.csv   | Metadados com número do episódio | 236    | 9       |

---

### 📌 Estrutura das tabelas

#### friends_quotes.csv

| Coluna          | Tipo   | Descrição                |
|----------------|--------|-------------------------|
| author         | string | Nome do personagem      |
| episode_number | float  | Número do episódio      |
| episode_title  | string | Título do episódio      |
| quote          | string | Texto da fala           |
| quote_order    | float  | Ordem da fala           |
| season         | float  | Número da temporada     |

#### friends_episodes_v3.csv

| Coluna         | Tipo   | Descrição              |
|----------------|--------|-----------------------|
| Year_of_prod   | int    | Ano de produção       |
| Season         | int    | Temporada             |
| Episode Number | int    | Número do episódio    |
| Episode_Title  | string | Título                |
| Duration       | int    | Duração em minutos    |
| Summary        | string | Resumo                |
| Director       | string | Diretor               |
| Stars          | float  | Rating IMDb           |
| Votes          | int    | Número de votos       |

---



## 🌐 API REST (OMDb)
Este projeto utiliza a **OMDb API** para consultar metadados dos episódios da série *Friends*, incluindo título, data de lançamento, duração, sinopse, rating e número de votos no IMDb.

### Endpoint utilizado

```http
GET https://www.omdbapi.com/?t=Friends&Season={season}&Episode={episode}&apikey={OMDB_KEY}
```

    
A API é consumida no notebook 03_coleta_api.ipynb, onde são feitas requisições para cada episódio com base na temporada e no número do episódio.
Os dados retornados são tratados, padronizados e salvos em:

```data/processed/episodios_omdb.csv```

- A OMDb API retorna os valores numéricos como texto, então foi necessário realizar conversões de tipo no tratamento dos dados.  
- O campo imdbVotes contém separadores de milhar (ex.: 7,440), que foram removidos antes da conversão.  
- A API retornou metadados para 235 episódios, enquanto a base principal continha 236 episódios, o que foi documentado como limitação do projeto.


### Campos coletados

| Campo       | Descrição            |
|------------|---------------------|
| Title      | Título do episódio  |
| Released   | Data de estreia     |
| imdbRating | Rating IMDb         |
| imdbVotes  | Número de votos     |
| Runtime    | Duração             |
| Plot       | Sinopse             |


## Tech Stack

- Python  
- Pandas  
- Matplotlib  
- Seaborn  
- Requests  
- python-dotenv  
- Jupyter Notebook  

## 🧹 Engenharia de dados

### ✔️ Tratamentos realizados

#### 1. Correção de tipos:
```python
quotes["season"] = quotes["season"].astype(int)
quotes["episode_number"] = quotes["episode_number"].astype(int)
```
#### 2. Conversão de strings para números:  
```pd.to_numeric(errors="coerce")```  

#### 3. Limpeza de valores:  

* Remoção de separadores de milhar em números  
Exemplo: 7,440 → 7440
## ⚠️ Dados inconsistentes
A temporada 2 apresentou inconsistência grave ➡️ (menos de 10% das falas esperadas)

#### Decisão tomada:

- Exclusão da análise
- Documentação do problema
## 📊 Análises realizadas


### 🗣️ Falas por personagem
📓 `02_tempo_de_fala.ipynb`

- Rachel possui mais falas no total  
- Ross tem pico na temporada 3  
- Distribuição equilibrada entre personagens  

---

### 🏆 Ranking geral
📓 `04_analises.ipynb`

- Ranking total de falas por personagem  

---

### ⭐ Rating por temporada
📓 `04_analises.ipynb`

- Média de ratings usando dados da OMDb  

---

### 🎄 Episódios de feriado
📓 `05_feriados_vs_rating.ipynb`

- Uso da biblioteca `holidays`  
- Janela de 7 dias para classificação temática  
##  ⚠️ Limitações


| Limitação                  | Impacto                     | Solução        |
|--------------------------|----------------------------|---------------|
| Temporada 2 incompleta   | Distorção de análise       | Exclusão       |
| API retorna 235 episódios| Diferença de 1 episódio    | Documentado    |
| Rating atual (não histórico) | Possível viés         | Considerado    |
## 🚀 Como executar
### 1. Clone o repositório

```bash
git clone https://github.com/seu-usuario/friends-analytics.git
cd friends-analytics
```

### 2. Instale as dependências
```pip install pandas numpy matplotlib seaborn nltk wordcloud requests python-dotenv holidays```

### 3. Configure a API

Crie um arquivo .env:

```OMDB_KEY=sua_chave```

 4. Baixe os datasets

Coloque em data/raw/:

```friends_quotes.csv
friends_episodes_v3.csv
```  


### 5. Execute os notebooks
1. 01_exploracao_inicial.ipynb  
2. 02_tempo_de_fala.ipynb   
3. 03_coleta_api.ipynb  
4. 04_analises.ipynb  
5. 05_feriados_vs_rating.ipynb  
