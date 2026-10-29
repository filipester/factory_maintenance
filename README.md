# factory_maintenance

Sistema de **manutenção preventiva** para fábricas: cadastro de equipamentos, planos preventivos, ordens de serviço e indicadores de manutenção (MTBF, MTTR e disponibilidade).

Nasceu de uma necessidade real: a manutenção da fábrica onde trabalho não tem sistema. O controle depende de planilhas, anotações e memória. O objetivo é que o sistema seja instalado e usado no chão de fábrica, não apenas um projeto de estudo.

> 🚧 **Status:** em desenvolvimento, na fase de levantamento de requisitos (Fase 0).

---

## Problema

Sem um sistema, a manutenção vira reação: a máquina para, alguém conserta. Fica difícil responder perguntas básicas:

- Quais preventivas estão atrasadas? Quais vencem nesta semana?
- Quais equipamentos críticos param com mais frequência?
- Quanto tempo, em média, leva para colocar uma máquina de volta em operação?

## Domínio

| Entidade | Campos principais |
|---|---|
| **Equipamento** | código, nome, setor, fabricante/modelo, criticidade (A/B/C), ativo/inativo |
| **PlanoManutencao** | equipamento, descrição, periodicidade (dias), checklist, última execução |
| **OrdemServico** | tipo (preventiva/corretiva), equipamento, plano (se preventiva), abertura, início, fim, responsável, status, tempo de máquina parada, custo, observações |

Evoluções previstas: peças e consumíveis por OS, periodicidade por horímetro e anexos (fotos, manuais).

## Regras de negócio

- **Próxima manutenção** = última execução + periodicidade.
- **Vencidas e a vencer:** planos atrasados e os dos próximos N dias, ordenados por criticidade.
- **Conclusão de OS preventiva:** atualiza a última execução do plano e, com isso, a próxima data.
- **Indicadores** por equipamento, calculados a partir das OS corretivas:
  - **MTBF** (tempo médio entre falhas)
  - **MTTR** (tempo médio de reparo)
  - **Disponibilidade**

## Stack

| Camada | Tecnologia |
|---|---|
| Linguagem | Python 3, `dataclasses`, type hints |
| API | FastAPI + Pydantic (Swagger gerado automaticamente) |
| Banco | PostgreSQL + SQLAlchemy + Alembic |
| Testes | pytest (unitários e de integração) |
| Qualidade | ruff, `pyproject.toml` |
| Infra | Docker Compose, GitHub Actions |
| Interface | Swagger no início; depois, telas simples com Jinja2 |

## Endpoints planejados

| Rota | Descrição |
|---|---|
| `/equipamentos` | CRUD de equipamentos |
| `/planos` | CRUD de planos de manutenção preventiva |
| `/planos/vencidos` | Planos atrasados e a vencer nos próximos N dias |
| `/ordens` | Abertura, acompanhamento e conclusão de ordens de serviço |

## Estrutura planejada

```
factory_maintenance/
├── api/          # rotas FastAPI
├── services/     # regras de negócio (próxima data, vencidas, indicadores)
├── models/       # entidades e modelos do banco
├── tests/        # pytest
├── seed.py       # dados fictícios para desenvolvimento
├── docker-compose.yml
└── pyproject.toml
```

## Roadmap

- [ ] **Fase 0 — Levantamento:** equipamentos reais, como a manutenção é controlada hoje, criticidade, planos preventivos necessários e indicadores que a gestão quer acompanhar.
- [ ] **Fase 1 — Fundamentos:** modelar Equipamento, Plano e OS com `dataclasses`; regras de próxima data e vencidas em funções puras.
- [ ] **Fase 2 — API REST:** FastAPI com os endpoints acima e schemas Pydantic, ainda com armazenamento em memória/JSON.
- [ ] **Fase 3 — Banco:** PostgreSQL + SQLAlchemy + Alembic e `seed.py` com dados fictícios.
- [ ] **Fase 4 — Testes:** pytest nas regras (próxima data, vencidas, conclusão de OS, MTBF/MTTR) e testes de integração nos endpoints.
- [ ] **Fase 5 — Boas práticas** (contínua): ruff, `pyproject.toml`, estrutura de pastas, diagrama do banco no README.
- [ ] **Fase 6 — Logs:** `logging` nas operações de OS e troubleshooting guiado por logs.
- [ ] **Fase 7 — Diferenciais:** `docker compose` (API + Postgres), CI com GitHub Actions, login com perfis técnico/gestor (JWT) e implantação na fábrica.

## Como rodar

### Ambiente de desenvolvimento

Requer Python 3.14+. Com [uv](https://docs.astral.sh/uv/) (recomendado):

```bash
uv sync
```

Ou com pip:

```bash
python -m venv .venv
.venv\Scripts\activate        # Windows (no Linux/macOS: source .venv/bin/activate)
pip install -r requirements.txt -r requirements-dev.txt
```

Os arquivos `requirements*.txt` são gerados a partir do `uv.lock` com `uv export`.

### Aplicação

*Em breve.* A meta é subir tudo com:

```bash
docker compose up
```

e acessar a documentação interativa em `http://localhost:8000/docs`.

## Dados

Este repositório usa apenas **dados fictícios**. A instância usada na fábrica roda com dados reais, que não são versionados.

## Licença

[MIT](LICENSE)
