## Objetivo

Estabelecer um modelo único de ramificação (Git Flow) para todos os repositórios da organização **EHFAKE**, facilitando entregas previsíveis, releases controladas e manutenção de código em produção.

## Modelo Geral

| Branch       | Função                                 | Origem           | Destino        | Prefixos derivados       |
| ------------ | -------------------------------------- | ---------------- | -------------- | ------------------------ |
| **main**     | Código estável (produção)              | release / hotfix | —              | —                        |
| **develop**  | Integração contínua                    | main             | release        | feature/, bugfix/        |
| **feature/** | Novas funcionalidades                  | develop          | develop        | `feature/<issue>-<slug>` |
| **bugfix/**  | Correções em desenvolvimento           | develop          | develop        | `bugfix/<issue>-<slug>`  |
| **release/** | Preparação de versão                   | develop          | main & develop | `release/vX.Y.Z`         |
| **hotfix/**  | Correções críticas em produção         | main             | main & develop | `hotfix/vX.Y.Z`          |
| **docs/**     | Melhoria de documentação (repo *docs*) | main             | main           | `doc/<issue>-<slug>`     |
| **gh-pages** | Site gerado pelo MkDocs                | mkdocs gh‑deploy | —              | —                        |

### Convenções de nomenclatura

- `<issue>`: número da issue relacionada.
- `<slug>`: descrição curta em *kebab‑case*.
- Versões seguem [Semantic Versioning](https://semver.org) `MAJOR.MINOR.PATCH`.

## Fluxo Resumido

1. **Feature**: `feature/123-melhora-busca` ← develop → PR → develop → *Squash & Merge*.
2. **Release**: `release/v1.2.0` ← develop → PR → main & develop → tag `v1.2.0`.
3. **Hotfix**: `hotfix/v1.2.1` ← main → PR → main & develop → tag `v1.2.1`.
4. **Documentação**: `doc/456-guia-instalação` ← main → PR → main → `mkdocs gh-deploy` publica em **gh-pages**.

## Repositórios e Particularidades

| Repositório    | Branches principais | Observações                                                                                                                           |
| -------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| **docs**       | main, gh-pages      | Ciclo de utilização de branches `doc/*` para atualizações referente a documentação que posteriormente seguirá o fluxo de Commit e PR. |
| **datasets**   | main                | Ciclo Git Flow simplificado; crie branches `feature/*`, `bugfix/*`, etc. Releases de lotes grandes via `release/*`.                   |
| **fakebuster** | main, develop       | Implementa tratamento ML com RAGFlow. Fluxo Git Flow completo.                                                                        |
| **backend**    | main, develop       | API e serviços. Fluxo Git Flow completo.                                                                                              |
| **frontend**   | main, develop       | Interface web. Fluxo Git Flow completo.                                                                                               |

## Regras de Merge

- Todo PR necessita **1+ reviewer** e pipeline CI verde.
- **Squash & Merge** para feature, bugfix e doc.
- **Merge commit** preservado em release e hotfix para manter histórico explícito.
- Tags criadas **apenas** em `main`.

## Automação

- GitHub Actions valida nome de branch usando regex.
- Releases publicam artefatos correspondentes (containers, pacotes npm, modelos ML).

## Histórico do documento

| Versão | Data       | Descrição            | Autor         |
| ------ | ---------- | -------------------- | ------------- |
| 1.0    | 18/04/2025 | Criação do documento | Equipe EHFAKE |

