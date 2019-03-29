---
title: Visão geral
seo-title: Visão geral
description: A finalidade do HTL com suporte pelo AEM é oferecer uma estrutura da
  Web altamente produtiva que aumente a segurança e permite que desenvolvedores HTML
  sem conhecimento Java participem melhor dos projetos do AEM.
seo-description: A finalidade do HTML Template Language - HTL - suportado pelo Adobe
  Experience Manager, é oferecer uma estrutura da Web em nível empresarial altamente
  produtiva que aumente a segurança e permite que desenvolvedores HTML sem conhecimento
  Java participem melhor dos projetos do AEM.
uuid: 8 f 486325-0 a 1 b -4186-a 998-96 fc 0034 c 44 a
contentOwner: Usuário
products: SG_ EXPERIENCEMANAGER/HTL
topic-tags: introdução
content-type: referência
discoiquuid: 8 f 779 e 08-94 c 7-43 bc-a 6 e 5-d 81 a 9 f 818 c 5 c
mwpw-migration-script-version: 2017-10-12 T 21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 796c55d3d85e6b5a3efaa5c04a25be1b0b4e54dd

---


# Visão geral {#overview}

A finalidade do HTML Template Language (HTL), compatível com o Adobe Experience Manager (AEM), é oferecer uma estrutura da Web em nível empresarial altamente produtiva que aumente a segurança e permite que desenvolvedores HTML sem conhecimento Java participem melhor dos projetos do AEM.

O Idioma do modelo HTML foi introduzido com o AEM 6.0 e assume o lugar do JSP (javaserver Pages) como o sistema de modelo de modelo de HTML preferido e recomendado. Para desenvolvedores da Web que precisam criar sites corporativos robustos, o Linguagem de modelo HTML ajuda a aumentar a segurança e a eficiência de desenvolvimento.

## Maior segurança {#increased-security}

O Linguagem de modelo HTML aumenta a segurança dos sites que o utilizam em sua implementação, em comparação ao JSP e à maioria dos sistemas de modelo, pois a HTL é capaz de aplicar automaticamente o escape apropriado sensível ao contexto para todas as variáveis que estão sendo geradas para a camada de apresentação. O HTL torna isso possível porque entende a sintaxe HTML e usa esse conhecimento para ajustar o escape necessário para expressões, com base em sua posição na marcação. Isso, por exemplo, resultará em expressões inseridas `href` ou `src` atributos de forma diferente das expressões colocadas em outros atributos, ou em outro lugar.

Embora o mesmo resultado possa ser obtido com idiomas de modelo como JSP, o desenvolvedor deve garantir manualmente que o escape apropriado seja aplicado a cada variável. Como uma única omissão ou erro no escape aplicado é potencialmente suficiente para causar uma vulnerabilidade de script entre sites (XSS), decidimos automatizar essa tarefa com HTL. Se necessário, os desenvolvedores ainda podem especificar um escape diferente das expressões, mas com HTL o comportamento padrão é muito mais provável de corresponder ao comportamento desejado, reduzindo a probabilidade de erros.

## Desenvolvimento simplificado {#simplified-development}

O Linguagem de modelo HTML é fácil de aprender e seus recursos são limitados de forma remota para garantir que fique simples e direto. Ela também tem mecanismos avançados para estruturar a marcação e invocar a lógica, ao mesmo tempo sempre impor separação estrita de preocupações entre marcação e lógica. HTL propriamente dito é HTML 5 padrão, pois usa expressões e atributos de dados para anotar a marcação com o comportamento dinâmico desejado, o que significa que não quebra a validade da marcação e o mantém legível. Observe que a avaliação das expressões e atributos de dados é feita totalmente no lado do servidor e não estará visível no lado do cliente, onde qualquer estrutura do javascript desejada pode ser usada sem interagir.

Esses recursos permitem desenvolvedores HTML sem conhecimento do Java e com pouca conhecimento específico do produto para editar modelos HTL, permitindo que sejam parte da equipe de desenvolvimento e simplifiquem a colaboração com os desenvolvedores Java completos de pilha. E vice-versa isso permite que os desenvolvedores do Java se concentrem no código de back-end sem se preocupar com o HTML.

## Custos reduzidos {#reduced-costs}

Maior segurança, desenvolvimento simplificado e colaboração de equipe aprimorada, traduz-se para projetos do AEM em esforços reduzidos, tempo mais rápido para o marketing (TTM) e custo total de apropriação (TCO).

Concretamente, do que foi observado ao reimplementar o site Adobe.com com o Linguagem modelo HTML é que o custo e a duração do projeto poderiam ser reduzidos em cerca de 25%.

![](assets/chlimage_1.png)

O diagrama acima mostra as seguintes melhorias na eficiência potencialmente possíveis pelo HTL:

* **HTML/CSS/JS:** Como os desenvolvedores HTML podem editar diretamente modelos HTL, os designs front-end não precisam ser implementados separadamente do projeto do AEM, mas podem ser implementados diretamente nos componentes do AEM reais. Isso reduz iterações complexas com desenvolvedores de Java de pilha completa.
* **JSP/HTL:** Como o HTL propriamente dito não requer nenhum conhecimento Java e é direto para gravação, qualquer desenvolvedor com experiência em HTML é capacitado para editar os modelos.
* **Java:** Graças à simples e simples de usar Usar a API fornecida pelo HTL, a interface com a lógica comercial é esclarecida, o que também beneficia o desenvolvimento em Java geral.

**Ler em seguida:**

* [Introdução ao idioma do modelo HTML](getting-started.md)

