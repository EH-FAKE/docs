# Backlog do Projeto – É Fake?

Este documento organiza os épicos e funcionalidades do projeto em formato de backlog, com base no objetivo de construir uma solução para detecção de notícias desinformativas utilizando IA.

## Objetivo Geral

Desenvolver uma tecnologia de Machine Learning para identificar e classificar automaticamente notícias como desinformativas, informativas ou neutras, de acordo com critérios definidos pelo usuário.

## Épicos e Features

### EP001 - Inteligência Artificial para Detecção de Desinformação

- **FT001.1** - Integrar RAGFlow com modelo LLM (Sabiá / LlamaIndex)
-  **FT001.2** - Criar pipeline para análise de notícias com classificação (desinformativa, informativa, neutra)
-  **FT001.3** - Implementar filtro por tema e fonte
-  **FT001.4** - Validar classificações com dataset rotulado

### EP002 - Interface do Usuário (Frontend)
-  **FT002.1** - Criar interface em Streamlit para busca de notícias
-  **FT002.2** - Permitir input de parâmetros (tema, fonte, tipo)
-  **FT002.3** - Exibir resultados com classificação e explicação

### EP003 - Orquestração de Serviços (Backend/API)
- **FT003.1** - Criar backend com FastAPI
- **FT003.2** - Expor endpoint para análise de notícia
- **FT003.3** - Validar e formatar resposta da IA para o frontend

### EP004 - Dados e Coleta de Notícias
- **FT004.1** - Integrar coleta contínua de notícias
- **FT004.2** - Ler e processar dados da planilha de temas mapeados
- **FT004.3** - Normalizar formato das notícias para uso pela IA

### EP005 - Documentação e Governança
- **FT005.1** - Documentar arquitetura e fluxos (linha-base)
- **FT005.2** - Definir papéis de governança (Mantenedor, CM, etc.)
- **FT005.3** - Manter changelogs e atas de decisão
- **FT005.4** - Publicar documentação no GitHub Pages


## Histórias de Usuário

### Como *usuário comum*, quero:
    - ⁠Buscar notícias sobre temas sensíveis para saber se há desinformação.
    - ⁠Filtrar resultados por fonte jornalística confiável.
    - ⁠Entender por que uma notícia foi considerada desinformativa.

### Como *pesquisador*, quero:
    - ⁠Acessar um banco de dados com classificações históricas.
    - ⁠Contribuir com novos exemplos de notícias para treinar o modelo.
    - ⁠Acompanhar atualizações do sistema e roadmap de funcionalidades.

### Como *desenvolvedor*, quero:
    - ⁠Ter um ambiente de desenvolvimento configurável com Docker.
    - ⁠Ver exemplos claros de contribuição com templates no GitHub.
    - ⁠Entender como o modelo de IA está integrado à arquitetura.

## Roadmap e Releases

### Release I – Até 28/04/2025
*Temas envolvidos: Git, Configuração de Ambiente, Linha-base*

- ⁠FT004.1 – Coleta contínua de notícias
- ⁠FT005.1 – Documentação da arquitetura
- ⁠FT005.4 – GitHub Pages com documentação inicial
- ⁠FT001.1 – Integração RAGFlow + Sabiá
- ⁠Docker, Compose e README funcional

### Release II – Até 02/06/2025
*Temas envolvidos: DevOps, Integração Contínua, Kubernetes, Build*

- ⁠FT001.2 – Pipeline de classificação
- ⁠FT002.1 – Interface básica em Streamlit
 - ⁠FT003.1 – Backend com FastAPI
- ⁠FT001.3 – Filtro por tema/fonte

### Release III – Até 25/06/2025
*Temas envolvidos: Sustentação, Dívidas Técnicas, Pós-mortem*

- ⁠FT001.4 – Validação com dataset rotulado
- ⁠FT002.3 – Explicações da IA
- ⁠FT003.2 e FT003.3 – Endpoint e integração com frontend
- ⁠FT005.3 – Pós-mortem, changelogs e governança

## Timeline 

| Semana | Datas      | Aulas-chave                                  | Entregas previstas                            |
|--------|------------|-----------------------------------------------|-----------------------------------------------|
| 1      | 24-27/03   | Introdução / Git Avançado                     | Definição do projeto e backlog inicial        |
| 2      | 31/03-03/04| Equipes / Repositório                         | Setup do ambiente e estrutura Git             |
| 3      | 07-10/04   | Configuração do Ambiente                      | FT004.1 / FT005.1                             |
| 4      | 14-17/04   | Backlog / Automação de Build                  | FT001.1 / FT005.4                             |
| 5      | 21-24/04   | Preparação para entrega                       | Finalização do ambiente                       |
| ✅     | *28/04*  | *Release I*                                 | Checklist software livre                      |
| 6-7    | 30/04-09/05| DevOps / Projeto Individual                   | FT001.2 / FT003.1                             |
| 8      | 12-16/05   | Isolamento / Kubernetes                       | FT002.1 / FT001.3                             |
| 9      | 19-23/05   | Licenças / Ajustes finais                     | FT003.2 / FT002.2                             |
| ✅     | *02/06*  | *Release II*                                | Pipeline funcional e integração frontend/api  |
| 10     | 04-09/06   | Trabalho Individual                           | Entrega individual                            |
| 11     | 11-18/06   | Clean Code / Dívidas Técnicas                 | FT001.4 / FT003.3 / FT002.3                   |
| ✅     | *25/06*  | *Release III*                               | Pós-mortem, documentação e fechamento         |



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