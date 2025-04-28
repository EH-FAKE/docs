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
  Framework de orquestração de fluxos RAG (Retrieval-Augmented Generation), facilitando a integração entre fontes de dados e modelos de linguagem. Versão mais recente: v0.18.0

  

- **[NGROK](https://github.com/NGROK)**  
  Ferramenta que cria túneis seguros para expor servidores locais à internet, muito utilizada para testes de aplicações web, APIs e integração com webhooks. Versão mais recente do SDK Go: v1.7.0

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

## Pré-requisitos para Rodar o docs do projeto

Para rodar o projeto **EhFake?**, é necessário:

- **Python 3.10+** instalado.
- Instalar o **MkDocs** 
  ```bash
    pip install mkdocs

- Clonar o repositório e instalar as dependências: <br>
    ```bash 
    pip install -r requirements.txt

- Rodar mkdocs serve:
    ```bash 
    mkdocs serve

- Abrir o localhost

    ```bash 
    http://127.0.0.1:8000/docs/


## Contribuição

Para contribuir com o projeto, siga as instruções no [Guia de Contribuição](docs/guia-de-contribuicao)
