📺 Friends Analytics — Engenharia e Análise de Dados

Projeto de portfólio desenvolvido para demonstrar habilidades em engenharia de dados, análise exploratória e consumo de APIs REST, utilizando como base os dados da série Friends (1994–2004).

📌 Sobre o projeto

Este projeto aplica técnicas reais de engenharia e análise de dados sobre um dataset público da série Friends. O objetivo é demonstrar, de forma prática, o pipeline completo de um projeto de dados, desde a coleta e limpeza até a análise e visualização dos resultados.

A série foi escolhida por ser uma das minhas preferidas, além de possuir um dataset rico, com mais de 60 mil falas distribuídas em 10 temporadas e 236 episódios, o que permite análises variadas e com resultados visualmente interessantes.

🗂️ Estrutura do projeto
friends/
├── data/
│   ├── raw/
│   │   ├── friends_quotes.csv
│   │   ├── friends_episodes_v2.csv
│   │   └── friends_episodes_v3.csv
│   │
│   └── processed/
│       ├── episodios_omdb.csv
│       └── sentimento_falas.csv
│
├── notebooks/
│   ├── 01_exploracao_inicial.ipynb
│   ├── 02_tempo_de_fala.ipynb
│   ├── 03_coleta_api.ipynb
│   ├── 04_analises.ipynb
│   ├── 05_feriados_vs_rating.ipynb
│   └── 06_palavras_mais_usadas.ipynb
│
├── outputs/
├── .env
├── .gitignore
└── README.md
🗃️ Fontes de dados
Datasets estáticos (Kaggle)
Arquivo	Descrição	Linhas	Colunas
friends_quotes.csv	Falas de todos os personagens	60.291	6
friends_episodes_v2.csv	Metadados dos episódios	235	8
friends_episodes_v3.csv	Metadados com número do episódio	236	9
Colunas do friends_quotes.csv
Coluna	Tipo	Descrição
author	string	Nome do personagem que falou
episode_number	float	Número do episódio na temporada
episode_title	string	Título do episódio
quote	string	Texto da fala
quote_order	float	Ordem da fala no episódio
season	float	Número da temporada
Colunas do friends_episodes_v3.csv
Coluna	Tipo	Descrição
Year_of_prod	int	Ano de produção
Season	int	Temporada
Episode Number	int	Número do episódio
Episode_Title	string	Título do episódio
Duration	int	Duração em minutos
Summary	string	Resumo do episódio
Director	string	Nome do diretor
Stars	float	Rating IMDb
Votes	int	Número de votos
🌐 API REST (OMDb API)

O projeto consome a OMDb API (Open Movie Database) para coletar dados atualizados de rating e votos diretamente do IMDb.

Endpoint utilizado
GET https://www.omdbapi.com/?t=Friends&Season={s}&Episode={e}&apikey={key}
Campos coletados
Campo	Descrição
Title	Título do episódio
Released	Data de estreia
imdbRating	Rating IMDb
imdbVotes	Número de votos
Runtime	Duração
Plot	Sinopse

Os dados foram salvos em data/processed/episodios_omdb.csv.

🔧 Tecnologias utilizadas
Tecnologia	Uso
Python	Linguagem principal
Pandas	Manipulação de dados
Matplotlib	Gráficos
Seaborn	Visualizações
Requests	Consumo de API
python-dotenv	Variáveis de ambiente
Jupyter Notebook	Desenvolvimento
🧹 Engenharia de dados
1. Correção de tipos
quotes["season"] = quotes["season"].astype(int)
quotes["episode_number"] = quotes["episode_number"].astype(int)
2. Tratamento da API
Conversão de strings para números
Uso de pd.to_numeric(errors="coerce")
Remoção de vírgulas em números (7,440 → 7440)
3. Dados inconsistentes

A temporada 2 apresentou inconsistência grave (menos de 10% das falas esperadas).

Decisão: excluir da análise e documentar.

📊 Análises realizadas
1. Falas por personagem

Notebook: 02_tempo_de_fala.ipynb

Rachel tem mais falas no total
Ross tem pico na temporada 3
Distribuição equilibrada entre personagens
2. Ranking geral

Notebook: 04_analises.ipynb

Ranking total de falas por personagem.

3. Rating por temporada

Notebook: 04_analises.ipynb

Cálculo do rating médio por temporada usando dados da OMDb.

4. Episódios de feriado

Notebook: 05_feriados_vs_rating.ipynb

Uso da biblioteca holidays
Janela de 7 dias para identificar episódios temáticos
⚠️ Limitações
Limitação	Impacto	Solução
Temporada 2 incompleta	Distorção	Exclusão
API retorna 235 episódios	Diferença de 1 episódio	Documentado
Rating atual (não histórico)	Viés	Considerado
🚀 Como executar
1. Clone
git clone https://github.com/seu-usuario/friends-analytics.git
cd friends-analytics
2. Instale dependências
pip install pandas numpy matplotlib seaborn nltk wordcloud requests python-dotenv holidays
3. Configure a API

Crie .env:

OMDB_KEY=sua_chave
4. Baixe datasets

Coloque em data/raw/:

friends_quotes.csv
friends_episodes_v3.csv
5. Execute os notebooks
01_exploracao_inicial.ipynb
02_tempo_de_fala.ipynb
03_coleta_api.ipynb
04_analises.ipynb
05_feriados_vs_rating.ipynb
