# Bem-vindo ao EHFAKE

Esta é a documentação oficial do **EhFake**, no qual temos como objetivo principal contribuir para o projeto CheckUp, adicionando novas funcionalidades e melhorando as existentes. 

Caso queira ter acesso a toda a documentação do nosso projeto, basta acessar o nosso [GitHub Pages](https://eh-fake.github.io/docs/)

## Contexto CheckUp

Check-up é um projeto que tem como objetivo analisar a presença de desinformação em anúncios publicitários de saúde que circulam em grandes sites de notícias do Brasil.

Este repositório contém o código de uma ferramenta desenvolvida pelo [**Aos Fatos**](https://aosfatos.org) para examinar os anúncios nativos de dez portais (listados abaixo).

A ferramenta tem três módulos: um crawler que coleta links de cada site, um raspador que captura e arquiva anúncios encontrados e um classificador de anúncios por tema que usa um grande modelo de linguagem (LLM).

Apesar de funcionar apenas com os dez sites cobertos pelo projeto, este código pode ser facilmente adaptado para ser usado em outros endereços, como mostramos abaixo.

O código deste projeto pode ser usado apenas para fins não-comerciais e com atribuição de crédito.

## Portais de notícias
Este repositório inicialmente contempla a coleta de notícias dos seguintes portais:

 - [Estadão](https://www.estadao.com.br)
 - [Folha](https://www.folha.uol.com.br)
 - [Globo](https://oglobo.globo.com/)
 - [IG](https://www.ig.com.br)
 - [Metrópoles](https://www.metropoles.com)
 - [R7](https://www.r7.com)
 - [RBS](https://www.clicrbs.com.br)
 - [Terra](https://www.terra.com.br)
 - [Veja](https://veja.abril.com.br)
 - [UOL](https://www.uol.com.br)

## Execução dos Scripts

### Iniciar os Serviços

Para iniciar os serviços necessários, utilize o comando:

`make start`

Este comando inicia um container docker com um banco de dados
e um container com `shell` com Python instalado.

Caso queira iniciar mais serviços, consultar a [descrição oficial do projeto](https://github.com/EH-FAKE/check-up/blob/develop/README.md)
```

## Contribuição

Acesse [Guia de Contribuição](CONTRIBUTING.md) para entender como colaborar.