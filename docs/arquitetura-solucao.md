# Arquitetura de Solução

Tipo de Arquitetura:  
➔ Aplicação Serverless com Gerenciamento via Docker, CI/CD Pipeline e Controle de Configuração + Arquitetura de Recuperação-Aumentada (RAG)

Resumo:  
O projeto é desenvolvido como uma aplicação serverless, hospedada na plataforma RAGFlow, aproveitando sua infraestrutura de orquestração, armazenamento vetorial e integração com modelos de linguagem (LLMs). A solução adota a abordagem Retrieval-Augmented Generation (RAG), utilizando a recuperação de dados relevantes de bases vetoriais para a geração de respostas contextualmente precisas. A arquitetura foi projetada para suportar escalabilidade e flexibilidade, com foco na automação de processos de integração e deployment, além da gestão eficiente de versões e evoluções do sistema.


## Componentes principais


| Componente | Descrição |
| --- | --- |
| Plataforma RAGFlow | Ambiente que centraliza toda a execução, interface e dados, fornecendo as ferramentas de orquestração e modelo de IA |
| FakeBuster | Aplicativo de IA hospedado no RAGFlow, responsável pela ingestão de dados e consulta à base vetorial para gerar respostas |
| Base Vetorial Interna | Base de conhecimento estruturada a partir um conjunto de dados de notícias coletadas e gerenciada pelo RAGFlow, utilizada para recuperação de informações relevantes |
| Interface Web Nativa | Interface gerada automaticamente no RAGFlow para interação com o usuário |
| Documentação | Mantida em GitHub Pages, separada da aplicação, usando Markdown |

## Fluxo de Funcionamento

1. O usuário acessa o aplicativo FakeBuster via interface nativa do RAGFlow.
2. O FakeBuster recebe a pergunta do usuário.
3. O mecanismo de recuperação do RAGFlow consulta a base vetorial interna para buscar informações relevantes.
4. O modelo de linguagem (LLM) Sabiá gera a resposta, utilizando o contexto recuperado da base vetorial.
5. A resposta é enviada ao usuário com as fontes citadas, caso aplicável.

## Tecnologias Utilizadas


* Plataforma de Execução: RAGFlow (SaaS)
* Armazenamento de Dados: Banco vetorial interno (embeddings gerenciados pelo RAGFlow)
* Modelo de IA: Sabiá (Maritaca AI) via RAGFlow
* Framework de Recuperação: LlamaIndex (nativo no RAGFlow)
* Controle de Versão: Git + GitHub
* Documentação: Markdown + GitHub Pages

## Diagrama da Arquitetura

<p align="center">
  <img src="https://www.mermaidchart.com/raw/a087e90a-b08b-4629-8938-43852d854162?theme=light&version=v0.1&format=svg" alt="Diagrama da Arquitetura" width="300">
</p>

## Histórico do documento

| Versão | Data       | Descrição            | Autor         |
| ------ | ---------- | -------------------- | ------------- |
| 1.0    | 21/04/2025 | Criação do documento | Amanda Campos |
| 1.1    | 27/04/2025 | Atualização da arquitetura| Amanda Campos |