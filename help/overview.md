---
title: Visão geral do HTL
description: Saiba como o AEM oferece suporte à Linguagem de modelo HTML (HTL) para entregar uma estrutura Web produtiva de nível empresarial, aumentando a segurança e permitindo que desenvolvedores de HTML sem conhecimento em Java tenham uma melhor participação em projetos do AEM.
exl-id: 5d06ff25-d681-4b95-8375-c28a8364eb7e
source-git-commit: 5ab1275c984135fe946f36905bbc979cf19edd80
workflow-type: tm+mt
source-wordcount: '711'
ht-degree: 100%

---


# Visão geral {#overview}

A finalidade da Linguagem de modelo HTML (HTL) compatível com o Adobe Experience Manager (AEM) é oferecer uma estrutura corporativa da Web altamente produtiva que aumenta a segurança e permite que desenvolvedores de HTML sem conhecimento em Java tenham uma melhor participação nos projetos AEM.

[Introduzida no AEM 6.0](history.md), a Linguagem de modelo HTML é o sistema de modelo do lado do servidor preferencial e recomendado para HTML no AEM. Para desenvolvedores da Web que precisam criar sites corporativos robustos, a Linguagem de modelo HTML ajuda a aumentar a segurança e a eficiência do desenvolvimento.

## Maior segurança {#increased-security}

A Linguagem de modelo HTML aumenta a segurança dos sites que a usam em sua implementação, em comparação à maioria dos outros sistemas de modelo, porque o HTL é capaz de aplicar automaticamente o escape com reconhecimento de contexto a todas as variáveis que são geradas na camada de apresentação. O HTL torna isso possível porque entende a sintaxe HTML e usa esse conhecimento para ajustar o escape necessário para expressões, com base em sua posição na marcação. Isso resultará, por exemplo, em expressões colocadas em atributos `href` ou `src` sendo escapadas de maneira diferente das expressões colocadas em outros atributos ou locais.

Embora o mesmo resultado possa ser obtido com linguagens de modelo como o JSP, o desenvolvedor deve garantir manualmente que o escape adequado seja aplicado a cada variável. Como uma única omissão ou erro no escape aplicado possivelmente é suficiente para causar uma vulnerabilidade na criação de script entre sites (XSS), decidimos automatizar essa tarefa com HTL. Se necessário, os desenvolvedores ainda podem especificar um escape diferente nas expressões, mas com HTL, o comportamento padrão está muito mais propenso a corresponder ao comportamento desejado, reduzindo a probabilidade de erros.

## Desenvolvimento simplificado {#simplified-development}

A Linguagem de modelo HTML é fácil de aprender e seus recursos são limitados de propósito para garantir que permaneça simples e direta. Ela também tem mecanismos poderosos para estruturar a marcação e chamar a lógica, ao mesmo tempo que impõe sempre a separação rigorosa das preocupações entre marcação e lógica. O HTL em si é o HTML5 padrão, pois usa expressões e atributos de dados para anotar a marcação com o comportamento dinâmico desejado, o que significa que não anula a validade da marcação e a mantém legível. Observe que a avaliação das expressões e dos atributos de dados é feita inteiramente no lado do servidor e não estará visível no lado do cliente, onde qualquer estrutura JavaScript desejada pode ser usada sem interferência.

Esses recursos permitem que desenvolvedores HTML sem conhecimento em Java e com pouco conhecimento específico do produto possam editar modelos HTL, permitindo que façam parte da equipe de desenvolvimento e simplificando a colaboração com desenvolvedores Java de pilha completa. E por outro lado isso permite que os desenvolvedores Java se concentrem no código back-end sem se preocuparem com HTML.

## Menor custo {#reduced-costs}

Para projetos do AEM, maior segurança, desenvolvimento simplificado e melhor colaboração em equipe resultam em menos esforço, um time-to-market (TTM) mais rápido e menor custo total de propriedade (TCO).

Especificamente, a partir do que foi observado ao reimplementar o site Adobe.com com a Linguagem de modelo HTML, o custo e a duração do projeto podem ser reduzidos em cerca de 25%.

![Aumente a eficiência e diminua os custos](assets/chlimage_1.png)

O diagrama acima mostra que possivelmente as seguintes melhorias na eficiência foram viabilizadas pelo HTL:

* **HTML / CSS / JS:** como os desenvolvedores de HTML são capazes de editar diretamente os modelos HTL, os designs de front-end não precisam mais ser implementados separadamente do projeto AEM, mas podem ser implementados diretamente nos componentes reais do AEM. Isso reduz iterações negativas com desenvolvedores Java de pilha completa.
* **JSP / HTL:** como o HTL em si não requer conhecimento em Java e tem gravação direta, qualquer desenvolvedor com conhecimento em HTML pode editar os modelos.
* **Java:** graças à API de uso clara e simples de usar fornecida pelo HTL, a interface com a lógica comercial é esclarecida, o que também beneficia o desenvolvimento do Java em geral.

## Vídeo de introdução {#video}

O vídeo a seguir, retirado de uma [sessão do AEM Gems](https://experienceleague.adobe.com/docs/experience-manager-gems-events/gems/gems2014/aem-introduction-to-htl.html?lang=pt-BR), fornece uma visão geral da finalidade do HTL, bem como exemplos de implementação.

>[!VIDEO](https://video.tv.adobe.com/v/19504/?quality=9)

Observe que o vídeo se refere ao HTL por [seu antigo nome, Sightly.](history.md)

## Próximas etapas {#next-steps}

Agora que você conhece os objetivos e as vantagens do HTL, comece a usar essa linguagem revisando o documento [Introdução à Linguagem de modelo HTML.](getting-started.md)
