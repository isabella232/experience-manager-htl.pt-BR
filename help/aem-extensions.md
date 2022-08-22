---
title: Extensões de AEM
description: AEM oferece extensões da especificação HTL para AEM para sua conveniência como desenvolvedor.
source-git-commit: 6d97bc5d0ab89dffaf56a54c73c94d069bb31ca6
workflow-type: tm+mt
source-wordcount: '308'
ht-degree: 0%

---


# Extensões de AEM {#aem-extensions}

Semelhante ao [Extensões do Apache Sling da especificação do HTL,](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#extensions-of-the-htl-specification-1) AEM oferece algumas opções de expressão adicionais que facilitam o trabalho com AEM conceitos diretamente nos scripts HTL.

## i18n {#i18n}

O mesmo [três opções adicionais](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#i18n) como no Apache Sling pode ser usado junto com `i18n`:

* `locale`
* `hint`
* `basename`

No entanto, em AEM, a variável [apoio à internacionalização](https://experienceleague.adobe.com/docs/experience-manager-65/developing/components/internationalization/i18n-dev.html) O para HTL é implementado com a ajuda da API do `com.day.cq.i18n` pacote.

## inclusão de sly de dados {#data-sly-include}

Em AEM, `data-sly-include` pode receber um `wcmmode` que controlam a [Modo WCM](https://developer.adobe.com/experience-manager/reference-materials/cloud-service/javadoc/com/day/cq/wcm/api/WCMMode.html) para o script incluído. Os valores permitidos são os nomes das constantes enum disponíveis.

## data-sly-resource {#data-sly-resource}

Além dos caminhos e `Resources`, o `data-sly-resource` o elemento de bloco também pode funcionar com [`Maps`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Map.html) ou [`Records`.](https://github.com/apache/sling-org-apache-sling-scripting-sightly-runtime/blob/master/src/main/java/org/apache/sling/scripting/sightly/Record.java) Com ambas as abordagens, a variável `resourceName` A propriedade String deve ser fornecida. Seu valor é usado para criar um [Recurso Sintético](https://www.javadoc.io/doc/org.apache.sling/org.apache.sling.api/latest/org/apache/sling/api/resource/SyntheticResource.html) que será incluído no contexto de renderização. O restante das propriedades do `Record` ou `Map` que foi passada para `data-sly-resource` será usado como normal `Resource` propriedades. Se a variável `sling:resourceType` estiver ausente neste mapa, o tipo de recurso será considerado como sendo o valor de `resourceType` [opção de expressão](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#229-resource) ou o tipo de recurso do recurso atual que direciona a renderização.

Considerando as seguintes propriedades de mapa/registro disponíveis no escopo do script como `map`:

```javascript
{
    resourceName: "myText",
    "sling:resourceType": "core/wcm/components/text/v2/text",
    "text": "Hello World!"
}
```

E considerando a seguinte marcação:

```html
<div class="outer" data-sly-resource="${map}"></div>
```

A seguinte saída é esperada:

```html
<div class="outer">
    <div class="myText">
        <div data-cmp-data-layer="{&quot;text-e58d65c472&quot;:{&quot;@type&quot;:&quot;core/wcm/components/text/v2/text&quot;,&quot;xdm:text&quot;:&quot;<p>Hello world!</p>&quot;}}" id="text-e58d65c472" class="cmp-text">
            <p>Hello world!</p>
        </div>
  </div>
</div>
```
