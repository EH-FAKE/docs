# Tutorial: Pipeline de Dados dos Scraps

## Contexto do Projeto

O projeto EH-FAKE tem como objetivo principal a identificação e classificação de notícias desinformativas. Para isso, é fundamental um pipeline robusto de coleta e processamento de dados, que envolve a extração de conteúdo de notícias de diversas fontes. Este documento detalha o fluxo de dados desde a coleta inicial de URLs até a extração e classificação do conteúdo.

## Visão Geral do Pipeline

O pipeline de dados dos scraps no projeto EH-FAKE é composto por três fases principais:

1.  **Coleta de URLs (Spiders)**: Responsável por navegar em portais de notícias e identificar URLs de artigos válidos.
2.  **Extração de Conteúdo (Plays)**: Acessa as URLs coletadas e extrai o conteúdo textual relevante (título, descrição, corpo da notícia, tags).
3.  **Classificação e Armazenamento**: Processa o conteúdo extraído, classifica-o utilizando um modelo de linguagem (LLM) e armazena os dados e artefatos gerados.

Este fluxo garante que o projeto tenha acesso a um volume contínuo de dados de notícias para análise e identificação de desinformação.




## 🕷️ SPIDERS - Coleta de URLs

### Função dos Spiders

Os *spiders* são componentes do pipeline responsáveis por navegar nas páginas iniciais dos portais de notícias e coletar URLs de artigos/notícias válidos. Eles atuam como a primeira etapa na aquisição de dados, identificando e filtrando links que serão posteriormente processados para extração de conteúdo.

### ⚠️ Alterações Necessárias nos Spiders Existentes

Para garantir a eficiência e a relevância dos dados coletados, algumas alterações são cruciais nos *spiders* já implementados:

#### 1. Melhorar Filtragem de URLs

Muitos *spiders* existentes podem não filtrar adequadamente os URLs, resultando na coleta de links irrelevantes ou duplicados. É fundamental aprimorar a lógica de filtragem para incluir apenas URLs que correspondam a artigos de notícias. O *spider* `gazetaDoPovo.py` serve como um excelente exemplo de implementação de boa filtragem, utilizando análise da URL para blacklist de seções puras e validação de segmentos e *slugs* para identificar URLs de notícias legítimas.

**Exemplo de filtragem a ser evitada (r7.py):**

```python
def allow_url(self, entry_url):
    return (
        entry_url.startswith("https://")
        and len(entry_url) > 100
        and re.match(
            r"https://(entretenimento|esportes|record|noticias)\.r7\.com", entry_url
        )
    )
```

**Exemplo de filtragem recomendada (gazetaDoPovo.py):**

```python
def allow_url(self, url: str) -> bool:
    p = urlparse(url)
    path = p.path.rstrip("/")

    # 1) blacklist pure sections
    if path in {"/videos", "/vozes", "/podcasts", "/newsletter", "/ebooks"}:
        self.logger.info(f"Blacklisted URL: {url}")
        return False

    # 2) require at least two non-empty segments (section + slug)
    segments = [seg for seg in path.split("/") if seg]
    if len(segments) < 2:
        self.logger.info(f"Blacklisted URL: {url}")
        return False

    slug = segments[-1]
    # 3a) long slugs by hyphens or 3b) by character length
    if slug.count("-") >= 3 or len(slug) > 30:
        return True

    return False
```

#### 2. Remover `yield scrapy.Request` Recursivos

O objetivo dos *spiders* é coletar apenas as URLs de notícias presentes na página inicial do portal, sem realizar um *crawling* recursivo. Portanto, quaisquer chamadas `yield scrapy.Request` que iniciem novas requisições recursivas devem ser removidas do método `parse`.

**Exemplo de código a ser removido:**

```python
def parse(self, response):
    seen = set()
    for entry in response.css("a"):
        url = entry.attrib.get("href")
        if url and url not in seen and self.allow_url(url):
            seen.add(url)
            yield URLItem(url=url)
            yield scrapy.Request(url=url, callback=self.parse)  # ← REMOVER ESTA LINHA
```

**Exemplo de código correto:**

```python
def parse(self, response):
    seen = set()
    for entry in response.css("a"):
        url = entry.attrib.get("href")
        if url and url not in seen and self.allow_url(url):
            seen.add(url)
            yield URLItem(url=url)  # ← MANTER APENAS ISTO
```

### 🆕 Criando um Novo Spider

Para adicionar um novo portal ao pipeline de coleta, siga a estrutura base para a criação de um novo *spider*. É crucial adaptar a lógica de filtragem de URLs (`allow_url`) e os seletores CSS/XPath (`response.css` ou `response.xpath`) para o portal específico, garantindo que apenas URLs de notícias válidas sejam coletadas.

**Estrutura base para um novo spider:**

```python
import scrapy
from spiders.base import BaseSpider
from spiders.items import URLItem
from urllib.parse import urlparse

class NovoPortalSpider(BaseSpider):
    name = "novoportalspider"
    start_urls = ["https://www.novoportal.com.br/"]
    allowed_domains = ["novoportal.com.br"]

    custom_settings = {
        **BaseSpider.custom_settings,
        "COOKIES_ENABLED": True,
        "DOWNLOAD_DELAY": 3,
        "DEFAULT_REQUEST_HEADERS": {
            "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8",
            "Accept-Language": "pt-BR,pt;q=0.9,en-US;q=0.8,en;q=0.7",
            "Accept-Encoding": "gzip, deflate, br",
            "Connection": "keep-alive",
            "Upgrade-Insecure-Requests": "1",
            "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36",
            "Cache-Control": "max-age=0",
        }
    }

    def allow_url(self, url: str) -> bool:
        """
        Implementar lógica específica do portal para filtrar apenas URLs de notícias
        """
        p = urlparse(url)
        path = p.path.rstrip("/")
        
        # Exemplo de filtragem - adaptar para cada portal
        segments = [seg for seg in path.split("/") if seg]
        if len(segments) < 2:
            return False
            
        # Verificar se é uma notícia real (slug longo, múltiplos hífens, etc.)
        slug = segments[-1]
        return slug.count("-") >= 2 or len(slug) > 20

    def start_requests(self):
        for url in self.start_urls:
            yield scrapy.Request(
                url,
                callback=self.parse,
                dont_filter=True,
                meta={"dont_redirect": True, "handle_httpstatus_list": [403]},
            )

    def parse(self, response):
        # Adaptar seletor CSS/XPath para o portal específico
        links = response.css("a")  # ou response.xpath("//a")
        
        for link in links:
            url = link.attrib.get("href")
            if not url:
                continue
                
            # Construir URL absoluta
            full_url = response.urljoin(url)
            full_url = full_url.split("#", 1)[0].split("", 1)[0]  # Remove fragmentos e query params
            
            if self.allow_url(full_url):
                yield URLItem(url=full_url)
```




## 🎭 PLAYS - Extração de Conteúdo

### Função dos Plays

Os *plays* são a segunda etapa do pipeline de dados, responsáveis por acessar cada URL de notícia coletada pelos *spiders* e extrair o conteúdo textual relevante. Isso inclui o título, a descrição (subtítulo/resumo), o corpo completo da matéria e as tags/categorias associadas à notícia.

### ⚠️ Alterações Necessárias nos Plays Existentes

Para otimizar a extração de conteúdo e focar nos dados necessários para a análise de desinformação, as seguintes alterações são essenciais nos *plays* existentes:

#### 1. Remover `take_screenshot`

Anteriormente, os *plays* poderiam ter funcionalidades para capturar *screenshots* das páginas. No entanto, para o objetivo atual de análise de texto, as capturas de tela não são mais necessárias. Todas as chamadas para `take_screenshot` e o armazenamento de caminhos de *screenshot* devem ser removidos.

**Exemplo de código a ser removido:**

```python
entry_screenshot_path = self.take_screenshot(page, self.url, goto=False)

# E na criação do EntryItem:
return EntryItem(
    title=entry_title,
    ads=ad_items,
    url=self.url,
    screenshot_path=entry_screenshot_path,  # ← REMOVER
)
```

#### 2. Atualizar TODOS os Seletores

A estrutura HTML dos portais de notícias é dinâmica e pode mudar frequentemente. É **IMPERATIVO** que todos os seletores CSS/XPath utilizados nos *plays* para extrair o título, corpo, descrição e tags sejam revisados e atualizados. Isso garante que o conteúdo correto seja extraído, mesmo após alterações no layout dos sites.

*   **Seletores existentes** (para título e corpo) podem estar desatualizados e precisam de verificação.
*   **Novos seletores** são necessários para a extração da descrição (subtítulo/resumo) e das tags/categorias, que são campos novos e cruciais para a análise.

#### 3. Focar na Extração de Conteúdo e Remover Código de Anúncios

O foco principal do projeto mudou da análise de anúncios para a análise de notícias. Portanto, todo o código relacionado à detecção, extração e processamento de anúncios deve ser removido dos *plays*. O foco deve ser exclusivamente na extração de:

*   **title**: Título da notícia (revisar seletores existentes).
*   **description**: Subtítulo/resumo da notícia (NOVO campo - identificar seletores).
*   **body**: Corpo completo da notícia (revisar seletores existentes).
*   **tags**: Tags/categorias da notícia (NOVO campo - identificar seletores).

Especificamente, remover:

*   Métodos como `find_items` que eram usados para anúncios.
*   Variáveis como `n_expected_ads`.
*   Locators específicos para elementos de anúncio (ex: `.videoCube`, `#taboola-*`).

### 🆕 Criando um Novo Play

Ao adicionar um novo portal ao pipeline, um novo *play* correspondente deve ser criado. A estrutura base a seguir serve como ponto de partida, mas a implementação do método `run` e a identificação dos seletores devem ser adaptadas à estrutura HTML de cada portal para garantir a extração precisa do conteúdo.

**Estrutura base para um novo play:**

```python
import time
from playwright.sync_api import sync_playwright
from plays.base import BasePlay
from plays.items import EntryItem
from plog import logger

class NovoPortalPlay(BasePlay):
    name = "novoportal"

    @classmethod
    def match(cls, url):
        return "novoportal.com.br" in url

    def pre_run(self):
        pass

    def run(self) -> EntryItem:
        with sync_playwright() as p:
            browser = self.launch_browser(p, viewport={"width": 1920, "height": 1080})
            page = browser.new_page()
            logger.info(f"[{self.name}] Opening URL \'{self.url}\'...")
            page.goto(self.url, timeout=180_000)
            
            # Implementar a lógica de extração de título, descrição, corpo e tags aqui
            # Exemplo (adaptar para o portal):
            entry_title = page.locator("h1.title").inner_text() if page.locator("h1.title").is_visible() else ""
            entry_description = page.locator("meta[name=\"description\"]").get_attribute("content") if page.locator("meta[name=\"description\"]").is_visible() else ""
            entry_body = " ".join([p.inner_text() for p in page.locator("div.article-body p").all()])
            entry_tags = [tag.inner_text() for tag in page.locator("a.tag").all()]

            return EntryItem(
                title=entry_title,
                description=entry_description,
                body=entry_body,
                tags=entry_tags,
                url=self.url,
            )
```




## Classificação e Armazenamento

Após a coleta e extração do conteúdo das notícias pelos *spiders* e *plays*, a etapa final do pipeline de dados envolve a classificação do conteúdo e o armazenamento dos dados processados.

### Classificação com LLM

Cada notícia coletada e seu conteúdo extraído são submetidos a um modelo de linguagem grande (LLM) para classificação. Esta classificação é opcional e depende da configuração do ambiente, exigindo uma chave de API para o serviço de LLM (por exemplo, OpenAI).

O processo de classificação visa categorizar as notícias em diferentes temas ou identificar a presença de desinformação, conforme os objetivos do projeto EH-FAKE. As categorias de classificação são definidas em arquivos específicos do projeto (ex: `llm/categories.py`).

### Armazenamento de Dados

Os dados coletados e classificados são armazenados em um banco de dados, cuja estrutura é definida em `models.py`. As tabelas principais incluem:

*   **Portal**: Informações sobre os portais de notícias analisados.
*   **Entry**: Detalhes das notícias coletadas de cada portal, incluindo o conteúdo extraído.
*   **URLQueue**: Fila de URLs para processamento pelos *spiders* e *plays*.
*   **QueueStatus**: Status de cada fila de *scraping*.

Além dos dados textuais, artefatos como capturas de tela (se habilitadas, embora não mais necessárias para o objetivo atual) e outros metadados podem ser armazenados para fins de auditoria ou análise futura. O armazenamento é configurado para garantir a persistência e a acessibilidade dos dados para as etapas subsequentes de análise e apresentação.

### Execução dos Scripts

O pipeline é orquestrado através de scripts de execução que gerenciam as etapas de coleta, extração e, implicitamente, a classificação e o armazenamento. Os comandos principais para iniciar os serviços e executar as etapas são:

*   `make start`: Inicia os serviços necessários, como containers Docker para o banco de dados e ambiente de execução.
*   `make init_db`: Cria as tabelas necessárias no banco de dados.
*   `make migrate_db`: Aplica migrações pendentes no banco de dados.
*   `make crawl`: Executa os *spiders* para coletar URLs de notícias.
*   `make scrape`: Executa os *plays* para extrair o conteúdo das notícias a partir das URLs coletadas.

Esses comandos garantem que o fluxo de dados seja executado de forma sequencial e que os dados sejam devidamente processados e armazenados para uso posterior na análise de desinformação.



