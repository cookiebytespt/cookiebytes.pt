---
ref: chaos-engineering-rocky
layout: post
title: O que o Rocky nos ensinou sobre engenharia do caos
description: O nosso Engenheiro-Chefe do Caos já apagou dois ficheiros do Figma e uma configuração de produção. Eis como um gato tornou a nossa pipeline de deploy genuinamente mais resiliente.
category: crew
emoji: 🐱
read_time: 5 min de leitura
lede: A Netflix tem o Chaos Monkey. Nós temos um gato. O cargo do Rocky — Engenheiro-Chefe do Caos — começou como uma piada depois de ele ter apagado uma configuração de produção ao dormir em cima de um teclado. Depois percebemos que ele tinha encontrado uma falha real, e a piada tornou-se uma disciplina.
author_emoji: 🐱
author_note: Revisto pelo Rocky, que passou duas vezes por cima do rascunho e apagou um parágrafo. Provavelmente era o mais fraco.
---

## O incidente

Uma terça-feira à tarde. O Rocky instala-se em cima de um portátil, como os gatos fazem. Doze minutos depois, um ficheiro de configuração num repositório de produção tinha sido substituído por algo que só se pode descrever como `jjjjjjjjjjjjj4`, feito commit (gravação automática mais um atalho de teclado muito infeliz) e publicado pela nossa pipeline — que não validava nada.

O serviço foi abaixo. O rollback demorou 40 minutos porque nunca o tínhamos ensaiado. O Rocky dormiu durante toda a recuperação.

## O que o gato pôs a nu

Todas as partes daquela falha eram nossas, não dele. A pipeline publicava alterações de configuração sem validação de esquema. Nada nos alertou até os utilizadores o fazerem. E o nosso rollback era uma página de wiki atualizada pela última vez dois trimestres antes. O Rocky não partiu o sistema — revelou que ele já estava partido, e saiu mais barato do que uma falha a sério com um cliente a sério a ver.

<div class="callout">🐾 Engenharia do caos numa frase: se um gato em cima de um teclado consegue deitar abaixo a produção, o problema era a produção, não o gato.</div>

## O que mudou

Os ficheiros de configuração passam agora por validação de esquema antes de qualquer deploy — um valor malformado faz falhar a pipeline, não o serviço. Os deploys são faseados, com rollback automático quando os health checks falham. E uma vez por mês fazemos um «simulacro Rocky»: alguém estraga intencionalmente um ambiente de staging, sem aviso, e medimos quanto tempo demoram a deteção e a recuperação. No primeiro simulacro precisámos de 35 minutos só para dar por isso. Agora estamos abaixo dos quatro.

## A lição honesta

A maioria das equipas pequenas salta o trabalho de resiliência porque nunca parece urgente — até à falha, quando de repente é a única coisa que importa. Não precisa de ferramentas à escala da Netflix. Precisa de barreiras de validação, rollbacks ensaiados e algo imprevisível a fazer pressão. Essa última parte, por acaso, temos cá em casa: ruivo e fundamentalmente sem remorsos.

<div class="divider">🍪 🍪 🍪</div>

Quer uma pipeline à prova de gatos? [Fale connosco](/pt/#contact) — auditorias de DevOps são um dos nossos trabalhos favoritos e, sim, o Rocky dá consultoria.
