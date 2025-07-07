# Pipeline de Dados do Check-up

## Contexto do Projeto

O projeto **Check-up** tem como objetivo principal analisar a presença de desinformação em anúncios publicitários de saúde que circulam em grandes sites de notícias do Brasil. Este documento detalha o fluxo completo de dados desde a coleta inicial de URLs até a apresentação final no frontend web.

## 🔄 Visão Geral do Pipeline

O pipeline de dados do Check-up é composto por **cinco etapas principais**:

1. **🕷️ Crawling (Spiders)**: Coleta URLs de artigos dos portais de notícias
2. **📰 Scraping (Plays)**: Extrai conteúdo e anúncios das páginas coletadas  
3. **🗄️ Armazenamento**: Salva dados no PostgreSQL e arquivos no MinIO
4. **🔗 API Backend**: Serve dados via FastAPI
5. **🖥️ Frontend**: Interface React para visualização


![Diagrama do Pipeline](../assets/diagrama-pipeline.png)

## 📊 Estrutura de Dados

### Modelos Principais (PostgreSQL)

**Portal**: Informações dos portais de notícias
```python
- id: Integer (Primary Key)
- name: String (Nome do portal)
- url: URL (URL base do portal)
- slug: String (Identificador único)
- created_at: DateTime
```

**URLQueue**: Fila de URLs para processamento
```python
- id: Integer (Primary Key)  
- url: URL (URL do artigo)
- created_at: DateTime
- statuses: List[QueueStatus] (Status de processamento)
```

**Entry**: Artigos de notícias processados
```python
- id: Integer (Primary Key)
- portal_id: ForeignKey (Portal)
- title: String (Título do artigo)
- url: URL (URL do artigo)
- description: String (Subtítulo/resumo)
- body: Text (Corpo completo do artigo)
- tags: JSON (Tags/categorias)
- screenshot: String (Caminho no MinIO)
- created_at: DateTime
```

**Advertisement**: Anúncios encontrados nos artigos
```python
- id: Integer (Primary Key)
- entry_id: ForeignKey (Entry)
- url: URL (URL do anúncio)
- title: String (Título do anúncio)
- tag: String (Categoria do anúncio)
- thumbnail: URL (URL da thumbnail)
- screenshot: String (Caminho no MinIO)
- excerpt: String (Descrição)
- category: Integer (Categoria LLM)
- category_verbose: String (Descrição da categoria)
- created_at: DateTime
```




## 🕷️ ETAPA 1: Spiders - Coleta de URLs

### Função dos Spiders

Os **spiders** são componentes Scrapy responsáveis por navegar nas páginas iniciais dos portais de notícias e coletar URLs de artigos válidos. Eles constituem o ponto de entrada do pipeline, identificando e filtrando links que serão posteriormente processados.

### 🔧 Como Funcionam

1. **Navegação**: Acessam a página inicial do portal (ex: `https://www.metropoles.com/`)
2. **Extração**: Identificam links de artigos usando seletores CSS/XPath
3. **Filtragem**: Aplicam regras para aceitar apenas URLs de notícias válidas
4. **Armazenamento**: Salvam URLs aprovadas na tabela `URLQueue`

### 📋 Portais Suportados

**✅ Funcionais:**
- Metrópoles (`metropolesspider`)
- IG (`igspider`) 
- MaisGoiás (`maisgoiasspider`)
- AliadosBrasil (`aliadosbrasilspider`)
- Veja (`vejaspider`)
- R7 (`r7spider`)
- UOL (`uolspider`)
- Folha (`folhaspider`)

### ⚙️ Configuração dos Spiders

**Estrutura Base:**
```python
class MetropolesSpider(BaseSpider):
    name = "metropolesspider"
    start_urls = ["https://www.metropoles.com/"]
    allowed_domains = ["metropoles.com"]
    
    def allow_url(self, entry_url):
        # Lógica de filtragem específica do portal
        return True
    
    def parse(self, response):
        # Extrai URLs usando seletores CSS
        for entry in response.css(".noticia__titulo > a"):
            url = entry.attrib.get("href")
            if url and self.allow_url(url):
                yield URLItem(url=url)
```

**Pipeline de Processamento:**
```python
# pipelines.py
class PostgresPipeline:
    def process_item(self, item, spider):
        # Verifica se URL já existe
        exists = self.session.query(URLQueue).filter(
            URLQueue.url == item["url"]
        ).first()
        
        if exists is None:
            # Cria nova entrada na fila
            URLQueue.create(self.session, item["url"])
        
        return item
```

### 🚀 Execução

**Comandos individuais:**
```bash
make crawl_metropoles  # Coleta URLs do Metrópoles
make crawl_ig          # Coleta URLs do IG
make crawl_veja        # Coleta URLs da Veja
```

**Execução em massa:**
```bash
make crawl_all_working # Todos os portais funcionais
```

**Verificação:**
```sql
-- Visualizar URLs coletadas
SELECT COUNT(*) FROM urlqueue;
SELECT url FROM urlqueue ORDER BY created_at DESC LIMIT 10;
```




## 🎭 ETAPA 2: Plays - Extração de Conteúdo e Anúncios

### Função dos Plays

Os **plays** são componentes baseados em Playwright que acessam cada URL coletada pelos spiders e extraem tanto o conteúdo editorial quanto os anúncios presentes na página. Eles constituem o coração do sistema de análise, capturando dados estruturados para posterior classificação.

### 🔧 Como Funcionam

1. **Seleção de URLs**: Buscam URLs na `URLQueue` filtradas por domínio
2. **Navegação**: Usam Playwright para abrir as páginas em um browser real
3. **Extração Dual**:
   - **Conteúdo Editorial**: título, descrição, corpo, tags
   - **Anúncios**: título, URL, thumbnail, categoria
4. **Armazenamento**: Salvam dados nas tabelas `Entry` e `Advertisement`
5. **Screenshots**: Capturam imagens das páginas no MinIO

### 📊 Estrutura dos Dados Extraídos

**EntryItem (Conteúdo Editorial):**
```python
@dataclass
class EntryItem:
    title: str           # Título principal do artigo
    url: str            # URL do artigo  
    description: str    # Subtítulo/resumo
    body: str          # Corpo completo do texto
    tags: List[str]    # Tags/categorias
    ads: List[AdItem]  # Anúncios encontrados
    screenshot_path: str # Caminho da screenshot
```

**AdItem (Anúncios):**
```python
@dataclass  
class AdItem:
    title: str          # Título do anúncio
    url: str           # URL de destino
    thumbnail_url: str # URL da imagem
    tag: str          # Categoria/origem (ex: "Taboola")
    screenshot_path: str # Screenshot do anúncio
    excerpt: str      # Descrição/texto
```

### 🎯 Exemplo de Play Funcional

**Metrópoles Play:**
```python
class MetropolesPlay(BasePlay):
    name = "metropoles"
    
    @classmethod
    def match(cls, url):
        return "metropoles.com" in url
    
    def run(self) -> EntryItem:
        with sync_playwright() as p:
            browser = self.launch_browser(p)
            page = browser.new_page()
            page.goto(self.url, timeout=180_000)
            
            # Extração do conteúdo editorial
            entry_title = page.locator("//h1").first.inner_text()
            
            description = ""
            try:
                description = page.locator(".noticiaCabecalho__subtitulo").inner_text()
            except:
                pass
            
            # Corpo do artigo
            body_paragraphs = []
            selectors = [".m-content", "article", ".article-content"]
            for selector in selectors:
                try:
                    elements = page.locator(f"{selector} p").all()
                    body_paragraphs = [p.inner_text() for p in elements]
                    if body_paragraphs:
                        break
                except:
                    continue
            
            body = " ".join(body_paragraphs)
            
            # Extração de anúncios (Taboola)
            ad_items = []
            try:
                page.locator("#taboola-below-article-thumbnails").scroll_into_view_if_needed()
                time.sleep(3)
                
                ad_elements = page.locator(".videoCube").all()
                for element in ad_elements:
                    if element.is_visible():
                        content = element.inner_html()
                        ad_item = self.find_items(content)
                        if ad_item.is_valid():
                            ad_items.append(ad_item)
            except:
                pass
            
            return EntryItem(
                title=entry_title,
                description=description, 
                body=body,
                tags=[],
                ads=ad_items,
                url=self.url
            )
```

### 🗄️ Processo de Armazenamento

**1. Seleção de URLs para Processamento:**
```python
# scrape_no_openai.py
def get_urls_for_platform(session, domain):
    """Busca URLs da URLQueue filtradas por domínio"""
    return session.query(URLQueue).join(URLQueue.statuses).filter(
        URLQueue.url.like(f"%{domain}%"),
        QueueStatus.value == "created" 
    ).limit(100).all()
```

**2. Processamento e Armazenamento:**
```python
# Para cada URL processada:
1. Play extrai conteúdo → EntryItem
2. Salva Entry no PostgreSQL
3. Para cada anúncio → Advertisement no PostgreSQL  
4. Screenshots salvos no MinIO
5. Atualiza QueueStatus para "completed"
```

### 🚀 Execução

**Comandos por portal:**
```bash
make scrape_metropoles     # Scraping do Metrópoles
make scrape_ig            # Scraping do IG  
make scrape_maisgoias     # Scraping do MaisGoiás
```

**Execução em massa:**
```bash
make scrape_all_working   # Todos os portais funcionais
```

**Comando direto (com argumentos):**
```bash
docker compose exec scraper python scrape_no_openai.py --platform metropoles.com
```

### 📈 Monitoramento

**Verificação no banco:**
```sql
-- Artigos processados
SELECT COUNT(*) FROM entry;

-- Anúncios encontrados  
SELECT COUNT(*) FROM advertisement;

-- Status por portal
SELECT 
    SUBSTRING(url FROM 'https://([^/]+)') as portal,
    COUNT(*) as total_urls
FROM urlqueue 
GROUP BY portal;
```

**Verificação no MinIO:**
- Console: `http://localhost:9001`
- Credenciais: `minioadmin/minioadmin`
- Buckets organizados por portal: `metropoles_com/`, `ig_com_br/`, etc.




## 🗄️ ETAPA 3: Armazenamento de Dados

### PostgreSQL - Banco Principal

O Check-up utiliza PostgreSQL como banco principal para armazenar dados estruturados. O schema é definido em `models.py` usando SQLAlchemy ORM.

**Fluxo de Armazenamento:**
```mermaid
graph TD
    A[Spider coleta URL] --> B[URLQueue - Fila de URLs]
    B --> C[Play processa URL]
    C --> D[Entry - Artigo principal]
    C --> E[Advertisement - Anúncios encontrados]
    D --> F[Portal - Info do portal]
    E --> F
```

**Relacionamentos:**
- `Portal` 1:N `Entry` (Um portal tem muitos artigos)
- `Entry` 1:N `Advertisement` (Um artigo tem muitos anúncios)
- `URLQueue` 1:N `QueueStatus` (Uma URL tem histórico de status)

### MinIO - Armazenamento de Arquivos

O MinIO é usado para armazenar screenshots e mídias capturadas durante o scraping.

**Estrutura de Buckets:**
```
scraped-articles/
├── metropoles_com/
│   ├── 2025/01/07/
│   │   ├── entry_screenshots/
│   │   └── ad_screenshots/
├── ig_com_br/
│   ├── 2025/01/07/
│   │   ├── entry_screenshots/
│   │   └── ad_screenshots/
```

**Processo de Upload:**
```python
# storage.py
def upload_file(local_path, minio_path):
    """Upload de arquivo para MinIO"""
    client.fput_object(
        bucket_name="scraped-articles",
        object_name=minio_path,
        file_path=local_path
    )
```

### 🔄 Status de Processamento

O sistema mantém controle detalhado do status de cada URL através da tabela `QueueStatus`:

**Estados possíveis:**
- `created`: URL coletada, aguardando processamento
- `processing`: URL sendo processada por um play
- `completed`: Processamento concluído com sucesso
- `failed`: Erro durante processamento
- `retry`: Agendada para nova tentativa

**Exemplo de consulta de status:**
```sql
SELECT 
    u.url,
    qs.value as status,
    qs.created_at
FROM urlqueue u
JOIN queuestatus qs ON u.id = qs.url_queue_id
WHERE qs.value = 'completed'
ORDER BY qs.created_at DESC;
```

## 🔗 ETAPA 4: API Backend (FastAPI)

### Estrutura da API

O backend em FastAPI (`web/server/main.py`) serve como ponte entre os dados armazenados e o frontend, fornecendo endpoints RESTful para acesso aos dados coletados.

**Endpoints Principais:**

**1. Listar Portais:**
```python
@app.get("/portais", response_model=List[str])
def listar_portais():
    """Retorna lista de portais com dados disponíveis"""
    # Busca diretamente nos buckets do MinIO
    portais = set()
    for obj in minio_client.list_objects(MINIO_BUCKET, recursive=True):
        portal = obj.object_name.split('/')[0]
        portais.add(portal)
    return sorted(list(portais))
```

**2. Buscar Notícias:**
```python
@app.get("/noticias/{portal}")
def buscar_noticias(
    portal: str,
    q: str = Query(None, description="Termo de busca"),
    page: int = Query(1, ge=1),
    limit: int = Query(10, ge=1, le=100)
):
    """Busca notícias de um portal específico com paginação"""
    # Busca arquivos JSON no MinIO do portal especificado
    # Aplica filtros de busca se fornecidos
    # Retorna dados paginados
```

**3. Detalhes da Notícia:**
```python
@app.get("/noticia/{portal}/{filename}")
def obter_noticia(portal: str, filename: str):
    """Retorna dados completos de uma notícia específica"""
    # Busca arquivo JSON específico no MinIO
    # Retorna conteúdo completo incluindo anúncios
```

### 🔄 Fluxo de Dados da API

```mermaid
graph TD
    A[Frontend Request] --> B[FastAPI Endpoint]
    B --> C[Consulta MinIO]
    C --> D[Processa Dados JSON]
    D --> E[Aplica Filtros/Paginação]
    E --> F[Retorna Response]
    F --> G[Frontend Atualiza UI]
```

### 📊 Formato de Resposta

**Estrutura de Notícia:**
```json
{
  "title": "Título da notícia",
  "url": "https://portal.com/noticia",
  "description": "Subtítulo ou resumo",
  "body": "Corpo completo da notícia...",
  "tags": ["política", "saúde"],
  "timestamp": "2025-01-07T10:30:00Z",
  "portal": "metropoles_com",
  "ads": [
    {
      "title": "Anúncio de produto",
      "url": "https://anuncio.com",
      "tag": "Taboola",
      "thumbnail": "https://thumb.com/img.jpg"
    }
  ]
}
```

## 🖥️ ETAPA 5: Frontend (React)

### Arquitetura do Frontend

O frontend em React (`web/client/src/`) oferece interface intuitiva para explorar os dados coletados, com foco na visualização de notícias e anúncios de saúde.

**Componentes Principais:**

**1. Home.tsx - Página Principal:**
```tsx
const Home = () => {
  const [portais, setPortais] = useState<string[]>([]);
  const [noticias, setNoticias] = useState<any[]>([]);
  const [portalSelecionado, setPortalSelecionado] = useState<string | null>(null);
  
  // Busca lista de portais disponíveis
  useEffect(() => {
    fetch(`${API_URL}/portais`)
      .then(res => res.json())
      .then(setPortais);
  }, []);
  
  // Busca notícias quando portal é selecionado
  useEffect(() => {
    if (portalSelecionado) {
      fetch(`${API_URL}/noticias/${portalSelecionado}`)
        .then(res => res.json())
        .then(setNoticias);
    }
  }, [portalSelecionado]);
}
```

**2. Funcionalidades Implementadas:**

- **Seleção de Portal**: Tabs para escolher entre portais disponíveis
- **Busca**: Campo de texto para filtrar notícias por título/conteúdo
- **Paginação**: Navegação através de grandes volumes de dados
- **Visualização Detalhada**: Modal com conteúdo completo da notícia
- **Estatísticas**: Contadores de notícias por portal

### 🎨 Interface do Usuário

**Layout Principal:**
```
┌─────────────────────────────────────────┐
│ Check-up - Análise de Anúncios de Saúde │
├─────────────────────────────────────────┤
│ [Metrópoles] [IG] [Veja] [R7] [UOL]...  │ <- Tabs dos portais
├─────────────────────────────────────────┤
│ 🔍 [___________________] [Buscar]       │ <- Campo de busca
├─────────────────────────────────────────┤
│ ┌─────────────────────────────────────┐ │
│ │ 📰 Título da Notícia               │ │
│ │ 🏷️  Portal: Metrópoles             │ │ <- Cards de notícias
│ │ 📅 07/01/2025 - 🔗 2 anúncios      │ │
│ │ [Ver Detalhes]                     │ │
│ └─────────────────────────────────────┘ │
├─────────────────────────────────────────┤
│ ← 1 2 3 ... 10 →                       │ <- Paginação
└─────────────────────────────────────────┘
```

### 🔄 Fluxo de Interação

1. **Carregamento Inicial**: Busca lista de portais disponíveis
2. **Seleção de Portal**: Usuário clica em tab do portal desejado
3. **Exibição de Notícias**: Lista paginada de notícias do portal
4. **Busca**: Usuário pode filtrar por termos específicos
5. **Detalhamento**: Modal mostra conteúdo completo + anúncios
6. **Navegação**: Paginação para explorar mais resultados

### 📱 Recursos de UX

- **Loading States**: Skeletons durante carregamento
- **Error Handling**: Mensagens de erro amigáveis  
- **Responsive Design**: Adaptável a diferentes tamanhos de tela
- **Busca Reativa**: Resultados atualizados em tempo real
- **Indicadores Visuais**: Badges para número de anúncios

## 🔄 FLUXO COMPLETO DE DADOS

### Pipeline End-to-End

```mermaid
graph TD
    A[Portal de Notícias] --> B[Spider - Scrapy]
    B --> C[URLQueue - PostgreSQL]
    C --> D[Play - Playwright]
    D --> E[Entry - PostgreSQL]
    D --> F[Advertisement - PostgreSQL]
    D --> G[Screenshots - MinIO]
    E --> H[FastAPI Backend]
    F --> H
    G --> H
    H --> I[React Frontend]
    I --> J[Interface do Usuário]
    
    style A fill:#e1f5fe
    style J fill:#e8f5e8
    style H fill:#fff3e0
```

### ⏱️ Cronograma de Execução

**1. Configuração (única vez):**
```bash
make setup  # ~5-10 minutos
```

**2. Coleta de URLs (diária):**
```bash
make crawl_all_working  # ~10-15 minutos
# Resultado: ~500-1000 URLs novas por portal
```

**3. Extração de Conteúdo (contínua):**
```bash
make scrape_all_working  # ~2-4 horas
# Resultado: ~100-200 artigos processados por hora
```

**4. Visualização (imediata):**
```bash
# Frontend disponível em: http://localhost:5173
# API disponível em: http://localhost:8000
```

## 🚀 Comandos de Execução

### Setup Inicial
```bash
# Configuração completa do ambiente
make setup

# Verificar se serviços estão rodando
docker compose ps

# Acessar logs em tempo real
docker compose logs -f
```

### Execução do Pipeline

**1. Coleta de URLs (Crawling):**
```bash
# Todos os portais funcionais
make crawl_all_working

# Portais individuais
make crawl_metropoles
make crawl_ig
make crawl_veja
make crawl_uol
```

**2. Extração de Conteúdo (Scraping):**
```bash
# Todos os portais funcionais  
make scrape_all_working

# Portais individuais
make scrape_metropoles
make scrape_ig
make scrape_maisgoias
```

**3. Pipeline Completo:**
```bash
# Executa crawl + scraping sequencialmente
make pipeline_complete

# Workflow otimizado
make collect_working
```

### Monitoramento e Debugging

**Acesso ao container:**
```bash
make bash
```

**Verificações no banco:**
```bash
# Dentro do container
python -c "
from models import *
from sqlalchemy import create_engine
from sqlalchemy.orm import Session
from decouple import config

engine = create_engine(config('DATABASE_URL'))
session = Session(engine)

print('URLs na fila:', URLQueue.select().count())
print('Artigos processados:', Entry.select().count()) 
print('Anúncios encontrados:', Advertisement.select().count())
"
```

**Verificação do MinIO:**
- Interface: `http://localhost:9001`
- Credenciais: `minioadmin/minioadmin`

**Frontend e API:**
- Frontend: `http://localhost:5173`
- API: `http://localhost:8000`
- Docs da API: `http://localhost:8000/docs`

### Troubleshooting

**Problema: Playwright browsers não instalados**
```bash
docker compose down
docker compose build --no-cache
make start
```

**Problema: Banco não conecta**
```bash
make wait-for-db
# ou
make stop && make start
```

**Problema: MinIO não acessível**
```bash
docker compose ps
# Verificar se container healthcheck_minio está "healthy"
```

## 🔧 Configuração e Customização

### Variáveis de Ambiente (.env)

```bash
# Banco de dados
DATABASE_URL=postgresql://postgres:supersecret@healthcheck_db:5432/healthcheck

# MinIO
MINIO_ENDPOINT=minio:9000
MINIO_ACCESS_KEY=minioadmin
MINIO_SECRET_KEY=minioadmin
MINIO_SECURE=False

# Opcional: OpenAI para classificação
OPENAI_API_KEY=sk-...

# Credenciais de portais (se necessário)
FOLHA_USERNAME=usuario
FOLHA_PASSWORD=senha
```

### Adicionando Novo Portal

**1. Criar Spider:**
```python
# spiders/novoportal.py
class NovoPortalSpider(BaseSpider):
    name = "novoportalspider"
    start_urls = ["https://novoportal.com/"]
    # ... implementação específica
```

**2. Criar Play:**
```python  
# plays/novoportal.py
class NovoPortalPlay(BasePlay):
    name = "novoportal"
    
    @classmethod
    def match(cls, url):
        return "novoportal.com" in url
    # ... implementação específica
```

**3. Adicionar comandos no Makefile:**
```makefile
crawl_novoportal:
	docker compose run scraper python crawl.py novoportalspider

scrape_novoportal:
	docker compose exec scraper python scrape_no_openai.py --platform novoportal.com
```

**4. Testar:**
```bash
make crawl_novoportal
make scrape_novoportal
```

## 📈 Métricas e Análises

### Consultas Úteis

**Resumo geral:**
```sql
SELECT 
    COUNT(DISTINCT p.name) as total_portais,
    COUNT(DISTINCT e.id) as total_artigos,
    COUNT(DISTINCT a.id) as total_anuncios,
    AVG(ads_por_artigo.count) as media_anuncios_por_artigo
FROM portal p
LEFT JOIN entry e ON p.id = e.portal_id
LEFT JOIN advertisement a ON e.id = a.entry_id
LEFT JOIN (
    SELECT entry_id, COUNT(*) as count 
    FROM advertisement 
    GROUP BY entry_id
) ads_por_artigo ON e.id = ads_por_artigo.entry_id;
```

**Top portais por volume:**
```sql
SELECT 
    p.name,
    COUNT(e.id) as artigos,
    COUNT(a.id) as anuncios
FROM portal p
LEFT JOIN entry e ON p.id = e.portal_id  
LEFT JOIN advertisement a ON e.id = a.entry_id
GROUP BY p.name
ORDER BY artigos DESC;
```

**Evolução temporal:**
```sql
SELECT 
    DATE(e.created_at) as data,
    COUNT(e.id) as artigos_processados,
    COUNT(a.id) as anuncios_encontrados
FROM entry e
LEFT JOIN advertisement a ON e.id = a.entry_id
WHERE e.created_at >= CURRENT_DATE - INTERVAL '7 days'
GROUP BY DATE(e.created_at)
ORDER BY data DESC;
```

## 🎯 Próximos Passos

### Melhorias Planejadas

1. **Classificação Automática**: Integração com LLM para categorização de anúncios
2. **Dashboard Analytics**: Painel com métricas em tempo real
3. **Alertas**: Notificações para anúncios suspeitos de desinformação  
4. **API Pública**: Endpoints para acesso externo aos dados
5. **Exportação**: Funcionalidades para download de relatórios

### Escalabilidade

1. **Processamento Paralelo**: Multiple workers para scraping
2. **Cache Redis**: Cache de consultas frequentes
3. **CDN**: Distribuição de screenshots via CDN
4. **Monitoring**: Prometheus + Grafana para métricas
5. **Auto-scaling**: Ajuste automático de recursos baseado na carga



