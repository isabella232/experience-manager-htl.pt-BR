---
title: Objetos globais do HTL
description: Saiba mais sobre objetos enumeráveis, objetos com suporte de Java e objetos com suporte de JavaScript no HTL.
exl-id: ca590b92-f1b3-4e44-a04a-a2c10dff256f
source-git-commit: 88edbd2fd66de960460df5928a3b42846d32066b
workflow-type: tm+mt
source-wordcount: '200'
ht-degree: 100%

---


# Objetos globais do HTL {#htl-global-objects}

Sem precisar especificar nada, o HTL fornece acesso a muitos objetos úteis para o desenvolvedor. Esses objetos estão além de qualquer um que possa ser introduzido por meio da [API de uso.](java-use-api.md)

>[!NOTE]
>
>Para desenvolvedores familiarizados com o desenvolvimento em JSP no AEM, o HTL fornece acesso a todos os objetos que normalmente eram disponibilizados no JSP depois de incluir o `global.jsp`.

## Objetos enumeráveis {#enumerable-objects}

Esses objetos fornecem acesso conveniente a informações de uso comum. Seu conteúdo pode ser acessado com a notação de pontos e iterado usando `data-sly-list` ou `data-sly-repeat`.

| Nome da variável | Descrição | Com suporte de |
|--- |--- |--- |
| `properties` | Lista de propriedades do recurso atual | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `pageProperties` | Lista de propriedades de página da página atual | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `inheritedPageProperties` | Lista de propriedades de página herdadas da página atual | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |

## Objetos com suporte de Java {#java-backed-objects}

Cada um dos seguintes objetos tem suporte do objeto Java correspondente.

| Nome da variável | Descrição |
|---|---|
| `component` | `com.day.cq.wcm.api.components.Component` |
| `componentContext` | `com.day.cq.wcm.api.components.ComponentContext` |
| `currentContentPolicy` | `com.day.cq.wcm.api.policies.ContentPolicy` |
| `currentContentPolicyProperties` | `com.day.cq.wcm.api.policies.ContentPolicy` |
| `currentDesign` | `com.day.cq.wcm.api.designer.Design` |
| `currentNode` | `javax.jcr.Node` |
| `currentPage` | `com.day.cq.wcm.api.Page` |
| `currentSession` | `javax.servlet.http.HttpSession` |
| `currentStyle` | `com.day.cq.wcm.api.designer.Style` |
| `designer` | `com.day.cq.wcm.api.designer.Designer` |
| `editContext` | `com.day.cq.wcm.api.components.EditContext` |
| `log` | `org.slf4j.Logger` |
| `out` | `java.io.PrintWriter` |
| `pageManager` | `com.day.cq.wcm.api.PageManager` |
| `reader` | `java.io.BufferedReader` |
| `request` | `org.apache.sling.api.SlingHttpServletRequest` |
| `resolver` | `org.apache.sling.api.resource.ResourceResolver` |
| `resource` | `org.apache.sling.api.resource.Resource` |
| `resourceDesign` | `com.day.cq.wcm.api.designer.Design` |
| `resourcePage` | `com.day.cq.wcm.api.Page` |
| `response` | `org.apache.sling.api.SlingHttpServletResponse` |
| `sling` | `org.apache.sling.api.scripting.SlingScriptHelper` |
| `slyWcmHelper` | `com.adobe.cq.sightly.WCMScriptHelper` |
| `wcmmode` | `com.adobe.cq.sightly.SightlyWCMMode` |
| `xssAPI` | `com.adobe.granite.xss.XSSAPI` |

## Objetos com suporte de JavaScript {#javascript-backed-objects}

É possível dar suporte à lógica HTL com JavaScript. No entanto, o método preferido ou recomendado é usando [Modelos Sling.](https://sling.apache.org/documentation/bundles/models.html)
