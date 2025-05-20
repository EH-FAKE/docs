## <center> Guia de Contribuição para branch </center>

##  Objetivo

Estabelecer um modelo padronizado de ramificação baseado no Git Flow para todos os repositórios da organização **EHFAKE**, com o objetivo de garantir:

- Entregas previsíveis e organizadas;
- Releases controladas e auditáveis;
- Fluxo de desenvolvimento sustentável;
- Manutenção segura do código em produção.

##  Estrutura Geral de Branches

| Branch        | Função                                     | Origem           | Destino         | Convenção de nomes           |
| ------------- | ------------------------------------------ | ---------------- | --------------- | ---------------------------- |
| `main`        | Código estável e versionado (produção)     | release / hotfix | —               | —                            |
| `develop`     | Integração contínua de funcionalidades     | main             | release         | `feature/*`, `bugfix/*`      |
| `feature/*`   | Desenvolvimento de novas funcionalidades   | develop          | develop         | `feature/<issue>-<slug>`     |
| `bugfix/*`    | Correções durante o desenvolvimento        | develop          | develop         | `bugfix/<issue>-<slug>`      |
| `release/*`   | Preparação de versões                      | develop          | main & develop  | `release/vX.Y.Z`             |
| `hotfix/*`    | Correções críticas diretamente em produção | main             | main & develop  | `hotfix/vX.Y.Z`              |
| `doc/*`       | Atualizações de documentação (repo `docs`) | main             | main            | `doc/<issue>-<slug>`         |
| `gh-pages`    | Site gerado automaticamente pelo MkDocs    | `gh-deploy`      | —               | —                            |

## Convenções de Nomenclatura

- `<issue>`: número da issue vinculada (ex.: `42`);
- `<slug>`: descrição resumida da mudança em *kebab-case* (ex.: `melhora-autenticacao`);
- Versões seguem o padrão [Semantic Versioning (SemVer)](https://semver.org): `MAJOR.MINOR.PATCH`.

##  Fluxo de Desenvolvimento

1. **Feature**  
   `feature/123-melhora-busca` ← `develop` → PR → `develop` → *Squash & Merge*

2. **Release**  
   `release/v1.2.0` ← `develop` → PR → `main` & `develop` → tag `v1.2.0`

3. **Hotfix**  
   `hotfix/v1.2.1` ← `main` → PR → `main` & `develop` → tag `v1.2.1`

4. **Documentação**  
   `doc/456-guia-instalacao` ← `main` → PR → `main` → `mkdocs gh-deploy` → **gh-pages**

## Repositórios e Regras Específicas

| Repositório     | Branches principais | Observações                                                                                       |
|------------------|---------------------|---------------------------------------------------------------------------------------------------|
| **docs**         | `main`, `gh-pages`  | Atualizações feitas via branches `doc/*`. Deploy automático com `mkdocs gh-deploy`.              |
| **datasets**     | `main`              | Fluxo Git Flow simplificado; utiliza `feature/*`, `bugfix/*` e `release/*` conforme necessário.  |
| **fakebuster**   | `main`, `develop`   | Aplicações de Machine Learning com RAGFlow. Utiliza Git Flow completo.                           |
| **backend**      | `main`, `develop`   | Serviços e APIs da aplicação. Git Flow completo.                                                  |
| **frontend**     | `main`, `develop`   | Interface e frontend web. Git Flow completo.                                                      |

##  Regras de Merge

- Todo **Pull Request** deve:
  - Ter **pelo menos 1 reviewer aprovado**;
  - Passar por todas as validações de **CI** com sucesso.
  
- Estratégias de merge:
  - **Squash & Merge**: para `feature/*`, `bugfix/*` e `doc/*`;
  - **Merge commit**: para `release/*` e `hotfix/*` (preservando o histórico de versionamento);
  - **Tags**: aplicadas apenas na branch `main`.

##  Automação

- GitHub Actions:
  - Validação automática de nomes de branch com **expressões regulares**;
  - Workflows específicos para release, build e deploy por repositório;
  
- Artefatos:
  - São publicados automaticamente nas releases (`Docker`, `npm`, modelos `ML`, etc.).

##  Histórico do Documento

| Versão | Data       | Descrição            | Autor         |
|--------|------------|----------------------|---------------|
| 1.0    | 18/04/2025 | Criação inicial      | Equipe EHFAKE |
| 1.1    | 20/05/2025 | Revisão e melhoria   | Equipe EHFAKE |