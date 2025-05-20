## <center> Guia de Contribuição para commit</center>

Este guia padroniza as mensagens de commit em todos os repositórios da organização EHFAKE, com o objetivo de manter um histórico de alterações limpo, rastreável e compatível com ferramentas de automação como geradores de changelog e versionamento semântico (SemVer).

## Especificação adotada
- Baseada na convenção [**Conventional Commits v1.0.0**](https://www.conventionalcommits.org/).
- Corpo e rodapé seguem o padrão da **convenção Angular**.
- O tipo `chore` **não é permitido**.


## Tipos de Commit Permitidos

| Tipo       | Descrição                                                  | Exemplos de uso                                           |
|------------|------------------------------------------------------------|------------------------------------------------------------|
| `build`    | Mudanças no sistema de build ou dependências externas      | Ajustes em `Dockerfile`, atualização do `package.json`     |
| `static`   | Alterações em conteúdo estático                            | Inclusão de imagens, atualização de ícones, textos fixos   |
| `ci`       | Configurações de integração contínua                       | Criação/ajuste de workflows do GitHub Actions              |
| `cd`       | Configurações de entrega contínua                          | Alterações em pipelines de deploy                          |
| `docs`     | Atualizações de documentação                               | Modificações no `README`, inclusão de novos guias          |
| `feat`     | Adição de nova funcionalidade                              | Implementação de novo endpoint, novo componente React      |
| `fix`      | Correção de erros ou falhas                                | Tratamento de exceções, correção de bug em layout          |
| `perf`     | Melhorias de performance                                   | Implementação de cache, `lazy-loading`                     |
| `refactor` | Refatorações sem alteração no comportamento                | Extração de funções, reorganização de pastas               |
| `improve`  | Melhorias visuais ou funcionais sem nova feature           | Ajustes de UI, melhorias em UX, mensagens mais claras      |
| `style`    | Alterações de formatação e estilo de código                | Ajustes com `prettier`, remoção de imports não utilizados  |
| `test`     | Criação ou modificação de testes                           | Testes unitários, mocks, cobertura                         |
| `revert`   | Reversão de commit anterior                                | `revert: feat(auth): adiciona OAuth2`                      |

---

##  Estrutura da Mensagem

```text
<tipo>(<escopo opcional>): <título no imperativo>

<corpo explicativo opcional>

<rodapé com BREAKING CHANGE e/ou referências de issue>
```

##  Regras Gerais

- **Título** com no máximo **50 caracteres**.
- **Corpo** com linhas de até **72 caracteres**.
- Utilize sempre o **modo imperativo e tempo presente**:

  - ✅ `adiciona`, `corrige`, `refatora`  
  - ❌ `adicionado`, `corrigido`, `refatorado`

- **Escopos** devem utilizar `kebab-case`, por exemplo:
  - `frontend/ui`
  - `backend/auth`

- Para alterações que **quebram compatibilidade**, adicione no rodapé:

  ```text
  BREAKING CHANGE: descrição da alteração incompatível
  ```

- Para **vincular issues**, utilize no rodapé:

  ```text
  Closes #123
  Related to #456
  ```

- Os commits devem ser **atômicos**: cada commit deve representar uma **única alteração lógica** no código.



## Exemplos de Commit 

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

##  Validação Automatizada

Todos os repositórios EHFAKE possuem validação de commits com:

- [**Husky**](https://typicode.github.io/husky) – ganchos `pre-commit` e `pre-push`
- [**Commitlint**](https://commitlint.js.org/) – valida mensagens conforme esta especificação
- [**Semantic Release**](https://semantic-release.gitbook.io/) – gera changelogs e versões com base nos commits

> Commits inválidos são automaticamente rejeitados.




## Histórico do documento

| Versão | Data | Descrição | Autor |
|--------|------|-----------|-------|
| 1.0 | 18/04/2025 | Criação do documento | Equipe EHFAKE |
| 1.1 | 19/04/2025 | Reformulação | Equipe EHFAKE |


##  Links Úteis

- [Conventional Commits](https://www.conventionalcommits.org/)
- [Semantic Versioning (SemVer)](https://semver.org/)
- [Angular Commit Guidelines](https://github.com/angular/angular/blob/main/CONTRIBUTING.md#commit)

