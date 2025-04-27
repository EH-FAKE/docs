# Arquitetura de Solução

## Visão Geral

Este projeto é baseado em uma arquitetura de *microserviços* com **, onde cada componente possui responsabilidade isolada e é mantido em um repositório independente. Os serviços se comunicam via APIs HTTP (REST), de forma síncrona.

A solução tem como foco o uso de uma *IA generalista* integrada com um sistema modular.


## Componentes

| Serviço        | Descrição                                                                 | Repositório   |
|----------------|---------------------------------------------------------------------------|---------------|
| *Frontend*   | Interface web desenvolvida com Streamlit, onde o usuário interage com a IA | ⁠ frontend ⁠    |
| *Backend*    | API REST em FastAPI que recebe requisições do front e orquestra o uso da IA | ⁠ backend ⁠     |
| *FakeBuster* | Serviço de IA com RAGflow, responsável por gerar respostas inteligentes     | ⁠ fakebuster ⁠  |
| *Datasets*   | Dados utilizados pela IA, organizados e versionados                         | ⁠ datasets ⁠    |
| *Docs*       | Repositório da documentação do projeto (README, tutoriais, landing page)    | ⁠ docs ⁠        |

## Comunicação entre serviços

•⁠  ⁠O usuário acessa o *Frontend* (Streamlit)
•⁠  ⁠O frontend envia uma requisição para o *Backend* (FastAPI)
•⁠  ⁠O backend realiza a orquestração e aciona o serviço de IA (*FakeBuster*)
•⁠  ⁠A IA, quando necessário, consulta o repositório de *datasets*
•⁠  ⁠A resposta final é retornada ao usuário via frontend

## Tecnologias Utilizadas

•⁠  ⁠*Frontend:* Streamlit (Python)
•⁠  ⁠*Backend:* FastAPI, Pydantic (Python), Airflow (??)
•⁠  ⁠*IA (FakeBuster):* RAGFlow, Maritaca AI, LlamaIndex, NeMo Guardrails
•⁠  ⁠*Armazenamento de dados:* CSV, JSON, Google Sheets
•⁠  ⁠*Containerização:* Docker e docker-compose
•⁠  ⁠*Controle de Versão:* Git + GitHub
•⁠  ⁠*Documentação:* Markdown + GitHub Pages, mermaid


## Diagrama da Arquitetura

<p align="center">
  <img src="https://www.mermaidchart.com/raw/b2593cc2-557a-4770-8975-1aa98a2303d8?theme=light&version=v0.1&format=svg" alt="Diagrama da Arquitetura" width="300">
</p>

## Histórico do documento

| Versão | Data       | Descrição            | Autor         |
| ------ | ---------- | -------------------- | ------------- |
| 1.0    | 21/04/2025 | Criação do documento | Amanda Campos |