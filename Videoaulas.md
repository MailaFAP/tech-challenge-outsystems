# Videoaulas

Aplicação web para gestão e consumo de videoaulas, com experiência diferenciada para Professor/a e Aluno/a, além de funcionalidades de autenticação, recuperação de acesso e gestão de perfil.

## 🔗 Acesso Rápido

- **[Acessar a aplicação](https://personal-z1nrjkzm-dev.outsystems.app/Videoaulas)**
- **[Documentação REST API](https://personal-z1nrjkzm-dev.outsystems.app/Videoaulas/rest/v1/)**

## 📑 Índice da Documentação

1. [Sobre o projeto](#1-sobre-o-projeto)
2. [Objetivo](#2-objetivo)
3. [Escopo funcional atual](#3-escopo-funcional-atual)
4. [Arquitetura funcional](#4-arquitetura-funcional)
5. [Modelo de dados](#5-modelo-de-dados)
6. [Estrutura de telas](#6-estrutura-de-telas)
7. [Lógica de negócio](#7-lógica-de-negócio)
8. [Integração REST](#8-integração-rest)
9. [Layout e componentes reutilizáveis](#9-layout-e-componentes-reutilizáveis)
10. [Regras de negócio atuais](#10-regras-de-negócio-atuais)
11. [Casos de uso principais](#11-casos-de-uso-principais)

## 1. Sobre o projeto
O Videoaulas é um app construído para disponibilizar conteúdos em vídeo de forma simples e direta.

### Proposta funcional atual

- permitir que professores publiquem e mantenham videoaulas
- permitir que alunos consultem a lista de conteúdos e acessem os links dos vídeos



O projeto foi estruturado em fluxos separados para:

- negócio principal
- autenticação e perfil
- layouts e blocos reutilizáveis
- integração REST

## 2. Objetivo
O objetivo principal do app é servir como um catálogo de videoaulas com manutenção simples de conteúdo.

Ele pode ser usado como base para:

- portal interno de formação
- ambiente educacional simples
- MVP de ensino online
- biblioteca de conteúdos em vídeo
- repositório de aulas gravadas

## 3. Escopo funcional atual

### 3.1 Funcionalidades para Professor/a

- visualizar a lista de vídeos
- criar novos vídeos
- editar vídeos existentes
- excluir vídeos

### 3.2 Funcionalidades para Aluno/a

- visualizar a lista de vídeos
- abrir o link do vídeo selecionado para assistí-los

## 4. Arquitetura funcional

A aplicação está organizada em vários fluxos:

![Domain Storytelling - Videoaulas](imagens/Domain%20Storytelling%20App%20Video-Aulas_2026-05-04%20(1).png)

### MainFlow

Fluxo principal do negócio.

**Contém as telas:**

- EscolherPerfil
- Video
- CriarEditar

### Common

Fluxo com componentes e telas comuns.

**Inclui:**

- perfil do utilizador
- blocos reutilizáveis de navegação e identidade

### Layouts

Fluxo com blocos de layout reutilizáveis.

**Inclui:**

- LayoutTopMenu
- LayoutBlank
- LayoutSideMenu
- LayoutBase

## 5. Modelo de dados

### 5.1 Entidade principal

#### Video

Entidade central do domínio.

#### Atributos

| Atributo | Tipo | Descrição | Obrigatório | Tamanho Máximo |
|----------|------|-----------|-------------|---|
| Id | Long Integer | Identificador automático | Sim | - |
| Title | Text | Título do vídeo | Sim | 50 |
| Description | Text | Descrição do vídeo | Sim | 250 |
| VideoURL | Text | Endereço do vídeo | Sim | 250 |

### 5.2 Observações

- a entidade Video está exposta para leitura

## 6. Estrutura de telas

### 6.1 EscolherPerfil
Tela de entrada funcional do app.

**Responsabilidade**

Permitir ao utilizador selecionar como deseja navegar no catálogo:

- Professor
- Aluno

**Comportamento**

- envia o parâmetro UserRole para a tela Video
- altera a experiência da próxima tela com base nesse valor

### 6.2 Video

Tela principal de listagem de conteúdos.

**Responsabilidade**

- listar os vídeos cadastrados
- permitir ordenação
- permitir paginação
- adaptar ações conforme o perfil recebido

**Parâmetros de entrada**

- UserRole (Text)

**Aggregate principal**

- GetVideos: busca registros da entidade Video

**Comportamento por perfil**

**Professor/a**

- visualiza botão Novo
- pode clicar no título de um vídeo existente para editar

**Aluno/a**

- ao clicar no título, é redirecionado para a URL do vídeo

**Recursos visuais**

- tabela de registos
- cabeçalho com ordenação
- paginação
- mensagem de vazio quando não há dados

### 6.3 CriarEditar

Tela de manutenção do conteúdo.

**Responsabilidade**

- criar novo vídeo
- editar vídeo existente
- excluir vídeo existente

**Parâmetros de entrada**

- VideoId (Video Identifier)

**Comportamento**

- se VideoId estiver vazio, a tela entra em modo de criação
- se VideoId tiver valor, a tela entra em modo de edição

**Títulos dinâmicos**

- Novo vídeo
- Editar Vídeo

**Campos do formulário**

- Title
- Description
- VideoURL

**Ações**

- **Publicar**: grava ou atualiza
- **Excluir**: remove o registo atual
- **Voltar**: regressa à tela anterior

## 7. Lógica de negócio

### 7.1 Server Actions customizadas

#### Video_CreateOrUpdate

Responsável por persistir um registo da entidade Video.

**Entrada**

- Video

**Saída**

- VideoId

**Responsabilidade**

- executar criação ou atualização do vídeo conforme o contexto

#### Video_Delete

Responsável por excluir um vídeo.

**Entrada**

- VideoId

**Saída**

- IsSuccess

**Responsabilidade**

- executar a remoção do registo

### 7.2 Screen Actions relevantes

#### Tela Video

**OnSort**

- aplica ordenação na listagem
- reinicia paginação

**OnPaginationNavigate**

- atualiza o índice de navegação
- recarrega os dados

#### Tela CriarEditar

**GravarOnClick**

- valida formulário
- chama ação de persistência
- mostra mensagem de sucesso
- regressa à tela anterior

**ExcluirOnClick**

- chama ação de exclusão
- mostra mensagem de sucesso
- regressa à tela anterior

## 8. Integração REST

O app expõe um serviço REST em:

```
/rest/v1
```

> 📖 **[Ver documentação completa da API REST](https://personal-z1nrjkzm-dev.outsystems.app/Videoaulas/rest/v1/)**

### 8.1 Endpoints disponíveis

- **GET /GetAllVideo**: Retorna a lista de vídeos.
- **POST /CreateVideo**: Cria um novo vídeo.
- **PUT /UpdateVideo**: Atualiza um vídeo existente.
- **DELETE /DeleteVideo**: Exclui um vídeo pelo identificador.

### 8.2 Estrutura de suporte REST

#### CriaVideo_Return

Estrutura usada no retorno da criação.

**Campos:**

- VideoId
- IsSuccess

### 8.3 Finalidade

A camada REST permite reutilização do catálogo por:

- integrações futuras
- apps consumidoras
- testes externos de API
- evolução para arquitetura orientada a serviços

## 9. Layout e componentes reutilizáveis
O app usa blocos de layout e componentes visuais já estruturados.

### 9.1 Layout principal

- **LayoutTopMenu**: Esse layout é usado nas principais telas de negócio.

### 9.2 Blocos comuns

- Menu
- UserInfo
- ApplicationTitle


### 9.3 Situação atual

A aplicação diferencia o comportamento entre Professor/a e Aluno/a com base no parâmetro:

- **UserRole**

Isso controla a experiência da interface.


## 10. Regras de negócio atuais

### 10.1 Gestão de vídeos

- um vídeo deve possuir título
- um vídeo deve possuir descrição
- um vídeo deve possuir URL

### 10.2 Experiência por perfil

- professor pode manter conteúdo
- aluno apenas consome conteúdo

### 10.3 Acesso ao vídeo

- o acesso ao conteúdo do aluno ocorre por redirecionamento à VideoURL

## 11. Casos de uso principais

### Caso 1 — Professor publica uma videoaula

- entra na tela de vídeos
- clica em Novo
- preenche título, descrição e URL
- clica em Publicar
- retorna à lista

### Caso 2 — Professor edita uma videoaula

- entra na lista
- clica no título do vídeo
- altera os dados
- publica novamente

### Caso 3 — Professor exclui uma videoaula

- abre um vídeo existente
- clica em Excluir
- confirma a exclusão

### Caso 4 — Aluno visualiza conteúdo

- escolhe perfil de aluno
- entra na lista
- clica no título do vídeo
- é redirecionado para a URL





