---
title: API de uso do HTL
description: Duas APIs estão disponíveis para HTL - API de uso de Java e API de uso de JavaScript
exl-id: 8f95707e-952c-4efe-a44e-9a1ad501605e
source-git-commit: 8e70ee4921a7ea071ab7e06947824c371f4013d8
workflow-type: ht
source-wordcount: '183'
ht-degree: 100%

---

# API de uso do HTL {#htl-use-api}

O HTL estimula a separação de interesses por não permitir que a lógica de negócios se misture com a marcação. A lógica de negócios pode ser implementada por meio da API de uso.

A tabela a seguir fornece uma visão geral das vantagens e desvantagens de cada API.

|  | **API de uso do Java** | **API de uso do JavaScript** |
|--- |--- |--- |
| **Vantagens** | <ul><li>Mais rápido</li><li>Pode ser inspecionado com um depurador</li><li>Teste de unidade facilitado</li></ul> | <ul><li>Pode ser modificado por desenvolvedores de front-end</li><li>Está localizado no componente, mantendo a lógica de exibição de um componente próxima ao modelo correspondente</li></ul> |
| **Desvantagens** | <ul><li>Não pode ser modificado por desenvolvedores de front-end</li></ul> | <ul><li>Mais lento</li><li>Nenhum depurador (ainda)</li><li>Teste de unidade difícil</li></ul> |

Para componentes de página, é recomendável usar um modelo misto, em que toda a lógica do modelo esteja localizada em Java, fornecendo APIs claras e agnósticas para qualquer coisa que aconteça na exibição (ou seja, dentro dos componentes). O AEM vem com excelentes modelos padrão, como a Página ou a API de recursos, que devem ser capazes de abranger a maioria dos casos.

Toda lógica de visualização específica de um componente deve ser colocada dentro dele como JavaScript, pois pertence a esse componente.
