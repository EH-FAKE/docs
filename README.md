# <center> EHFAKE - Documentação </center>

O **EhFake** é um projeto em desenvolvimento por estudantes da Universidade de Brasília (UnB), com o objetivo de criar uma tecnologia baseada em Machine Learning capaz de identificar e classificar notícias desinformativas.

## Contexto 

O projeto tem como objetivo: <br>
    - Coletar informações de diversas fontes. <br>
    - Verificar se há presença de desiformação. <br>
    - Retornar uma lista de notícias classificadas de acordo com critérios definidos.


## Estrutura da Solução

- **Query**: Tema que está sendo pesquisado.
- **Pergunta**: Prompt formulado por especialistas para análise de notícias.
- **Explicação para IA**: Contextos fornecidos para a IA realizar a classificação.
- **Classificação**:
  - Desinformativo
  - Informativo
  - Não se encaixa

## Tecnologias Utilizadas

O projeto **EhFake** faz uso de tecnologias modernas e ferramentas de código aberto voltadas para o desenvolvimento de aplicações com IA generativa, recuperação de informações e controle de segurança em interações com LLMs. Abaixo estão listadas as principais ferramentas e bibliotecas utilizadas:

- **[RAGFlow](https://github.com/infiniflow/ragflow)**  
  Framework de orquestração de fluxos RAG (Retrieval-Augmented Generation), facilitando a integração entre fontes de dados e modelos de linguagem.

- **[Maritaca AI (modelo Sabiá)](https://github.com/maritaca-ai)**  
  Projeto brasileiro de modelos de linguagem open source, com foco no idioma português. Utilizado como base linguística para análise de notícias.

- **[LlamaIndex](https://github.com/run-llama/llama_index)**  
  Framework que permite conectar dados externos (como documentos, bancos de dados, APIs) com LLMs, facilitando a construção de pipelines de RAG.

- **[NeMo Guardrails](https://github.com/NVIDIA/NeMo-Guardrails)**  
  Ferramenta desenvolvida pela NVIDIA para garantir segurança e controle no comportamento de modelos de linguagem, adicionando limites (guardrails) às interações.

- **[Streamlit](https://github.com/NVIDIA/NeMo-Guardrails)**  
  Framework Python para desenvolvimento rápido de interfaces web interativas, utilizado para exibição e teste do protótipo do EhFake.

## Requisitos Funcionais

- **Coleta contínua** de notícias de jornais e revistas citados.
- **Pesquisa personalizada** de notícias conforme critérios definidos:
  - **Tema**:
    - Estabilidade democrática
    - Saúde
    - Meio ambiente
    - Mulheres
    - Povos e comunidades tradicionais
    - População negra
    - População LGBTQIA+
  - **Fonte da notícia**: Nome do jornal ou todas as desinformativas.


## Instalação e uso

```bash
# Clonar o repositório
git clone https://github.com/EH-FAKE/docs.git
cd docs

# Criar e ativar ambiente virtual
python -m venv .venv 
python3 -m venv .venv  # Caso não tenha o Python instalado

source .venv/bin/activate  # Linux/Mac Os
.\.venv\Scripts\activate.bat  # Windows

# Instalar dependências
pip install -r requirements.txt

# Rodar localmente
mkdocs serve
```

## Pré-requisitos para Rodar o Projeto

Para rodar o projeto **EhFake?**, é necessário:

- **Python 3.10+** instalado.
- Instalar o **MkDocs** e o tema **Material for MkDocs**:
  ```bash
    pip install mkdocs mkdocs-material

- Clonar o repositório e instalar as dependências: <br>
    ```bash 
    pip install -r requirements.txt

- Ter acesso às ferramentas e bibliotecas utilizadas no projeto:
    - RAGFlow

    - Maritaca AI

    - LlamaIndex

    - NeMo Guardrails


## Estrutura do Projeto

## Contribuição

Para contribuir com o projeto, siga as instruções no [Guia de Contribuição](guia-de-contribuicao/politica-de-branches.md).


