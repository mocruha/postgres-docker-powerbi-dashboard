# Game Player Analytics

Análise de jogadores de um jogo fictício com PostgreSQL, Docker e Data Studio.

![Dashboard de demografia e preferências dos jogadores](dashboard.png)

## Visão geral

O projeto modela perfis, atividade e banimentos de jogadores em um banco PostgreSQL containerizado, responde perguntas de negócio com SQL e apresenta os resultados em um dashboard no Data Studio.

O objetivo é demonstrar um fluxo analítico completo: dados sintéticos, modelagem relacional, consultas de KPI, exportação e visualização.

## Problema

Quem são os jogadores, quanto tempo jogam, quais classes preferem e como esses fatores se relacionam com banimentos?

O projeto responde a nove perguntas:

- Quantos jogadores estão cadastrados?
- Quantos jogadores foram banidos?
- Qual a média e o máximo de horas jogadas?
- Qual a média e o máximo de nível?
- Como os jogadores se distribuem por gênero?
- Como os jogadores se distribuem por faixa etária?
- Quais classes de personagem são mais populares?
- Existe preferência de classe por gênero?
- Quais classes concentram mais horas jogadas?

## Dados

Dataset sintético com 500 jogadores, gerado para fins de estudo. Não contém dados reais de pessoas.

| Tabela | Grão | Colunas principais |
|---|---|---|
| `users.user_profile` | um jogador | `user_id`, `username`, `gender`, `birth_date` |
| `users.user_game_data` | um personagem por jogador | `user_id`, `character_class`, `time_played_hours`, `level` |
| `users.banned_users` | um banimento | `user_id`, `reason_banned`, `ban_date`, `appeal_status`, `last_login_date` |

`user_profile.user_id` é a chave primária. As outras tabelas referenciam esse identificador.

## Stack

- PostgreSQL 16
- Docker e Docker Compose
- SQL para DDL, seed e consultas analíticas
- Data Studio para visualização
- GNU Make para automação

## Arquitetura

```text
Dados sintéticos
      │
      ▼
PostgreSQL em Docker
      │
      ▼
View analytics.player_overview
      │
      ▼
Exportação CSV
      │
      ▼
Data Studio
```

## Análises

As consultas estão em [`sql/queries/queries.sql`](sql/queries/queries.sql).

- Total de jogadores e de banidos.
- Média e máximo de horas jogadas.
- Média e máximo de nível.
- Distribuição por gênero.
- Distribuição por faixa etária.
- Ranking de classes por número de jogadores.
- Preferência de classe por gênero.
- Classes com maior volume de horas jogadas.
- Motivos e status de recursos de banimento.

## Resultados

- 500 jogadores e 27 banidos: taxa de banimento de 5,4%.
- 1.242.135 horas jogadas, com média de 2.484 horas por jogador.
- Nível médio de 52,7, com máximo de 100.
- Archer é a classe mais popular (91 jogadores), seguida de Warrior (89).
- Witch concentra o maior volume de horas (226.393), mesmo sem ser a classe mais popular.
- 246 mulheres, 201 homens e 53 pessoas em outras identidades de gênero.
- Hacking e cheating somam 51,9% dos banimentos.

A distribuição etária do dataset atual tem concentração artificial na faixa 60+ e não deve ser usada como conclusão sobre jogadores reais.

## Dashboard

O dashboard apresenta:

- total de jogadores, banidos e nível médio;
- distribuição de gênero;
- faixa etária;
- preferência por classe;
- motivos de banimento.

<!-- Inserir o link público do Data Studio após publicar o relatório. -->

## Como executar

Pré-requisitos: Docker, Docker Compose e GNU Make.

```bash
git clone git@github.com:mocruha/game-player-analytics.git
cd game-player-analytics
cp .env.example .env
make db.reset
make db.export
```

O banco fica disponível em `localhost:5432`, com o database `game_analytics`.

O arquivo `data/exports/player_overview.csv` é gerado para upload no Data Studio:

1. Criar uma fonte de dados do tipo `Upload de CSV`.
2. Enviar `data/exports/player_overview.csv`.
3. Ajustar os tipos dos campos.
4. Atualizar o relatório.

## Estrutura

```text
.
├── dashboard.png
├── docker/
│   └── compose.yaml
├── sql/
│   ├── schema.sql
│   ├── seed/
│   │   ├── 01_user_profile.sql
│   │   ├── 02_user_game_data.sql
│   │   └── 03_banned_users.sql
│   └── queries/
│       └── queries.sql
├── .env.example
├── .gitignore
├── Makefile
└── README.md
```

## Limitações

- O dataset é sintético e não representa jogadores reais.
- A exportação CSV é estática; o dashboard precisa ser atualizado após cada nova exportação.
- A análise etária depende da qualidade das datas de nascimento geradas.
- O projeto não inclui dados pessoais reais.

## Autor

Gustavo Mocruha — [GitHub](https://github.com/mocruha)