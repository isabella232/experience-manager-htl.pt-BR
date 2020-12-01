---
title: API de uso do JavaScript do HTL
description: A Linguagem do modelo HTML - HTL - API de uso do JavaScript permite que um arquivo HTL acesse o código auxiliar escrito em JavaScript.
translation-type: tm+mt
source-git-commit: ee712ef61018b5e05ea052484e2a9a6b12e6c5c8
workflow-type: tm+mt
source-wordcount: '324'
ht-degree: 2%

---


# API de uso do JavaScript do HTL {#htl-javascript-use-api}

A HTML Template Language (HTL) JavaScript Use-API permite que um arquivo HTL acesse o código auxiliar escrito em JavaScript. Isso permite que toda a lógica comercial complexa seja encapsulada no código JavaScript, enquanto o código HTL trata somente da produção de marcação direta.

As seguintes convenções são usadas.

```javascript
/**
 * In the following example '/libs/dep1.js' and 'dep2.js' are optional
 * dependencies needed for this script's execution. Dependencies can
 * be specified using an absolute path or a relative path to this
 * script's own path.
 *
 * If no dependencies are needed the dependencies array can be omitted.
 */
use(['dep1.js', 'dep2.js'], function (Dep1, Dep2) {
    // implement processing
  
    // define this Use object's behavior
    return {
        propertyName: propertyValue
        functionName: function () {}
    }
});
```

## Um exemplo simples {#a-simple-example}

Definimos um componente, `info`, localizado em

`/apps/my-example/components/info`

Ele contém dois arquivos:

* **`info.js`**: um arquivo JavaScript que define a classe de uso.
* **`info.html`**: um arquivo HTL que define o componente  `info`. Esse código usará a funcionalidade de `info.js` por meio da API de uso.

### /apps/my-example/component/info/info.js {#apps-my-example-component-info-info-js}

```java
"use strict";
use(function () {
    var info = {};
    info.title = resource.properties["title"];
    info.description = resource.properties["description"];
    return info;
});
```

### /apps/my-example/component/info/info.html {#apps-my-example-component-info-info-html}

```xml
<div data-sly-use.info="info.js">
    <h1>${info.title}</h1>
    <p>${info.description}</p>
</div>
```

Também criamos um nó de conteúdo que usa o componente `info` em

`/content/my-example`, com propriedades:

* `sling:resourceType = "my-example/component/info"`
* `title = "My Example"`
* `description = "This is some example content."`

Esta é a estrutura do repositório resultante:

### Estrutura do repositório {#repository-structure}

```java
{
  "apps": {
    "my-example": {
      "components": {
        "info": {
          "info.html": {
            ...
          },
          "info.js": {
            ...
          }
        }
      }
    }
 },
 "content": {
    "my-example": {
      "sling:resourceType": "my-example/component/info",
      "title": "My Example",
      "description": "This is some example content."
    }
  }
}
```

Considere o seguinte modelo de componente:

```xml
<section class="component-name" data-sly-use.component="component.js">
    <h1>${component.title}</h1>
    <p>${component.description}</p>
</section>
```

A lógica correspondente pode ser gravada usando o seguinte JavaScript do lado do servidor, localizado em um arquivo `component.js` ao lado do modelo:

```javascript
use(function () {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };

    var title = currentPage.getNavigationTitle() || currentPage.getTitle() || currentPage.getName();
    var description = properties.get(Constants.DESCRIPTION_PROP, "").substr(0, Constants.DESCRIPTION_LENGTH);

    return {
        title: title,
        description: description
    };
});
```

Isso tenta obter o `title` de fontes diferentes e corta a descrição para 50 caracteres.

## Dependências {#dependencies}

Vamos imaginar que nós tenhamos uma classe de utilidade que já está equipada com recursos inteligentes, como a lógica padrão para o título de navegação ou recortar uma sequência de caracteres com um certo comprimento:

```javascript
use(['../utils/MyUtils.js'], function (utils) {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };

    var title = utils.getNavigationTitle(currentPage);
    var description = properties.get(Constants.DESCRIPTION_PROP, "").substr(0, Constants.DESCRIPTION_LENGTH);

    return {
        title: title,
        description: description
    };
});
```

## Extensão {#extending}

O padrão de dependência também pode ser usado para estender a lógica de outro componente (que normalmente é `sling:resourceSuperType` do componente atual).

Imagine que o componente pai já fornece o `title`, e nós também queremos adicionar um `description`:

```javascript
use(['../parent-component/parent-component.js'], function (component) {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };

    component.description = utils.shortenString(properties.get(Constants.DESCRIPTION_PROP, ""), Constants.DESCRIPTION_LENGTH);

    return component;
});
```

## Passando parâmetros para um modelo {#passing-parameters-to-a-template}

No caso de instruções `data-sly-template` que podem ser independentes dos componentes, pode ser útil passar parâmetros para a Use-API associada.

Então, em nosso componente vamos chamar um modelo que está localizado em um arquivo diferente:

```xml
<section class="component-name" data-sly-use.tmpl="template.html" data-sly-call="${tmpl.templateName @ page=currentPage}"></section>
```

Em seguida, este é o modelo localizado em `template.html`:

```xml
<template data-sly-template.templateName="${@ page}" data-sly-use.tmpl="${'template.js' @ page=page, descriptionLength=50}">
    <h1>${tmpl.title}</h1>
    <p>${tmpl.description}</p>
</template>
```

A lógica correspondente pode ser gravada usando o seguinte JavaScript do lado do servidor, localizado em um arquivo `template.js` ao lado do arquivo de modelo:

```javascript
use(function () {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description"
    };

    var title = this.page.getNavigationTitle() || this.page.getTitle() || this.page.getName();
    var description = this.page.getProperties().get(Constants.DESCRIPTION_PROP, "").substr(0, this.descriptionLength);

    return {
        title: title,
        description: description
    };
});
```

Os parâmetros transmitidos são definidos na palavra-chave `this`.
