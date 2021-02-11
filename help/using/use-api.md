---
title: API de uso do HTL
description: Duas APIs estão disponíveis para HTL - API de uso de Java e API de uso de Javascript
translation-type: tm+mt
source-git-commit: f7e46aaac2a4b51d7fa131ef46692ba6be58d878
workflow-type: tm+mt
source-wordcount: '183'
ht-degree: 8%

---


# API de uso do HTL {#htl-use-api}

O HTL estimula a separação de preocupações ao não permitir que a lógica comercial se misture com a marcação. A lógica comercial pode ser implementada por meio da API de uso.

A tabela a seguir apresenta uma visão geral das vantagens e desvantagens de cada API.

|  | **Java Use-API** | **JavaScript Use-API** |
|--- |--- |--- |
| **Vantagens** | <ul><li>Mais rápido</li><li>Pode ser inspecionado com um depurador</li><li>Teste fácil de unidade</li></ul> | <ul><li>Pode ser modificado por desenvolvedores front-end</li><li>Está localizado dentro do componente, mantendo a lógica de visualização de um componente próxima ao modelo correspondente</li></ul> |
| **Desvantagens** | <ul><li>Não pode ser modificado por desenvolvedores front-end</li></ul> | <ul><li>Mais lento</li><li>Nenhum depurador (ainda)</li><li>Teste de unidade mais difícil</li></ul> |

Para componentes de página, é recomendável usar um modelo misto, em que toda a lógica do modelo está localizada em Java, fornecendo APIs claras agnósticas para qualquer coisa que acontece na visualização (ou seja, dentro dos componentes). AEM vem com grandes modelos padrão, como a Página ou a API de recursos, que devem ser capazes de abranger a maioria dos casos.

Toda lógica de visualização específica para um componente deve ser colocada dentro desse componente como JavaScript, pois pertence a esse componente.
