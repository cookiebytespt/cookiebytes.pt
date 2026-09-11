---
ref: smart-irrigation-sonoff-24vac
layout: post
title: "Rega inteligente do jardim com um Sonoff 4CH Pro e electroválvulas Rain Bird"
description: Como construímos um controlador de rega inteligente de quatro zonas com um Sonoff 4CH Pro R3, um transformador de 24 VAC e electroválvulas Rain Bird — com guia de ligações completo, para que o possa replicar.
category: tech
emoji: 💧
read_time: 8 min de leitura
lede: Os controladores de rega comerciais ou são temporizadores básicos ou caixas caras dependentes da cloud. Queríamos quatro zonas de rega controláveis a partir do Home Assistant, por isso construímos o nosso com um Sonoff 4CH Pro R3, um transformador de 24 VAC e válvulas Rain Bird — por bem menos de 100 €. Aqui fica a receita completa.
author_emoji: 💧
author_note: Testado durante todo o verão num jardim português a sério. A Cookie supervisionou a abertura das valas. O Rocky tentou supervisionar as ligações elétricas e foi escoltado para fora.
---

## 💡 A ideia

Um sistema de rega automático são, na verdade, só três coisas: electroválvulas que abrem as linhas de água, algo que as liga e desliga, e um horário. As válvulas são um problema resolvido — a Rain Bird faz válvulas solenoides de 24 VAC há décadas. O «algo que as liga» é onde os controladores comerciais nos apanham: interfaces pouco práticas, apps proprietárias e nenhuma integração com o resto da casa inteligente.

Um Sonoff 4CH Pro R3 resolve isto de forma elegante. É um relé Wi-Fi de quatro canais com **contactos secos** — cada relé é um interruptor isolado, eletricamente independente daquilo que alimenta a placa. Isso significa que pode comutar um circuito de válvulas de baixa tensão completamente separado, que é exatamente o que a rega precisa. Quatro relés, quatro zonas de rega.

<div class="divider">💧 💧 💧</div>

## 🎛️ Porque é que isto é melhor do que um controlador de loja

Um controlador de rega convencional faz uma coisa: corre zonas com um temporizador. Esta montagem é um conjunto de blocos abertos, e isso torna-a muito mais personalizável:

**Controlo a partir de qualquer lado, à sua maneira.** De origem, o Sonoff funciona com a **app eWeLink** — horários, temporizadores e controlo manual a partir do telemóvel. Também traz **suporte RF de 433 MHz**, por isso um comando barato pode ativar zonas a partir do jardim sem tocar no telemóvel, e os botões físicos da própria unidade continuam a funcionar quando o Wi-Fi não funciona.

**Liga-se ao Home Assistant.** Cada zona passa a ser uma entidade switch (a integração eWeLink funciona de imediato; a placa também pode ser reprogramada com ESPHome ou Tasmota para controlo totalmente local). A partir daí, a rega deixa de ser um temporizador e passa a ser automações: regar ao amanhecer, quando a evaporação é menor, saltar a rega quando a previsão indica chuva, regar mais tempo durante uma onda de calor com base num sensor de temperatura exterior e receber uma notificação se uma zona regou mais tempo do que o esperado.

**Cresce consigo.** Precisa de uma quinta zona? Junte outro Sonoff. Quer um sensor de humidade do solo para decidir se deve sequer regar? É mais uma entidade na mesma automação. Nenhum controlador convencional deixa reescrever a sua lógica — este é *só* lógica.

<div class="divider">💧 💧 💧</div>

## 🛒 Lista de compras

Tudo o que foi usado nesta montagem:

<div class="shop-grid">
  <div class="shop-item">
    <span class="shop-emoji">🎚️</span>
    <strong>Sonoff 4CH Pro R3</strong>
    <p>Relé Wi-Fi + RF de 4 canais com contactos secos — o cérebro do sistema.</p>
    <a href="https://amazon.es/-/pt/dp/B08DKW3JV5" target="_blank" rel="noopener">Ver na Amazon →</a>
  </div>
  <div class="shop-item">
    <span class="shop-emoji">⚡</span>
    <strong>Transformador de 24 VAC</strong>
    <p>Baixa os 230 V da rede para os 24 VAC que os solenoides das válvulas esperam.</p>
    <a href="https://amazon.es/-/pt/dp/B0C19ND3TY" target="_blank" rel="noopener">Ver na Amazon →</a>
  </div>
  <div class="shop-item">
    <span class="shop-emoji">📦</span>
    <strong>Caixa estanque (IP65)</strong>
    <p>Aloja o Sonoff e o transformador no exterior, faça chuva ou faça sol.</p>
    <a href="https://amazon.es/-/pt/dp/B0DJ2H6RK2" target="_blank" rel="noopener">Ver na Amazon →</a>
  </div>
  <div class="shop-item">
    <span class="shop-emoji">💧</span>
    <strong>Electroválvulas Rain Bird ×4</strong>
    <p>Uma por zona — o cavalo de batalha do setor para abrir e fechar linhas de água.</p>
    <a href="https://www.leroymerlin.pt/produtos/electrovalvula-rainbird-91457189.html" target="_blank" rel="noopener">Ver no Leroy Merlin →</a>
  </div>
</div>

<div class="shop-extras">🧰 <strong>Extras aborrecidos, mas essenciais:</strong> uma caixa de válvulas enterrada, uma válvula de esfera manual para a entrada de água, conectores estanques com gel e cabo exterior de dois condutores para ligar a caixa às válvulas.</div>

<div class="divider">💧 💧 💧</div>

## ⚡ A lógica elétrica

Os solenoides Rain Bird são de 24 VAC, por isso o transformador baixa os 230 V da rede para os 24 VAC que as válvulas esperam — a configuração padrão em rega. O Sonoff nunca alimenta as válvulas diretamente; os seus relés de contacto seco ficam simplesmente a meio do circuito de 24 VAC, como interruptores de luz. Uma vantagem da corrente alternada: não há polaridade para trocar nas ligações das válvulas — os dois fios do solenoide são intercambiáveis.

<div class="callout">💡 Regue uma zona de cada vez (o Sonoff tem um modo interlock precisamente para isto). A pressão da água mantém-se utilizável e o transformador só tem de alimentar a corrente de um solenoide de cada vez.</div>

<div class="divider">💧 💧 💧</div>

## 🔌 As ligações

Aqui está o esquema, exatamente como foi instalado:

<figure>
  <img src="/assets/blog/irrigation/schema.png" alt="Esquema de ligações — Sonoff 4CH Pro R3 a comutar 24 VAC para quatro zonas de válvulas">
  <figcaption>O esquema de ligações — um transformador, quatro relés de contacto seco, quatro zonas.</figcaption>
</figure>

```
230 V rede ───┬── Sonoff 4CH Pro R3  [Input 100–240V: N, L]
              │
              └── Transformador 24 VAC  [Primário: N, L]
                        │
                  Secundário A ─── COM R1 ── COM R2 ── COM R3 ── COM R4
                                    (comuns em cadeia)

   R1 NO ── fio do solenoide zona 1 ─┐
   R2 NO ── fio do solenoide zona 2 ─┤
   R3 NO ── fio do solenoide zona 3 ─┼── todos os fios de retorno ── Secundário B
   R4 NO ── fio do solenoide zona 4 ─┘
```

Passo a passo:

1. **Entrada da rede.** Leve os 230 V para dentro da caixa estanque e divida-os em dois: para os terminais N/L de `Input 100–240V` do Sonoff (a placa alimenta-se diretamente da rede) e para o primário do transformador.
2. **Uma perna do secundário para os relés.** Leve um dos fios do secundário de 24 VAC do transformador ao terminal `COM` do relé 1 e depois ligue-o em cadeia ao `COM` dos relés 2, 3 e 4 com pontes curtas.
3. **Um fio por zona.** A partir do terminal `NO` (normalmente aberto) de cada relé, leve um condutor até à caixa de válvulas — um por zona.
4. **Retorno comum.** Na caixa de válvulas, junte o segundo fio de todos os solenoides e leve um único condutor de retorno até à outra perna do secundário do transformador.
5. **Lado da canalização.** As válvulas ficam numa caixa de válvulas enterrada: primeiro a válvula de esfera manual (para poder cortar a água em manutenções), depois as válvulas Rain Bird em linha. Use conectores estanques com gel em todas as emendas — esta caixa inunda, é para isso que serve. Uma camada de gravilha ou seixos no fundo mantém os acessórios fora da lama e deixa a água escoar.

<figure>
  <img src="/assets/blog/irrigation/enclosure-closed.jpeg" alt="A caixa concluída, montada na parede">
  <figcaption>A caixa concluída — IP65, montada na parede, com o LED de Wi-Fi aceso.</figcaption>
</figure>

<figure>
  <img src="/assets/blog/irrigation/enclosure-inside.jpeg" alt="Dentro da caixa — transformador de 24 VAC em cima, Sonoff 4CH Pro R3 em baixo">
  <figcaption>Por dentro: transformador de 24 VAC em cima, Sonoff 4CH Pro R3 em baixo.</figcaption>
</figure>

<figure>
  <img src="/assets/blog/irrigation/valve-box.jpeg" alt="A caixa de válvulas — válvulas Rain Bird, corte manual e emendas estanques">
  <figcaption>A caixa de válvulas — válvulas Rain Bird, corte manual, emendas estanques e seixos para drenagem.</figcaption>
</figure>

<div class="callout">⚠️ Esta montagem envolve ligações à rede de 230 V. Desligue o disjuntor antes de tocar em qualquer coisa, proteja a instalação com um diferencial e, se alguma parte desta frase lhe soou estranha, peça a um eletricista para tratar do lado da rede. A metade de 24 V é segura para experimentar; a metade de 230 V não é sítio para aprender.</div>

<div class="divider">💧 💧 💧</div>

## 📱 Configuração

Emparelhe primeiro o Sonoff com a app eWeLink; depois, há duas definições que importam:

**Modo interlock.** Ative-o para que só um relé possa estar ligado de cada vez — uma zona a regar de cada vez, pressão total, transformador sem sobrecarga.

**Home Assistant.** Integre a placa no Home Assistant e crie os horários aí, em vez de na app. O nosso rega ao amanhecer, salta automaticamente os dias de chuva e reporta cada rega no resumo noturno — experimente pedir isso a um controlador comprado numa loja.

<div class="divider">💧 💧 💧</div>

## 🌱 O que lhe diríamos antes de começar

O sistema completo ficou bem abaixo dos 100 € e funcionou o verão inteiro sem um soluço. Se o fizéssemos outra vez: compre os conectores estanques antes de achar que precisa deles, identifique as duas pontas de cada fio de zona no momento em que o puxa e ponha a caixa num sítio onde consiga mesmo chegar — mais cedo ou mais tarde, vai estar ali de multímetro na mão.

Quer um sistema de rega inteligente — ou uma casa inteligente completa — sem ter de abrir as valas? [Fazemos instalações](/pt/#contact), com esquema e tudo.
