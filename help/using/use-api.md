---
title: HTL Use-API
seo-title: Adobe HTL Use-API
description: Duas APIs estão disponíveis para HTL - API de uso de Java e API de uso
  de Javascript
seo-description: Duas APIs estão disponíveis para Adobe HTL - API de uso de Java e
  API de uso de Javascript
uuid: ab 44 aa 5 c-ce 7 e -40 b 9-97 fb-e 86 c 6 a 28405 c
contentOwner: Usuário
products: SG_ EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: referência
discoiquuid: 89004426-eb 59-4 b 63-913 f -51 bf 98662773
mwpw-migration-script-version: 2017-10-12 T 21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 796c55d3d85e6b5a3efaa5c04a25be1b0b4e54dd

---


# HTL Use-API {#htl-use-api}

A tabela a seguir apresenta uma visão geral dos prós e contras de cada API.

|  | **API de uso Java** | **Javascript Use-API** |
|--- |--- |--- |
| **Vantagens** | <ul><li>mais rápido</li><li>pode ser inspecionado com um depurador</li><li>fácil de segmentar</li></ul> | <ul><li>pode ser modificado por desenvolvedores front-end</li><li>está localizado no componente, mantendo a lógica de visualização de um componente próximo ao modelo correspondente</li></ul> |
| **Desvantagens** | <ul><li>não pode ser modificado por desenvolvedores front-end</li></ul> | <ul><li>mais lento</li><li>sem depurador (ainda)</li><li>harder-test</li></ul> |


Para componentes de página, é recomendável usar um modelo misto, onde toda a lógica do modelo está localizada em Java, fornecendo apis claras que são agnósticos a qualquer coisa que ocorre na visualização (isto é, dentro dos componentes). O AEM vem com excelentes modelos padrão, como a Página ou a API de recursos, que deve ser capaz de abranger a maioria dos casos.

Toda lógica de exibição específica para um componente deve ser colocada dentro desse componente como javascript, pois pertence a esse componente.
