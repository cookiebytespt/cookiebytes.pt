---
ref: home-assistant-automations
layout: post
title: Cinco automações de Home Assistant que instalamos em todas as casas inteligentes
description: Depois de dezenas de instalações, estas são as automações que os clientes continuam a usar seis meses depois — e as que deixámos discretamente de recomendar.
category: tech
emoji: 🏠
read_time: 7 min de leitura
lede: Depois de dezenas de instalações de Home Assistant, surgiu um padrão — os clientes adoram as automações durante duas semanas e depois desativam discretamente a maioria. Estas cinco são as que sobrevivem. São aborrecidas, e é exatamente essa a ideia.
author_emoji: 🏠
author_note: Testadas em casas reais, incluindo a nossa — onde a primeira fuga detetada pelo sensor foi a taça de água do Billy.
---

## 1. Luzes que seguem o sol, não um horário

Horários fixos para a iluminação avariam duas vezes por ano e parecem errados na maioria das noites. Em vez disso, acendemos as luzes da noite com base na elevação do `sun.sun` — normalmente quando o sol desce abaixo de 1,5° — e aumentamos o brilho gradualmente. Ninguém repara nesta automação, e esse é o maior elogio que uma automação pode receber.

## 2. O reset de «saíram todos»

Uma automação, disparada quando a última pessoa sai de casa: luzes apagadas, termóstato em modo eco, leitores multimédia em pausa e uma notificação apenas se ficou uma janela aberta. Usamos entidades de pessoa agrupadas numa contagem da `zone.home` em vez de device trackers diretamente — os telemóveis mentem, e a presença por Wi-Fi oscila quando o telemóvel de alguém entra em repouso.

<div class="callout">💡 O detalhe essencial: um atraso de 10 minutos com uma condição de cancelamento. Sem isso, ir pôr o lixo lá fora desliga a casa.</div>

## 3. Modos da manhã, não alarmes da manhã

Em vez de «às 7:00 faz X», configuramos um `input_select` para o modo da casa (a dormir, a acordar, dia, noite, ausente). As automações reagem à mudança de modo, e o próprio modo pode ser alterado pela hora, pelo primeiro movimento na cozinha ou manualmente num dashboard. Separar «o que acontece» de «quando acontece» é, de longe, a maior vitória de manutenção no Home Assistant.

## 4. Alertas de água e fugas que escalam

Sensores de fuga debaixo do lava-loiça, atrás da máquina de lavar e junto ao esquentador. Primeiro alerta: notificação no telemóvel. Sem confirmação em 5 minutos: notificação para toda a gente em casa e as luzes a piscar a vermelho. Se houver uma válvula inteligente instalada, corta a água aos 10. A escada de escalonamento importa — uma única notificação silenciosa às 3 da manhã não protege nada.

## 5. O resumo noturno «está tudo bem?»

Às 22:00 a casa envia uma mensagem: portas trancadas ou não, janelas abertas ou não, baterias abaixo de 15% e qualquer entidade que esteja `unavailable` há mais de uma hora. Uma mensagem, uma vez por dia. Isto substitui a dúzia de notificações irritantes que levam as pessoas a silenciar a app de vez.

<div class="divider">🍪 🍪 🍪</div>

## O que deixámos de recomendar

Luzes ativadas por movimento na sala (ótimas em corredores, insuportáveis em noite de cinema), anúncios por voz para eventos de rotina (a novidade passa em dias) e qualquer automação que obrigue o cliente a lembrar-se de que ela existe. Se precisa de manual, é uma funcionalidade, não uma automação.

A pensar numa instalação de Home Assistant? [Fazemos instalações](/pt/#contact) — sensores, dashboards, escadas de escalonamento e tudo.
