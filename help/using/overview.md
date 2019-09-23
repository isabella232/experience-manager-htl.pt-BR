---
title: Visão geral do AEM HTL
seo-title: Visão geral da documentação técnica do AEM HTL.
description: A finalidade do HTL suportado pelo AEM é oferecer uma estrutura Web de nível empresarial altamente produtiva que aumenta a segurança e permite que desenvolvedores de HTML sem conhecimento de Java participem melhor em projetos do AEM.
seo-description: Este documento apresenta os princípios e a finalidade da Linguagem de modelo HTML - HTL - suportada pelo Adobe Experience Manager. HTL é uma estrutura Web altamente produtiva de nível empresarial que aumenta a segurança e permite que desenvolvedores HTML sem conhecimento Java participem melhor em projetos do AEM.
uuid: 8f486325-0a1b-4186-a998-96fc0034c44a
contentOwner: Usuário
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: introdução
content-type: referência
discoiquuid: 8f779e08-94c7-43bc-a6e5-d81a9f818c5c
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 1d4565a4cffa6e5d42d6a5242f7ce62203dc7c63

---


# Visão geral {#overview}

A finalidade da Linguagem de modelo HTML (HTL), suportada pelo Adobe Experience Manager (AEM), é oferecer uma estrutura da Web altamente produtiva em nível corporativo que aumenta a segurança e permite que desenvolvedores de HTML sem conhecimento Java participem melhor em projetos do AEM.

A Linguagem de modelo HTML foi introduzida com o AEM 6.0 e substitui o JSP (JavaServer Pages) como o sistema de modelo preferencial e recomendado do lado do servidor para HTML. Para desenvolvedores da Web que precisam criar sites corporativos robustos, a Linguagem de modelo HTML ajuda a aumentar a segurança e a eficiência do desenvolvimento.

## Maior segurança {#increased-security}

A Linguagem de modelo HTML aumenta a segurança dos sites que a usam em sua implementação, em comparação ao JSP e à maioria dos outros sistemas de modelo, porque o HTL é capaz de aplicar automaticamente a escape sensível ao contexto a todas as variáveis que são geradas na camada de apresentação. O HTL torna isso possível porque entende a sintaxe HTML e usa esse conhecimento para ajustar o escape necessário para expressões, com base em sua posição na marcação. Isso resultará, por exemplo, em expressões colocadas em `href` `src` ou atributos para serem evitadas de forma diferente das expressões colocadas em outros atributos ou em outros locais.

Embora o mesmo resultado possa ser obtido com linguagens de modelo como o JSP, o desenvolvedor deve garantir manualmente que o escape adequado seja aplicado a cada variável. Como uma única omissão ou erro no escape aplicado é potencialmente suficiente para causar uma vulnerabilidade de script entre sites (XSS), decidimos automatizar essa tarefa com HTL. Se necessário, os desenvolvedores ainda podem especificar uma fuga diferente nas expressões, mas com HTL, o comportamento padrão é muito mais provável de corresponder ao comportamento desejado, reduzindo a probabilidade de erros.

## Desenvolvimento simplificado {#simplified-development}

A Linguagem de modelo HTML é fácil de aprender e seus recursos são propositadamente limitados para garantir que permaneça simples e direto. Também tem mecanismos poderosos para estruturar a marcação e invocar a lógica, ao mesmo tempo que impõe sempre a separação rigorosa das preocupações entre marcação e lógica. O próprio HTL é HTML5 padrão, pois usa expressões e atributos de dados para anotar a marcação com o comportamento dinâmico desejado, o que significa que ela não quebra a validade da marcação e a mantém legível. Observe que a avaliação das expressões e dos atributos de dados é feita inteiramente no lado do servidor e não estará visível no lado do cliente, onde qualquer estrutura JavaScript desejada pode ser usada sem interferir.

Esses recursos permitem que desenvolvedores HTML sem conhecimento Java e com pouco conhecimento específico do produto possam editar modelos HTL, permitindo que eles façam parte da equipe de desenvolvimento e simplificando a colaboração com desenvolvedores Java de pilha completa. E vice-versa isso permite que os desenvolvedores Java se concentrem no código back-end sem se preocupar com HTML.

## Custos reduzidos {#reduced-costs}

Maior segurança, desenvolvimento simplificado e melhor colaboração entre equipes, traduz-se para projetos AEM em menor esforço, tempo de entrada no mercado (TTM) mais rápido e menor custo total de propriedade (TCO).

Concretamente, a partir do que foi observado ao reimplementar o site Adobe.com com com a Linguagem de modelo HTML, o custo e a duração do projeto podem ser reduzidos em cerca de 25%.

![](assets/chlimage_1.png)

O diagrama acima mostra as seguintes melhorias na eficiência potencialmente possíveis pelo HTL:

* **** HTML / CSS / JS: Como os desenvolvedores de HTML são capazes de editar diretamente modelos HTL, os designs front-end não precisam mais ser implementados separadamente do projeto AEM, mas podem ser implementados diretamente nos componentes reais do AEM. Isso reduz iterações dolorosas com desenvolvedores Java completos.
* **** JSP / HTL: Como o próprio HTL não requer conhecimento Java e é direto para escrever, qualquer desenvolvedor com conhecimento em HTML tem poder para editar os modelos.
* **** Java: Graças à API de uso clara e simples fornecida pela HTL, a interface com a lógica comercial é esclarecida, o que também beneficia o desenvolvimento do Java em geral.

**Leia a seguir:**

* [Introdução à linguagem de modelo HTML](getting-started.md)

