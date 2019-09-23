---
title: HTL JavaScript Use-API
seo-title: HTL JavaScript Use-API
description: A Linguagem de modelo HTML - HTL - API de uso do JavaScript permite que um arquivo HTL acesse o código auxiliar escrito em JavaScript.
seo-description: A Linguagem de modelo HTML - HTL - API de uso do JavaScript permite que um arquivo HTL acesse o código auxiliar escrito em JavaScript.
uuid: 7ab34b10-30ac-44d6-926b-0234f52e5541
contentOwner: Usuário
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: referência
discoiquuid: 18871af8-e44b-4eec-a483-fcc765dae58f
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: bd1962e25d152be4f1608d0a83d8d5b3e728b4aa

---


# HTL JavaScript Use-API {#htl-javascript-use-api}

A HTML Template Language (HTL) JavaScript Use-API permite que um arquivo HTL acesse o código auxiliar escrito em JavaScript. Isso permite que toda a lógica comercial complexa seja encapsulada no código JavaScript, enquanto o código HTL trata somente da produção de marcação direta.

## Um exemplo simples {#a-simple-example}

We define a component, , located at`info`

**`/apps/my-example/components/info`**

It contains two files:

* **`info.js`**: um arquivo JavaScript que define a classe de uso.
* `info.html`: an HTL file that defines the component . `info` This code will use the functionality of  through the use-API.`info.js`

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

We also create a content node that uses the  component at **`info`**

**`/content/my-example`**, with properties:

* `sling:resourceType = "my-example/component/info"`
* `title = "My Example"`
* `description = "This is some example content."`

Esta é a estrutura do repositório resultante:

### Repository Structure {#repository-structure}

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

Consider following component template:

```xml
<section class="component-name" data-sly-use.component="component.js">
    <h1>${component.title}</h1>
    <p>${component.description}</p>
</section>
```

The corresponding logic can be written using following server-side JavaScript, located in a  file right next to the template:******`component.js`

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

This tries to take the  from different sources and crops the description to 50 characters.`title`

## Dependências {#dependencies}

Let's imagine that we have a utility class that is already equipped with smart features, like the default logic for the navigation title or nicely cutting a string to a certain length:

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

## Extending {#extending}

The dependency pattern can also be used to extend the logic of another component (which typically is the  of the current component).`sling:resourceSuperType`

Imagine que o componente pai já fornece o `title`, e nós também queremos adicionar um **`description`** :

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

## Passando parâmetros para um modelo {#passing-parameters-to-a-template}

No caso de **`data-sly-template`** declarações que podem ser independentes dos componentes, pode ser útil passar parâmetros para a Use-API associada.

Então, em nosso componente vamos chamar um modelo que está localizado em um arquivo diferente:

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

A lógica correspondente pode ser gravada usando o seguinte JavaScript do lado ***do*** servidor, localizado em um `template.js` arquivo ao lado do arquivo de modelo:

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

Os parâmetros passados são definidos na `this` palavra-chave.
