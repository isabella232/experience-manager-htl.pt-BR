---
title: HTL Use-API
seo-title: Adobe HTL Use-API
description: Duas APIs estão disponíveis para HTL - API de uso de Java e API de uso de Javascript
seo-description: Duas APIs estão disponíveis para Adobe HTL - API de uso de Java e API de uso de Javascript
uuid: ab44aa5c-ce7e-40b9-97fb-e86c6a28405c
contentOwner: Usuário
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: referência
discoiquuid: 89004426-eb59-4b63-913f-51bf9862773
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 5cbaf9c747acf748d12559c2c8e3aba4600cf9a4

---


# HTL Use-API {#htl-use-api}

A tabela a seguir fornece uma visão geral dos prós e contras de cada API.

|  | **Java Use-API** | **JavaScript Use-API** |
|--- |--- |--- |
| **Prós** | <ul><li>mais rápido</li><li>pode ser inspecionado com um depurador</li><li>teste fácil de unidade</li></ul> | <ul><li>podem ser modificados por desenvolvedores front-end</li><li>está localizado no componente, mantendo a lógica de exibição de um componente próxima ao modelo correspondente</li></ul> |
| **Cons** | <ul><li>não pode ser modificado por desenvolvedores front-end</li></ul> | <ul><li>slower</li><li>no debugger (yet)</li><li>harder to unit-test</li></ul> |


For page components, it is recommended to use a mixed model, where all model logic is located in Java, providing clear APIs that are agnostic to anything that happens in the view (i.e. within the components). AEM comes with great default models like the Page or the Resource API that should be able to cover most cases.

All view logic that is specific to a component should be placed within that component as JavaScript, because it belongs to that component.
