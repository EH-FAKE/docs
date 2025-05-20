# Contribuindo para o Check-up

Seja bem-vindo(a)! Ficamos felizes que você queira contribuir com o Check-up. Check-up é uma analisador de presença de desinformação em anúncios publicitários de saúde.

Este documento explica como contribuir com código, relatar bugs e adicionar novos portais de notícias ao Check-up.

## Sumário

- [Reportando Problemas](#reportando-problemas)
- [Configuração do Ambiente](#configuração-do-ambiente)
- [Adicionando um novo portal por conta própria](#adicionando-um-novo-portal-por-conta-própria)
- [Pull Requests](#pull-requests)
- [Mensagens de Commit](#mensagens-de-commit)
- [Revisão de Código](#revisão-de-código)

---

## Reportando Problemas

Antes de reportar um bug ou sugerir uma funcionalidade:

1. **Verifique as issues existentes** para evitar duplicatas.
2. Inclua detalhes:
   - Sistema operacional e ambiente (ex: `uname -a`, `python --version`).
   - Passos para reproduzir o problema.
   - Comportamento esperado vs. real.
   - Capturas de tela ou logs de erro (se aplicável).

---

## Configuração do Ambiente

Para configurar seu ambiente local:

1. Clone o Projeto
Faça um *fork* do repositório no GitHub e clone-o localmente:

   ```sh
   git clone https://github.com/<seu-usuário>/check-up.git
   cd check-up 
   ```

2. Mantenha Seu Fork Atualizado
Para sincronizar seu fork com o repositório original (aosfatos/check-up), execute:

   ```sh
   git remote add upstream https://github.com/aosfatos/check-up.git
   git pull --rebase upstream master
   ```

3. Pré-requisitos
   - [Docker](https://docs.docker.com/get-docker/) (versão 20+ recomendada)
   - [Make](https://www.gnu.org/software/make/) (já incluso na maioria dos sistemas Unix)

4. Comandos básicos
   Execute os serviços e prepare o ambiente com:

   ```sh
   make start     # Inicia os containers (banco de dados + shell Python)
   make init_db   # Cria as tabelas no banco de dados
   ```

> **NOTA**
>
> - O comando make start inicia um container com o banco de dados e outro com um shell interativo (Python) para executar comandos.
> - Consulte o Makefile para ver todos os comandos disponíveis (ex.: testes, parar containers).

---

## Adicionando um novo portal por conta própria

1. Adicionar novo portal ao Banco de Dados
Se você quiser adicionar um novo portal de notícias, como [Correio Braziliense](https://www.correiobraziliense.com.br/), insira as informações do portal no Banco de Dados:

   ```sh
   make bash

   python add_portal.py "Correio Braziliense" "https://www.correiobraziliense.com.br/"
   ```

2. Criar o spider
Crie um arquive `spiders/correio.py` com o seguinte conteúdo:

   ```python
   import scrapy

   from spiders.base import BaseSpider
   from spiders.items import URLItem


   class CorreioBrazilienseSpider(BaseSpider):
      name = "correiobraziliensespider"
      start_urls = ["https://www.correiobraziliense.com.br/"]
      allowed_domains = ["correiobraziliense.com.br"]

      def allow_url(self, entry_url):
         return "https://correiobraziliense.com.br" in entry_url

      def parse(self, response):
         url_item = URLItem()
         for entry in response.css('a[title][data-tb-link]::attr(href)')
               url = entry.attrib.get("href")
               if url and self.allow_url(url):
                  url_item["url"] = url
                  yield url_item
                  yield scrapy.Request(url=url, callback=self.parse)
   ```

   Este script irá buscar novas notícias publicas na página inicial do Correio Braziliense.

3. Criar o Script Playwright
   Também será necessário criar um script Playwright correspondente ao novo portal para coletar anúncios. Crie um arquivo em `plays/correio.py` com o seguinte código:

   ```python
   import time

   from playwright.sync_api import sync_playwright

   from plays.base import BasePlay
   from plays.items import AdItem, EntryItem
   from plays.utils import get_or_none
   from plog import logger


   class CorreioBraziliensePlay(BasePlay):
      name = "correiobraziliense"
      n_expected_ads = 10  # Add the minimum amount of expected ads

      @classmethod
      def match(cls, url):
         return "correiobraziliense.com.br" in url

      def find_items(self, html_content) -> AdItem:
         return AdItem(
               title=get_or_none(r'title="(.*?)"', html_content),
               url=get_or_none(r'href="(.*?)"', html_content),
               thumbnail_url=get_or_none(r'url\(&quot;(.*?)&quot;\)', html_content),
               tag=get_or_none(r'<span class="branding-inner".*?>(.*?)<\/span>', html_content),
         )

      def pre_run(self):
         pass

      def run(self) -> EntryItem:
         with sync_playwright() as p:
               browser = self.launch_browser(p)
               page = browser.new_page()
               logger.info(f"[{self.name}] Opening URL '{self.url}'...")
               page.goto(self.url, timeout=180_000)
               logger.info(f"[{self.name}] Searching for ads...")
               page.locator("#taboola-below-article-thumbnails").scroll_into_view_if_needed()

               entry_screenshot_path = self.take_screenshot(page, self.url, goto=False)
               entry_title = page.locator("title").inner_text()
               time.sleep(self.wait_time * 2)

               elements = page.locator(".videoCube")
               ad_items = []
               visible_elements = []
               for i in range(elements.count()):
                  element = elements.nth(i)
                  if not element.is_visible():
                     continue
                  visible_elements.append(element)
                  content = element.inner_html()
                  ad_item = self.find_items(content)
                  ad_items.append(ad_item)

               return EntryItem(
                  title=entry_title,
                  ads=ad_items,
                  url=self.url,
                  screenshot_path=entry_screenshot_path,
               )
   ```

   Este script irá procurar por anúncios nativos em cada uma das notícias coletadas no portal Correrio Braziliense.

> **Nota**:  
>
> - O método `run` usa [Playwright](https://playwright.dev/python/) para simular um navegador real.  
> - Adapte os seletores CSS (ex: `.videoCube`) conforme a estrutura de anúncios do portal.  
> - Para debug, use `logger.info` ou `page.screenshot(path="debug.png")`.  

---

## Pull Requests

1. **Discuta mudanças grandes primeiro**: Abra uma issue para propor funcionalidades ou refatorações complexas.
2. **Padrão de nomes para branches**:
   - Correções: `fix/issue-123-descrição`
   - Funcionalidades: `feat/issue-456-descrição`
3. **Utiliza o template de PR**: Abra um PR utilizando o template disponibilizado
4. **Atualize a documentação** se suas alterações afetarem funcionalidades existentes.

---

## Mensagens de Commit

Use [Conventional Commits](https://www.conventionalcommits.org/pt-br/):

```text
feat: adiciona autenticação de usuário
fix: corrige vazamento de memória no módulo X
docs: atualiza exemplos de uso da API
```

Exemplo:

```sh
git commit -s -m "feat: adiciona suporte ao portal X"
```

---

## Revisão de Código

- Revisores podem comentar em sua PR. Resolva os feedbacks com novos commits.
- Garanta que todos os testes passem e a cobertura se mantenha alta.
- Faça "squash" dos commits em unidades lógicas antes de mergear.

---

### Certificado de Origem do Desenvolvedor

Todos os commits devem incluir esta assinatura:

```text
Signed-off-by: Seu Nome <seu.email@exemplo.com>
```

Isso certifica conformidade com o [DCO 1.1](https://developercertificate.org/).

---

**Agradecemos sua contribuição!** 🎉
