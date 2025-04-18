## Objetivo
Padronizar as mensagens de commit em todos os repositórios da organização **EHFAKE**, garantindo histórico limpo, rastreável e compatível com ferramentas de automação de changelog e versionamento semântico.

## Especificação adotada
- Baseada na especificação **Conventional Commits v1.0.0**.
- Alinhada à convenção Angular para corpo e rodapé.
- **chore** **não** é um tipo permitido.

### Tipos permitidos
| Tipo | Descrição | Exemplos de uso |
|------|-----------|-----------------|
| **build** | Mudanças que afetam o sistema de build ou dependências externas | ajustes no Dockerfile, atualização de dependências |
| **static** | Alterações em conteúdo estático (imagens, textos, ícones) | troca de logotipo, inclusão de assets |
| **ci** | Configuração ou ajustes de CI | atualização de workflow GitHub Actions |
| **cd** | Configurações de entrega contínua | modificação de pipeline de deploy |
| **docs** | Apenas mudanças na documentação | atualização do README, inclusão de tutoriais |
| **feat** | Adição de nova funcionalidade | criação de endpoint, novo componente React |
| **fix** | Correção de bug | ajuste de overflow, tratamento de exceção |
| **perf** | Melhoria de performance | cache em consulta, lazy‑loading |
| **refactor** | Refatoração sem alteração de comportamento | reorganização de módulos, renomeação de classes |
| **improve** | Pequenas melhorias visuais ou de usabilidade | melhoria de copy, ajuste de espaçamento |
| **style** | Mudanças de formatação e estilo de código | lint, prettier, remoção de imports |
| **test** | Adição ou ajuste de testes | novos testes unitários, correção de mocks |
| **revert** | Reversão de commit anterior | revert "feat(auth): add OAuth2" |

## Estrutura da mensagem
```
<tipo>(<escopo opcional>): <título sucinto no imperativo>

<corpo opcional>

<rodapé opcional>
```

### Regras
- **Título** ≤ 50 caracteres; corpo envolto a 72 caracteres por linha.
- Use **imperativo, tempo presente** (ex.: "corrige layout", "adiciona teste").
- Adote *kebab‑case* nos **escopos** (ex.: `backend/auth`, `frontend/ui`).
- Inclua `BREAKING CHANGE:` no rodapé para alterações incompatíveis.
- Relacione issues no rodapé (`Closes #123`).
- Commits devem ser **atômicos** (uma mudança lógica por commit).
- Prefira _squash & merge_ para unificar histórico de feature/bugfix.

### Exemplos
```
feat(backend/auth): adiciona rotação de refresh token

Implementa novo fluxo de rotação usando JWT ID, reduzindo riscos de reuse...

Closes #45
```
```
fix(frontend/ui): corrige alinhamento do botão de login em telas < 320px
```
```
docs(datasets): atualiza README com processo de curadoria
```

## Automação
Todos os repositórios possuem **Husky** + **commitlint** configurados para validar esta política. Pushes que não obedecerem às regras são rejeitados pelo servidor.

## Histórico do documento
| Versão | Data | Descrição | Autor |
|--------|------|-----------|-------|
| 1.0 | 18/04/2025 | Criação do documento | Equipe EHFAKE |

