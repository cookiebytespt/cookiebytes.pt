---
ref: brindigrafica-website
layout: post
title: Reconstruir o brindigrafica.pt — uma loja em Next.js sobre um backend em Swift
description: A Brindigráfica vende brindes publicitários — t-shirts, bonés, canetas, tudo. Reconstruímos o site como uma loja em Next.js suportada por uma API em Vapor, com Auth0 para identidade, um backoffice para a equipa e importação de catálogos de fornecedores. Eis a stack, onde corre e as partes que nos deram luta.
category: tech
emoji: 🎁
read_time: 9 min de leitura
lede: A Brindigráfica precisava de mais do que um site de montra. Precisava de um catálogo, de uma loja, de pedidos de orçamento, de contas de cliente e de um sítio onde a própria equipa pudesse gerir tudo isso — sem fingir ser uma empresa que vive dentro de uma app. Este é o relato do que construímos, de onde corre e das decisões que voltaríamos a tomar (e da que tivemos de desfazer logo no primeiro dia).
author_emoji: 🍪
author_note: O Rocky apagou exatamente zero configurações de produção durante este projeto, o que contamos como recorde pessoal. A Peggy reviu o modelo de dados e não disse nada, o que é o mais próximo de uma aprovação que ela dá.
---

<figure>
  <img src="/assets/blog/brindigrafica/banner.png" alt="A nova loja brindigrafica.pt, construída pela CookieBytes">
</figure>

## O pedido

A [Brindigráfica](https://brindigrafica.pt) é uma empresa portuguesa de brindes publicitários: t-shirts, bonés, canetas, sacos personalizados e as mil outras coisas onde uma empresa põe o seu logótipo. O site antigo fazia o que a maioria dos sites do setor faz — mostrava umas fotos e um número de telefone — e o negócio a sério acontecia por email, telefone e folhas de cálculo.

O pedido foi trazer esse negócio para online, por fases. Não era "construir uma Amazon". Um catálogo de produtos a sério, uma loja onde os clientes pudessem montar uma encomenda ou pedir um orçamento, contas de cliente para acompanhar essas encomendas e orçamentos, e — a parte que mais conta no dia a dia — um backoffice onde a equipa da Brindigráfica pudesse gerir produtos, encomendas e utilizadores sem nos ligar.

Definimos a v1 de forma deliberada: catálogo, carrinho, encomendas e pedidos de orçamento, **ainda sem pagamentos online**. Brindes publicitários são orçamentados, negociados e faturados; forçar um checkout nesse fluxo seria construir muito bem a coisa errada.

<div class="divider">🍪 🍪 🍪</div>

## A stack

O projeto inteiro vive num só repositório, `Brindigrafica/`, dividido em `frontend/`, `backend/` e `docs/`. Consolidámos assim desde cedo — ter tudo num sítio tornou o deployment e as alterações transversais (um campo novo num produto toca nas duas metades) bem menos dolorosos.

| Camada | O que usámos |
| --- | --- |
| Frontend | Next.js (App Router), TypeScript |
| API | Swift no servidor — Vapor 4, Fluent ORM |
| Base de dados | PostgreSQL |
| Identidade | Auth0 (New Universal Login, Management API) |
| Alojamento do frontend | Vercel, Node 24, `brindigrafica.pt` + `www` |
| Alojamento da API | <!-- TODO: onde corre a API Vapor? (Fly.io / Hetzner / Docker num VPS / …) --> |

**Porquê Swift no servidor?** Em parte porque gostamos — fazemos apps móveis, e um backend na mesma linguagem em que pensamos no iOS é um ganho real de produtividade. Mas sobretudo porque o Vapor é uma framework madura e fortemente tipada que produz binários pequenos e rápidos, e o Fluent dá-nos um esquema baseado em migrações que vive no controlo de versões ao lado dos modelos. A tipagem forte do lado da API apanhou mais bugs em tempo de compilação do que teríamos encontrado numa semana de testes.

**Porquê Next.js à frente?** Uma loja precisa de renderizar depressa e indexar bem — os server components e o App Router dão-nos isso no catálogo e nas páginas de produto — enquanto o carrinho, a conta e o backoffice são verdadeiramente interativos. Uma framework cobre os dois lados sem termos de manter dois frontends.

<div class="divider">🍪 🍪 🍪</div>

## Como as peças encaixam

O frontend nunca fala diretamente com a API a partir do browser. Cada pedido passa por um **proxy BFF** dentro do Next.js: route handlers que guardam a sessão do lado do servidor, anexam o token da API e reencaminham a chamada. Os tokens nunca chegam ao cliente, a API só vê um único chamador de confiança, e a superfície exposta ao browser mantém-se pequena. Arrancámos a camada de dados fictícios que tínhamos usado na fase de design em vez de a manter como "fallback" — um fallback que serve produtos falsos em silêncio é um bug à espera de ser descoberto por um cliente.

A API em si é RESTful, em inglês do princípio ao fim. Uma pequena decisão que nos poupou discussões mais tarde: os parâmetros de query são em inglês apesar de o produto ser português — `?category=`, não `?categoria=`. A UI é traduzida; a API não é uma UI.

<figure>
  <img src="/assets/blog/brindigrafica/architecture.png" alt="Diagrama de arquitetura — browser para Next.js na Vercel, proxy BFF para a API Vapor, PostgreSQL atrás, Auth0 para identidade">
  <figcaption>Browser → Next.js (Vercel) → proxy BFF → API Vapor → PostgreSQL. O Auth0 fica ao lado e é a única coisa que alguma vez vê uma password.</figcaption>
</figure>

**As contas** são Auth0 de ponta a ponta. Os clientes têm uma área de conta com *Encomendas* e *Orçamentos*; os administradores têm o backoffice. Personalizámos os ecrãs e emails do próprio Auth0 com a marca Brindigráfica — New Universal Login com um page template, mais todos os templates de email (boas-vindas, recuperação de password, verificação) — e guardámos tudo como ficheiros no repositório, com um pequeno script que os aplica através da Management API. A marca é código, não algo que alguém montou à mão num dashboard e que ninguém consegue reproduzir.

<div class="divider">🍪 🍪 🍪</div>

## O backoffice

É aqui que a equipa da Brindigráfica realmente vive. É uma subpágina do frontend em `/backoffice` (chamámos-lhe `/admin` durante cerca de um dia; ganhou a palavra do cliente), que reutiliza exatamente a mesma API com verificação de papéis — sem segunda app, sem segundo deploy, sem segundo conjunto de bugs.

A partir daí a equipa consegue:

- **Gerir produtos** — criar, editar, publicar, desativar.
- **Gerir encomendas e pedidos de orçamento** — avançá-los no seu ciclo de vida, ver o que um cliente pediu e quando.
- **Gerir utilizadores** — incluindo adicionar e convidar novos. Optámos por fazer os convites através da Management API do Auth0: a API cria o utilizador no Auth0 e o Auth0 envia o email personalizado para definir a password. Não queríamos ser donos de um pipeline de email só para enviar uma mensagem, e o Auth0 já trata da entregabilidade, expiração e reenvios melhor do que nós nos daríamos ao trabalho.

Uma regra com que tivemos cuidado: **apagar produtos**. Um produto criado manualmente pode ser apagado de forma permanente — mas só se não estiver associado a nenhuma encomenda ou pedido de orçamento. Tudo o que tenha histórico é desativado, nunca apagado, para que um orçamento de março continue a mostrar o que foi orçamentado em março. Produtos importados de fornecedores só podem ser desativados. O que nos leva à parte difícil.

<div class="divider">🍪 🍪 🍪</div>

## Os desafios

### 1. Assumimos que o Vapor 5 já tinha saído. Não tinha.

Começámos a API em Vapor 5 — novo design async-first, mais limpo que o 4, exatamente o que queríamos escrever. Depois fomos à procura de um driver Fluent e descobrimos que o Vapor 5 ainda estava em alpha. Tínhamos confundido "muito discutido" com "lançado". A correção foi simples (voltar a Vapor 4 + Fluent + PostgreSQL, uma combinação estável e muito testada), mas é uma lição que vamos continuar a repetir a nós próprios: *ver a página de releases, não os posts de blog, antes de escolher uma versão major.*

### 2. Os catálogos dos fornecedores têm seis formatos diferentes

A Brindigráfica não fabrica; compra a fornecedores, cada um com o seu catálogo, os seus preços e — o mais importante — a sua própria ideia do que é uma API. Construímos conectores de importação para os três que conseguimos:

- **Stricker** — importação de catálogo estruturado.
- **Cifra** — API com autenticação por token.
- **Valento** — REST, decifrado a partir dos manuais deles.

Outros três (Tejo Brinde, Chuvitex, SOL'S) simplesmente não têm integração que se aproveite, por isso esses produtos ficam manuais.

As decisões sobre o modelo de dados foram tão importantes como os conectores. A origem do fornecedor é registada em todos os produtos importados, mas é **só para o backoffice** — um cliente nunca vê quem fez a caneta. Não fazemos **nenhum arredondamento de preços** na importação; o que o fornecedor diz é o que entra na base de dados, e as decisões de preço são tomadas por pessoas por cima disso. E nada do que é importado fica online automaticamente: cada importação entra como não publicada e é **revista antes de ser publicada**. Um conector que empurrasse 2.000 produtos diretamente para o site público porque um fornecedor mudou o nome a uma categoria daria uma manhã muito má.

### 3. Manter os segredos fora do cliente

O proxy BFF existe por causa disto. O primeiro rascunho do frontend tinha o browser a chamar a API com um bearer token, o que serve para uma demo e é péssimo para uma loja com dados de clientes. Mover todas as chamadas à API para trás de route handlers do Next.js deu mais trabalho de canalização, mas a fronteira do token tem agora exatamente uma linha de espessura e está no servidor.

### 4. Personalizar algo que não alojamos

Os ecrãs do Auth0 são a primeira coisa que um cliente novo vê, e pareciam Auth0. O Classic Universal Login deixar-nos-ia escrever HTML à mão, mas é o caminho legado. A abordagem New Universal Login + page template é mais limitada e levou mais tempo a ficar bem, mas é a suportada e sobrevive às atualizações do próprio Auth0. Preferimos lutar com as restrições da coisa atual a ser donos de uma página HTML à medida que se parte daqui a um ano.

<div class="divider">🍪 🍪 🍪</div>

## Deployment

O **frontend** faz deploy para a Vercel a partir do repositório — produção em `brindigrafica.pt` e `www.brindigrafica.pt`, com URLs de pré-visualização para cada branch para que o cliente possa rever uma alteração antes de ela ir ao ar. Zero configuração além de apontar o DNS.

A **API** <!-- TODO: preencher — plataforma, como o binário Vapor é construído (Dockerfile?), como o Postgres é alojado/copiado, como as migrações correm no deploy -->.

A configuração do Auth0 — page template de login, templates de email, o script da Management API que os aplica — vive em `docs/` e no repositório, para que um tenant novo possa ser montado só a partir dos ficheiros.

<div class="divider">🍪 🍪 🍪</div>

## O que vem a seguir

A v1 saiu com a loja deliberadamente sem pagamentos. O roadmap a partir daqui:

- **Pagamentos online** — depois de o fluxo de orçamento e encomenda ter tido clientes reais durante algum tempo e sabermos que produtos fazem realmente sentido comprar de prateleira.
- **Integração com calendário** — marcações de clientes sincronizadas com o Google Calendar, mais uma linha temporal de encomendas/produção que a equipa consiga ver de relance.
- **Mais conectores de fornecedores**, à medida que os restantes ganhem APIs — ou que descubramos como interpretar as listas de preços deles sem uma.

<div class="divider">🍪 🍪 🍪</div>

## O que diríamos ao próximo cliente

Construa o backoffice primeiro na cabeça, mesmo que construa a loja primeiro em código. A equipa da Brindigráfica não quer saber de server components nem de concorrência em Swift; quer que adicionar um produto demore trinta segundos e que um orçamento de há seis meses continue a mostrar o que foi orçamentado. Todas as decisões de arquitetura acima — o proxy, as regras de eliminação, a importação com revisão antes de publicar — vieram de perguntar o que a *equipa* precisa, não o que o *site* precisa.

Precisa de uma loja, de um catálogo ou de um backend que a sua equipa consiga mesmo gerir? [Fale connosco](/pt/#contact). Nós trazemos a manteiga.
