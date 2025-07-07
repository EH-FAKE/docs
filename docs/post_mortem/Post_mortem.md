# Post-Mortem do Projeto EH-FAKE

## 1. Visão Geral do Projeto

O projeto EH-FAKE é uma iniciativa desenvolvida por estudantes da Universidade de Brasília (UnB) com o objetivo de combater a desinformação, especificamente em anúncios publicitários de saúde que circulam em grandes sites de notícias brasileiros. A solução proposta visa identificar e classificar notícias desinformativas utilizando tecnologias de Machine Learning e Processamento de Linguagem Natural (PLN).

## 2. Estrutura do Repositório GitHub

O repositório EH-FAKE no GitHub é composto por três sub-repositórios principais, cada um com uma função específica no ecossistema do projeto:

### 2.1. `check-up`

Este é o coração do projeto, contendo a lógica principal para a análise de desinformação. Suas funcionalidades incluem:

*   **Coleta de URLs**: Utiliza a biblioteca Scrapy para rastrear e coletar URLs de notícias das páginas iniciais de dez portais de notícias brasileiros (Estadão, Folha, Globo, IG, Metrópoles, R7, RBS, Terra, Veja, UOL).
*   **Raspagem de Anúncios**: Emprega a biblioteca Playwright para simular a navegação em um navegador e capturar anúncios nativos presentes nas páginas de notícias. Também é responsável por arquivar esses anúncios e registrar capturas de tela.
*   **Classificação de Anúncios**: Utiliza um Grande Modelo de Linguagem (LLM) para classificar os anúncios coletados em 45 categorias pré-definidas, com a opção de integração com a API da OpenAI.

### 2.2. `docs`

Este repositório é dedicado à documentação abrangente do projeto EH-FAKE. Ele fornece informações cruciais sobre:

*   **Contexto do Projeto**: Detalha o problema da desinformação em anúncios de saúde e a motivação por trás do EH-FAKE.
*   **Estrutura da Solução**: Explica como a solução é arquitetada, incluindo os conceitos de Query, Pergunta, Explicação para IA e Classificação (Desinformativo, Informativo, Não se encaixa).
*   **Tecnologias Utilizadas**: Lista e descreve as principais ferramentas e bibliotecas, como RAGFlow (para orquestração de fluxos RAG) e NGROK (para exposição de servidores locais).
*   **Requisitos Funcionais**: Apresenta os requisitos do sistema, como coleta contínua de notícias e pesquisa personalizada por tema (Estabilidade democrática, Saúde, Meio ambiente, Mulheres, Povos e comunidades tradicionais, População negra, População LGBTQIA+) e fonte da notícia.
*   **Instalação e Uso**: Fornece instruções detalhadas para configurar e executar o projeto localmente, incluindo a criação de ambiente virtual, instalação de dependências e execução dos scripts.
*   **Contribuição**: Orientações para novos contribuidores.



## 3. Análise Técnica e Estrutura do Código

### 3.1. `check-up`

O repositório `check-up` é o componente central do projeto, responsável pela coleta, raspagem e classificação de anúncios. A estrutura do código reflete essa modularidade:

*   **`models.py`**: Define os modelos de dados para o banco de dados, incluindo `Portal`, `Entry`, `Advertisement`, `URLQueue` e `QueueStatus`. Isso indica uma abordagem robusta para o armazenamento e gerenciamento dos dados coletados.
*   **`spiders/`**: Contém os *spiders* (rastreadores) implementados com a biblioteca Scrapy. Cada arquivo dentro deste diretório (`estadao.py`, `folha.py`, `globo.py`, etc.) é responsável por coletar URLs de notícias de um portal específico. A existência de um `base.py` sugere uma estrutura de herança para reutilização de código entre os *spiders*.
*   **`plays/`**: Abriga os scripts que utilizam a biblioteca Playwright para raspar os anúncios das páginas de notícias. Assim como os *spiders*, cada arquivo (`estadao.py`, `folha.py`, etc.) corresponde a um portal específico, indicando a necessidade de adaptação do código para a estrutura HTML de cada site. O `base.py` e `utils.py` provavelmente fornecem funcionalidades comuns.
*   **`llm/`**: Contém a lógica para a classificação de anúncios usando um Grande Modelo de Linguagem (LLM). A menção a `categories.py` e `OPENAI_API_KEY` no README sugere que a classificação é baseada em categorias predefinidas e pode ser integrada com a API da OpenAI.
*   **`docker/`**: Inclui arquivos relacionados à conteinerização do ambiente, como `compose.yml` e `Dockerfile`, facilitando a configuração e execução do projeto em diferentes ambientes.
*   **`Makefile`**: Define comandos para automatizar tarefas como iniciar serviços (`make start`), inicializar o banco de dados (`make init_db`), executar migrações (`make migrate_db`), coletar URLs (`make crawl`) e raspar anúncios (`make scrape`). Isso simplifica o fluxo de trabalho para desenvolvedores e usuários.

### 3.2. `docs`

O repositório `docs` é uma documentação bem estruturada do projeto, utilizando o MkDocs. A organização dos arquivos indica uma preocupação com a clareza e facilidade de uso:

*   **`docs/`**: Contém os arquivos Markdown que compõem a documentação. A presença de subdiretórios e arquivos como `index.md`, `contributing.md`, `tutorial_spiders_plays.md` demonstra uma organização lógica do conteúdo.
*   **`mkdocs.yml`**: Arquivo de configuração do MkDocs, que define a estrutura da navegação, temas e plugins utilizados na documentação.
*   **`requirements.txt`**: Lista as dependências Python necessárias para construir e servir a documentação.



## 4. Objetivos e Resultados do Projeto

### 4.1. Objetivos Iniciais

O principal objetivo do projeto EH-FAKE, conforme descrito na documentação, é desenvolver uma tecnologia baseada em Machine Learning para identificar e classificar notícias desinformativas, com foco inicial em anúncios publicitários de saúde. Os objetivos específicos incluem:

*   **Coleta Contínua de Notícias**: Estabelecer um sistema para coletar regularmente notícias de jornais e revistas parceiros.
*   **Pesquisa Personalizada**: Permitir a pesquisa e classificação de notícias com base em temas específicos (estabilidade democrática, saúde, meio ambiente, mulheres, povos e comunidades tradicionais, população negra, população LGBTQIA+) e fontes.
*   **Identificação de Desinformação**: Desenvolver um mecanismo para verificar a presença de desinformação em anúncios.
*   **Classificação de Anúncios**: Categorizar anúncios em 'Desinformativo', 'Informativo' ou 'Não se encaixa' usando LLMs.

### 4.2. Resultados Alcançados

Com base na análise dos repositórios e da documentação, o projeto EH-FAKE demonstrou os seguintes resultados e progressos:

*   **Infraestrutura de Coleta Funcional**: O repositório `check-up` implementa com sucesso *spiders* (Scrapy) para coleta de URLs e *plays* (Playwright) para raspagem de anúncios de dez grandes portais de notícias brasileiros. A modularidade do código permite a fácil adição de novos portais.
*   **Sistema de Classificação Baseado em LLM**: A arquitetura prevê a utilização de um LLM para classificar anúncios, o que representa uma abordagem moderna e escalável para a detecção de desinformação. A integração com a API da OpenAI sugere flexibilidade na escolha do modelo.
*   **Documentação Abrangente**: O repositório `docs` é um ponto forte do projeto, fornecendo uma documentação clara e detalhada sobre o contexto, estrutura, tecnologias, requisitos e instruções de uso. Isso é crucial para a colaboração e a adoção do projeto.
*   **Modularidade e Reusabilidade**: A separação das funcionalidades em diferentes repositórios (`check-up`, `docs`, `airflow-rag`) e a estrutura interna de cada um (e.g., `base.py` para *spiders* e *plays*) indicam um design que favorece a modularidade e a reusabilidade do código.
*   **Automação de Workflow**: A presença do `Makefile` no `check-up` e `airflow-rag` simplifica a execução de tarefas complexas, como inicialização de banco de dados, migrações e processos de coleta e raspagem, o que é um indicativo de maturidade no desenvolvimento.
*   **Conteinerização**: O uso de Docker e Docker Compose facilita a configuração do ambiente de desenvolvimento e produção, garantindo consistência e reprodutibilidade.

### 4.3. Lições Aprendidas e Desafios

Embora o projeto demonstre um progresso significativo, algumas lições e desafios podem ser inferidos:

*   **Dependência de Estruturas de Sites**: A necessidade de criar *spiders* e *plays* específicos para cada portal de notícias indica que o sistema é sensível a mudanças na estrutura HTML dos sites. Manter esses componentes atualizados pode ser um desafio contínuo.
*   **Complexidade da Classificação de Desinformação**: A classificação de desinformação é inerentemente complexa e subjetiva. A eficácia do LLM dependerá da qualidade dos prompts, dos dados de treinamento e da capacidade de adaptação a novas formas de desinformação.
*   **Escalabilidade da Coleta**: A coleta contínua de notícias de múltiplos portais e a raspagem de anúncios podem demandar recursos computacionais significativos, especialmente à medida que o volume de dados aumenta.
*   **Manutenção de Modelos de LLM**: A performance do sistema de classificação está diretamente ligada à manutenção e atualização do modelo de linguagem, o que pode exigir expertise em Machine Learning e recursos computacionais.
*   **Colaboração e Comunicação**: Em projetos de código aberto com múltiplos contribuidores, a comunicação eficaz e a coordenação são essenciais para garantir a integração suave das diferentes partes do sistema.
*   **Documentação Contínua**: Embora a documentação seja um ponto forte, a manutenção e atualização contínua, especialmente em um projeto em desenvolvimento ativo, é crucial para garantir que ela reflita o estado atual do código.

## 5. Recomendações e Próximos Passos

Para o futuro do projeto EH-FAKE, as seguintes recomendações podem ser consideradas:

*   **Monitoramento e Manutenção de Spiders/Plays**: Implementar um sistema de monitoramento para detectar quebras nos *spiders* e *plays* devido a alterações nos layouts dos sites. Considerar abordagens mais resilientes à mudança de layout, se possível.
*   **Aprimoramento do LLM**: Continuar refinando o modelo de linguagem e os prompts para melhorar a precisão da classificação de desinformação. Explorar técnicas de *fine-tuning* ou modelos específicos para o domínio de saúde.
*   **Otimização de Recursos**: Investigar otimizações para a coleta e raspagem de dados, como o uso de proxies, rotação de IPs ou processamento distribuído, para lidar com a escalabilidade.
*   **Feedback e Validação Humana**: Integrar um ciclo de feedback humano para validar as classificações do LLM e usar esses dados para retreinar e aprimorar o modelo.
*   **Expansão para Outras Mídias**: Considerar a expansão da análise para outras formas de mídia, como vídeos ou áudios, se relevante para o escopo do projeto.
*   **Engajamento da Comunidade**: Continuar incentivando a contribuição da comunidade, talvez através de *hackathons* ou desafios, para acelerar o desenvolvimento e aprimoramento do projeto.
*   **Publicação de Resultados**: Compartilhar os resultados e as descobertas do projeto com a comunidade acadêmica e o público em geral para aumentar a conscientização sobre a desinformação em anúncios de saúde.

## 6. Conclusão

O projeto EH-FAKE é uma iniciativa promissora e bem estruturada para combater a desinformação em anúncios de saúde. Com uma base técnica sólida, uma documentação abrangente e uma abordagem modular, ele tem o potencial de causar um impacto significativo. Os desafios identificados são inerentes a projetos dessa natureza, mas com um planejamento contínuo e a colaboração da comunidade, o EH-FAKE pode evoluir para uma ferramenta ainda mais poderosa e eficaz.

