# Backlog do Projeto – eh-fake

Desenvolver uma solução baseada em IA (Machine Learning + RAG) para identificar e classificar automaticamente notícias como desinformativas, informativas ou neutras, com foco em apoio à checagem e combate à desinformação.

## Objetivo Geral

Refatorar e escalar a ferramenta Check-up, que realiza coleta de notícias de portais jornalísticos, visando aprimorar o scraping, aumentar a cobertura de fontes e implementar orquestração e automação dos processos com Apache Airflow. O objetivo final é fornecer uma base robusta de dados para análise de desinformação.

## Histórias de Usuário

### Como *pesquisador de desinformação*, quero:
- Consultar um banco de dados com notícias atualizadas de diversos portais
- Ter acesso às classificações de notícias (informativa, desinformativa, fora de escopo)
- Confiar na consistência dos dados, com poucos erros de scraping

### Como *desenvolvedor*, quero:
- Refatorar os scrapers para que sejam mais robustos e fáceis de manter
- Orquestrar os scrapers no Airflow, com logs claros e retries automáticos
- Ter documentação clara sobre como adicionar novos scrapers ou pipelines

### Como *analista de dados*, quero:
- Receber dados limpos, organizados e bem estruturados para análises posteriores
- Verificar rapidamente se algum scraper falhou ou parou de funcionar


## Épicos e Funcionalidades

### **EP001 – Refatoração dos Scrapers**
- **FT001.1** – Revisar e otimizar scrapers atuais (10 portais)
- **FT001.2** – Corrigir bugs e tratar casos de exceção nos scrapers
- **FT001.3** – Padronizar a saída dos scrapers (schema unificado)

### **EP002 – Expansão da Coleta de Dados**
- **FT002.1** – Adicionar scraping de novos portais e fontes de notícia (meta: +10)
- **FT002.2** – Criar mecanismo para fácil adição de novos scrapers no futuro
- **FT002.3** – Implementar verificação automática de falhas nos scrapers

### **EP003 – Orquestração e Automação**
- **FT003.1** – Migrar scrapers para DAGs no Apache Airflow
- **FT003.2** – Implementar monitoramento, retries e logs estruturados
- **FT003.3** – Criar pipeline de pré-processamento e armazenamento dos dados

### **EP004 – Classificação de Notícias**
- **FT004.1** – Implementar classificação das notícias em: 
    - Informativa
    - Desinformativa
    - Fora de escopo
- **FT004.2** – Criar pipeline de classificação integrado ao Airflow
- **FT004.3** – Validar resultados manualmente e ajustar regras ou modelo

### **EP005 – Documentação e Sustentação**
- **FT005.1** – Documentar arquitetura, scrapers, pipelines e processos
- **FT005.2** – Criar guia de contribuição e manutenção dos scrapers
- **FT005.3** – Publicar documentação no GitHub Pages



## Mapa de Dependências

| Feature | Depende de | Motivo da dependência |
|---------|------------|------------------------|
| FT003.1 – Migrar para Airflow | FT001.3 – Padronizar saída | Dados precisam estar padronizados para criar DAGs robustas |
| FT002.3 – Verificação automática de falhas | FT003.2 – Monitoramento no Airflow | Monitoramento é necessário para notificar falhas |
| FT004.2 – Pipeline de classificação | FT003.3 – Pipeline de armazenamento | Classificação ocorre sobre dados coletados e armazenados |
| FT004.3 – Validação manual | FT004.1 – Classificação | Só valida após a classificação estar operando |
| FT005.3 – Publicação da documentação | FT005.1 e FT005.2 | Documentação precisa estar concluída antes da publicação |


## Roadmap e Releases

| Release | Data       | Entregas principais                                   | Épico                                     |
|---------|------------|------------------------------------------------------|-------------------------------------------|
| **R1**  | 13/05/2025 | ✔️ Kick-off e primeira revisão dos scrapers atuais   | [EP001 – Refatoração dos Scrapers](#ep001--refatoração-dos-scrapers)   |
| **R2**  | 21/05/2025 | ✔️ Refatoração completa dos 10 scrapers + padronização | [EP001 – Refatoração dos Scrapers](#ep001--refatoração-dos-scrapers)   |
| **R3**  | 28/05/2025 | ✔️ Adição de novos scrapers (meta +5)                 | [EP002 – Expansão da Coleta de Dados](#ep002--expansão-da-coleta-de-dados) |
| **R4**  | 04/06/2025 | ✔️ Adição de mais scrapers (meta +5) + modularização  | [EP002 – Expansão da Coleta de Dados](#ep002--expansão-da-coleta-de-dados) |
| **R5**  | 11/06/2025 | ✔️ Início da migração para Airflow + primeiros DAGs   | [EP003 – Orquestração e Automação](#ep003--orquestração-e-automação)   |
| **R6**  | 18/06/2025 | ✔️ Pipeline completo no Airflow + monitoramento/logs | [EP003 – Orquestração e Automação](#ep003--orquestração-e-automação)   |
| **R7**  | 25/06/2025 | ✔️ Classificação de notícias + documentação finalizada | [EP004 – Classificação de Notícias](#ep004--classificação-de-notícias), [EP005 – Documentação e Sustentação](#ep005--documentação-e-sustentação) |

## Timeline 

| Semana | Período      | Entregas principais                                          |
|--------|---------------|--------------------------------------------------------------|
| 1      | 13-17/05      | R1 – Início + revisão geral dos scrapers existentes         |
| 2      | 18-24/05      | R2 – Refatoração dos scrapers + padronização dos dados      |
| 3      | 25-31/05      | R3 – Adicionar 5 novos scrapers                             |
| 4      | 01-07/06      | R4 – Adicionar +5 scrapers + estrutura modular para scrapers|
| 5      | 08-14/06      | R5 – Migrar scrapers para DAGs no Airflow                   |
| 6      | 15-21/06      | R6 – Implementar monitoramento, retries e pipeline completo |
| 7      | 22-25/06      | R7 – Implementar classificação + validação + documentação   |


## Links Úteis

•⁠  ⁠[RAGFlow](https://github.com/infiniflow/ragflow)  
•⁠  ⁠[Maritaca AI – Sabiá](https://github.com/maritaca-ai/maritalk-api)  
•⁠  ⁠[LlamaIndex](https://github.com/run-llama/llama_index)  
•⁠  ⁠[NeMo Guardrails](https://github.com/NVIDIA/NeMo-Guardrails)  
•⁠  ⁠[Temas mapeados – Google Sheets](https://docs.google.com/spreadsheets/d/1D1bGN2gfV-pn_SQrjKhcR_9n3W6xSWZM/edit?usp=sharing)

## Histórico do documento

| Versão | Data       | Descrição            | Autor         |
| ------ | ---------- | -------------------- | ------------- |
| 1.0    | 21/04/2025 | Criação do documento | Amanda Campos |
| 1.1    | 27/04/2025 | Atualização do Roadmap | Luis Miranda  |
| 1.2    | 05/05/2025 | atualização das histórias de usuários e roadmap | Amanda Campos |