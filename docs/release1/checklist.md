# Checklist do projeto de Gerência de Configuração Evolução de Software 

### Gerência e Controle de Versão
- [x] Repositório público no GitHub/GitLab (com histórico limpo e organizado)
- [x] Uso de `git-flow` ou similar para estratégia de branches
- [x] Versionamento semântico (SemVer) aplicado
- [x] Tags e releases publicados com **Release Notes** claras
- [ ] GitHub Actions / GitLab CI configurado com:
    - [ ] Build automatizado
    - [ ] Testes automatizados (unitários/integrados)
    - [ ] Linter (ex: ESLint, Flake8, etc.)
    - [ ] Validação de segurança e dependências (ex: Dependabot, Snyk)
- [x] Arquivos de configuração de ambiente: `Dockerfile`, `docker-compose.yml`, `.env.example`

---

### Documentação
- [x] `README.md` completo com:
  - [x] Visão geral do projeto (com prints de como funciona o projeto)
  - [x] Tecnologias utilizadas
  - [x] Como rodar localmente (instalação + execução)
  - [x] Como contribuir (passo a passo) - getting started - https://blog.discourse.org/tag/getting-started/
  - [x] Como usar a aplicação  (guia de usuário) - https://blog.discourse.org/tag/getting-started/
  - [x] Licença
- [x] `CONTRIBUTING.md` com diretrizes de contribuição
- [x] `CODE_OF_CONDUCT.md` com boas práticas de convivência
- [x] `CHANGELOG.md` com histórico de alterações
- [x] gitpage com:
  - [x] Landing page - visão de produto - ex: https://www.discourse.org/
  - [x] Arquitetura da solução
  - [x] Roadmap e backlog público
  - [ ] Dicionário de dados (se aplicável)
  - [x] Documentação técnica de como contribuir (community)

---

### Comunicação e Comunidade
- [x] Sistema de governança (ex: mantenedores, comitês, votação)
- [x] Templates para issues e pull requests
- [x] Etiquetas (labels) para organizar issues (ex: good first issue, bug, enhancement)
- [x] Agendas públicas de reuniões (caso ocorram)

---

### Licenciamento e Aspectos Legais
- [x] `LICENSE` com licença de software livre (ex: MIT, GPL, Apache 2.0)
- [x] Verificação de licenças das dependências utilizadas
- [x] Termos de uso e política de privacidade (para projetos web/app)

---

### Qualidade e Testabilidade
- [ ] Cobertura de testes mínima estabelecida e monitorada
- [ ] Testes end-to-end automatizados (se aplicável)
- [ ] Ferramentas de análise estática de código
- [ ] Monitoramento de qualidade com badges (ex: Codecov, SonarCloud)

---

### Sustentabilidade e Crescimento
- [x] Roadmap público com funcionalidades desejadas
- [x] Planejamento de onboarding de novos contribuidores (documentacao de onboarding)

---

### Infraestrutura e Deploy (Opcional)
- [ ] Deploy automatizado (CI/CD) para ambiente de homologação/produção
- [ ] Infraestrutura como código (IaC) para ambientes cloud (ex: Terraform, Ansible)
- [ ] Observabilidade básica: logs, métricas e alertas (ex: Prometheus, Grafana, Sentry)