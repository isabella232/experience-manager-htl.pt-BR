---
title: HTL javascript Use-API
seo-title: HTL javascript Use-API
description: The HTML Template Langugae - HTL - javascript Use-API ativa um arquivo
  HTL para acessar o código de ajuda escrito em javascript.
seo-description: The HTML Template Langugae - HTL - javascript Use-API ativa um arquivo
  HTL para acessar o código de ajuda escrito em javascript.
uuid: 7 ab 34 b 10-30 ac -44 d 6-926 b -0234 f 52 e 5541
contentOwner: Usuário
products: SG_ EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: referência
discoiquuid: 18871 af 8-e 44 b -4 eec-a 483-fcc 765 dae 58 f
mwpw-migration-script-version: 2017-10-12 T 21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 796c55d3d85e6b5a3efaa5c04a25be1b0b4e54dd

---


# HTL javascript Use-API {#htl-javascript-use-api}

O Javascript de modelo HTML (HTL) de uso de javascript permite que um arquivo HTL acesse o código de ajuda escrito em javascript. Isso permite que toda a lógica comercial complexa seja encapsulada no código javascript, enquanto o código HTL trata apenas da produção de marcação direta.

## Um exemplo simples {#a-simple-example}

Nós definimos um componente, `info`localizado em

**`/apps/my-example/components/info`**

Ele contém dois arquivos:

* **`info.js`**: um arquivo javascript que define a classe use.
* `info.html`: um arquivo HTL que define o componente `info`. Esse código usará a funcionalidade de `info.js` uso da API de uso.

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

Também criamos um nó de conteúdo que usa o **`info`** componente em

**`/content/my-example`**, com propriedades:

* `sling:resourceType = "my-example/component/info"`
* `title = "My Example"`
* `description = "This is some example content."`

Esta é a estrutura de repositório resultante:

### Estrutura de repositório {#repository-structure}

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

A lógica correspondente pode ser gravada usando o seguinte ***javascript*** do lado do servidor, localizado em um `component.js` arquivo diretamente ao lado do modelo:

```
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

Isso tenta tirar `title` fontes diferentes e recortar a descrição para 50 caracteres.

## Dependências {#dependencies}

Vamos imaginar que temos uma classe de utilitários que já está equipada com recursos inteligentes, como a lógica padrão para o título de navegação ou recorte uma sequência de caracteres com um determinado comprimento:

```
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

O padrão de dependência também pode ser usado para estender a lógica de outro componente (que normalmente é o `sling:resourceSuperType` componente atual).

Imagine que o componente pai já fornece o `title`e também queremos adicionar **`description`** a:

```
use(['../parent-component/parent-component.js'], function (component) {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };
 
    component.description = utils.shortenString(properties.get(Constants.DESCRIPTION_PROP, ""), Constants.DESCRIPTION_LENGTH);
 
    return component;
});
```

## Passar parâmetros para um modelo {#passing-parameters-to-a-template}

No caso **`data-sly-template`** de instruções que podem ser independentes dos componentes, pode ser útil passar parâmetros para a API de uso associada.

Em nosso componente, vamos chamar um modelo que está localizado em um arquivo diferente:

```xml
<section class="component-name" data-sly-use.tmpl="template.html" data-sly-call="${tmpl.templateName @ page=currentPage}"></section>
```

Esse é o modelo localizado em `template.html`:

```xml
<template data-sly-template.templateName="${@ page}" data-sly-use.tmpl="${'template.js' @ page=page, descriptionLength=50}">
    <h1>${tmpl.title}</h1>
    <p>${tmpl.description}</p>
</template>
```

A lógica correspondente pode ser gravada usando o seguinte ***javascript*** do lado do servidor, localizado em um `template.js` arquivo diretamente ao lado do arquivo modelo:

```
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

Os parâmetros enviados são definidos na `this` palavra-chave.
