---
title: API de uso do Java do HTL
description: 'Linguagem de modelo HTML - HTL - A API de uso de Java habilita um arquivo de HTL para acessar métodos de ajuda em uma classe de Java personalizada. '
translation-type: tm+mt
source-git-commit: f7e46aaac2a4b51d7fa131ef46692ba6be58d878
workflow-type: tm+mt
source-wordcount: '2558'
ht-degree: 1%

---


# API de uso do Java do HTL {#htl-java-use-api}

A HTML Template Language (HTL) Java Use-API permite que um arquivo HTL acesse métodos auxiliares em uma classe Java personalizada por meio de `data-sly-use`. Isso permite que toda a lógica comercial complexa seja encapsulada no código Java, enquanto o código HTL trata somente da produção de marcação direta.

Um objeto Java Use-API pode ser um POJO simples, instanciado por uma implementação específica por meio do construtor padrão do POJO.

Os POJOs Use-API também podem expor um método público, chamado init, com a seguinte assinatura:

```java
    /**
     * Initializes the Use bean.
     *
     * @param bindings All bindings available to the HTL scripts.
     **/
    public void init(javax.script.Bindings bindings);
```

O mapa `bindings` pode conter objetos que fornecem contexto para o script HTL executado no momento que o objeto Use-API pode usar para seu processamento.

## Um exemplo simples {#a-simple-example}

Vamos start com um componente HTL que não tem uma classe de uso. Ele consiste em um único arquivo, `/apps/my-example/components/info.html`

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html}

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

Também adicionamos algum conteúdo para esse componente a ser renderizado em `/content/my-example/`:

### `http://<host>:<port>/content/my-example.json` {#http-localhost-content-my-example-json}

```java
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

Quando esse conteúdo é acessado, o arquivo HTL é executado. No código HTL, usamos o objeto de contexto `properties` para acessar `title` e `description` do recurso atual e exibi-los. O HTML de saída será:

### `view-source:http://<host>:<port>/content/my-example.html` {#view-source-http-localhost-content-my-example-html}

```xml
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Adicionando uma Classe de Uso {#adding-a-use-class}

O componente **info** na sua versão atual não precisa de uma classe de uso para executar sua função (muito simples). Há casos, no entanto, em que você precisa fazer coisas que não podem ser feitas em HTL e, portanto, você precisa de uma classe de uso. Mas lembre-se do seguinte:

>[!NOTE]
>
>Uma classe use só deve ser usada quando algo não pode ser feito somente em HTL.

Por exemplo, suponha que você deseja que o componente `info` exiba as propriedades `title` e `description` do recurso, mas tudo em minúsculas. Como o HTL não tem um método para sequências de caracteres em minúsculas, você precisará de uma classe de uso. Podemos fazer isso adicionando uma classe de uso Java e alterando `info.html` da seguinte forma:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-1}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    private String lowerCaseTitle;
    private String lowerCaseDescription;

    @Override
    public void activate() throws Exception {
        lowerCaseTitle = getProperties().get("title", "").toLowerCase();
        lowerCaseDescription = getProperties().get("description", "").toLowerCase();
    }

    public String getLowerCaseTitle() {
        return lowerCaseTitle;
    }

    public String getLowerCaseDescription() {
        return lowerCaseDescription;
    }
}
```

Nas seções a seguir, percorremos as diferentes partes do código.

### Classe Java Local vs. Pacote {#local-vs-bundle-java-class}

A classe de uso do Java pode ser instalada de duas formas: **local** ou **bundle**. Este exemplo usa uma instalação local.

Em uma instalação local, o arquivo de origem Java é colocado junto ao arquivo HTL, na mesma pasta do repositório. A fonte é compilada automaticamente sob demanda. Não é necessária nenhuma etapa separada de compilação ou embalagem.

Em uma instalação de pacote, a classe Java deve ser compilada e implantada em um pacote OSGi usando o mecanismo de implantação de conjunto de AEM padrão (consulte [Classe Java embutida](#bundled-java-class)).

>[!NOTE]
>
>Uma **classe de uso local do Java** é recomendada quando a classe de uso é específica para o componente em questão.
>
>Um **bundle Java use-class** é recomendado quando o código Java implementa um serviço que é acessado de vários componentes HTL.

### O pacote Java é o caminho do repositório {#java-package-is-repository-path}

Quando uma instalação local é usada, o nome do pacote da classe use deve corresponder ao do local da pasta do repositório, com todos os hífens no caminho substituídos pelos sublinhados no nome do pacote.

Nesse caso, `Info.java` está localizado em `/apps/my-example/components/info`, portanto, o pacote é `apps.my_example.components.info`:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-1}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {

   ...

}
```

>[!NOTE]
>
>O uso de hífens nos nomes de itens do repositório é uma prática recomendada AEM desenvolvimento. No entanto, os hífens são ilegais nos nomes dos pacotes Java. Por esse motivo, **todos os hífens no caminho do repositório devem ser convertidos em sublinhados no nome do pacote**.

### Extensão `WCMUsePojo` {#extending-wcmusepojo}

Embora existam várias maneiras de incorporar uma classe Java com HTL (consulte Alternativas para `WCMUsePojo`), o mais simples é estender a classe `WCMUsePojo`:

#### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-2}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo

    ...
}
```

### Inicializando a classe {#initializing-the-class}

Quando a classe use é estendida de `WCMUsePojo`, a inicialização é realizada substituindo o método `activate`:

### /apps/my-example/component/info/Info.java {#apps-my-example-component-info-info-java-3}

```java
...

public class Info extends WCMUsePojo {
    private String lowerCaseTitle;
    private String lowerCaseDescription;

    @Override
    public void activate() throws Exception {
        lowerCaseTitle = getProperties().get("title", "").toLowerCase();
        lowerCaseDescription = getProperties().get("description", "").toLowerCase();
    }

...

}
```

### Contexto {#context}

Normalmente, o método [ativate](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) é usado para pré-calcular e armazenar (em variáveis de membro) os valores necessários no código HTL, com base no contexto atual (a solicitação atual e o recurso, por exemplo).

A classe `WCMUsePojo` fornece acesso ao mesmo conjunto de objetos de contexto que estão disponíveis em um arquivo HTL (consulte [Objetos Globais](global-objects.md)).

Em uma classe que estende `WCMUsePojo`, os objetos de contexto podem ser acessados pelo nome usando

[`<T> T get(String name, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)

Como alternativa, os objetos de contexto comumente usados podem ser acessados diretamente pelo **método de conveniência** apropriado:

|  |  |
|---|---|
| [PageManager](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/PageManager.html) | [getPageManager()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageManager()) |
| [Página](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getCurrentPage()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentPage()) |
| [Página](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getResourcePage()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourcePage()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getPageProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageProperties()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getProperties()) |
| [Designer](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [getDesigner()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getDesigner()) |
| [Design](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Design.html) | [getCurrentDesign()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentDesign()) |
| [Estilo](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Style.html) | [getCurrentStyle()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentStyle()) |
| [Componente](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/components/Component.html) | [getComponent()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getComponent()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getInheritedProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getInheritedProperties) |
| [Recurso](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/Resource.html) | [getResource()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResource()) |
| [ResourceResolver](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [getResourceResolver()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourceResolver()) |
| [SlingHttpServletRequest](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [getRequest()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getRequest()) |
| [SlingHttpServletResponse](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [getResponse()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResponse()) |
| [SlingScriptHelper](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [getSlingScriptHelper()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getSlingScriptHelper()) |

### Métodos Getter {#getter-methods}

Depois que a classe use for inicializada, o arquivo HTL será executado. Durante esse estágio, o HTL normalmente obtém o estado de várias variáveis membro da classe de uso e as renderiza para apresentação.

Para fornecer acesso a esses valores a partir do arquivo HTL, é necessário definir métodos getter personalizados na classe use, de acordo com a seguinte convenção de nomenclatura:

* Um método do formulário `getXyz` exporá no arquivo HTL uma propriedade de objeto chamada `xyz`.

No exemplo a seguir, os métodos `getTitle` e `getDescription` resultam em propriedades de objetos `title` e `description` se tornarem acessíveis no contexto do arquivo HTL:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-4}

```java
...

public class Info extends WCMUsePojo {

    ...

    public String getLowerCaseTitle() {
        return lowerCaseTitle;
    }

    public String getLowerCaseDescription() {
        return lowerCaseDescription;
    }
}
```

### atributo de uso inteligente de dados {#data-sly-use-attribute}

O atributo `data-sly-use` é usado para inicializar a use-class dentro do código HTL. Em nosso exemplo, o atributo `data-sly-use` declara que queremos usar a classe `Info`. Podemos usar apenas o nome local da classe, pois estamos usando uma instalação local (uma vez que o arquivo de origem Java foi colocado na mesma pasta do arquivo HTL). Se estivéssemos usando uma instalação de conjunto, teríamos que especificar o nome de classe totalmente qualificado.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-2}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Identificador local {#local-identifier}

O identificador `info` (após o ponto em `data-sly-use.info`) é usado no arquivo HTL para identificar a classe. O escopo desse identificador é global dentro do arquivo, depois que ele é declarado. Não está limitado ao elemento que contém a instrução `data-sly-use`.

### `/apps/my-example/component/info/info.html`{#apps-my-example-component-info-info-html-3}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Obtendo propriedades {#getting-properties}

O identificador `info` é então usado para acessar as propriedades do objeto `title` e `description` que foram expostas por meio dos métodos getter `Info.getTitle` e `Info.getDescription`.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-4}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Saída {#output}

Agora, quando acessamos `/content/my-example.html`, ele retornará o seguinte HTML:

### `view-source:http://<host>:<port>/content/my-example.html` {#view-source-http-localhost-content-my-example-html-1}

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

## Além das noções básicas {#beyond-the-basics}

Nesta seção, apresentaremos outros recursos que vão além do exemplo simples acima:

* Passando parâmetros para uma classe de uso.
* Classe de uso do Java embutida.
* Alternativas para `WCMUsePojo`

### Passando parâmetros {#passing-parameters}

Os parâmetros podem ser passados para uma classe use na inicialização. Por exemplo, nós poderíamos fazer algo assim:

### `/content/my-example/component/info/info.html` {#content-my-example-component-info-info-html}

```xml
<div data-sly-use.info="${'Info' @ text='Some text'}">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
    <p>${info.upperCaseText}</p>
</div>
```

Estamos transmitindo um parâmetro chamado `text`. Em seguida, a classe use maiúscula a string que recuperamos e exibe o resultado com `info.upperCaseText`. Esta é a classe de uso ajustada:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-5}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {

    ...

    private String reverseText;

    @Override
    public void activate() throws Exception {

        ...

        String text = get("text", String.class);
        reverseText = new StringBuffer(text).reverse().toString();

    }

    public String getReverseText() {
        return reverseText;
    }

    ...
}
```

O parâmetro é acessado pelo método `WCMUsePojo` [`<T> T get(String paramName, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

No nosso caso, a declaração:

`get("text", String.class)`

A string é então revertida e exposta pelo método:

`getReverseText()`

### Enviar apenas parâmetros do modelo de sly de dados {#only-pass-parameters-from-data-sly-template}

Embora o exemplo acima esteja tecnicamente correto, não faz muito sentido passar um valor de HTL para inicializar uma classe use, quando o valor em questão está disponível no contexto de execução do código HTL (ou, trivialmente, o valor é estático, como acima).

O motivo é que a classe use sempre terá acesso ao mesmo contexto de execução que o código HTL. Isto traz à tona um ponto de importação das melhores práticas:

>[!NOTE]
>
>A transmissão de um parâmetro para uma classe use só deve ser feita quando a classe use for usada em um arquivo `data-sly-template` que é chamado de outro arquivo HTL com parâmetros que precisam ser transmitidos.

Por exemplo, vamos criar um arquivo separado `data-sly-template` ao lado de nosso exemplo existente. Chamaremos o novo arquivo `extra.html`. Ele contém um bloco `data-sly-template` chamado `extra`:

### `/apps/my-example/component/info/extra.html` {#apps-my-example-component-info-extra-html}

```xml
<template data-sly-template.extra="${@ text}"
          data-sly-use.extraHelper="${'ExtraHelper' @ text=text}">
  <p>${extraHelper.reversedText}</p>
</template>
```

O modelo `extra` utiliza um único parâmetro, `text`. Em seguida, inicializa a classe de uso do Java `ExtraHelper` com o nome local `extraHelper` e passa o valor do parâmetro de modelo `text` como o parâmetro use-class `text`.

O corpo do modelo obtém a propriedade `extraHelper.reversedText` (que, sob o capô, chama `ExtraHelper.getReversedText()`) e exibe esse valor.

Também adaptamos nosso `info.html` existente para usar esse novo modelo:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-5}

```xml
<div data-sly-use.info="Info"
     data-sly-use.extra="extra.html">

  <h1>${info.lowerCaseTitle}</h1>
  <p>${info.lowerCaseDescription}</p>

  <div data-sly-call="${extra.extra @ text=properties.description}"></div>

</div>
```

O arquivo `info.html` agora contém duas `data-sly-use` instruções, a original que importa a `Info` classe de uso Java e uma nova que importa o arquivo de modelo sob o nome local `extra`.

Observe que poderíamos ter colocado o bloco de modelo dentro do arquivo `info.html` para evitar o segundo `data-sly-use`, mas um arquivo de modelo separado é mais comum e mais reutilizável.

A classe `Info` é empregada como antes, chamando seus métodos getter `getLowerCaseTitle()` e `getLowerCaseDescription()` pelas propriedades HTL correspondentes `info.lowerCaseTitle` e `info.lowerCaseDescription`.

Em seguida, executamos um `data-sly-call` para o modelo `extra` e passamos para ele o valor `properties.description` como parâmetro `text`.

A classe de uso do Java `Info.java` foi alterada para lidar com o novo parâmetro de texto:

### `/apps/my-example/component/info/ExtraHelper.java` {#apps-my-example-component-info-extrahelper-java}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class ExtraHelper extends WCMUsePojo {
    private String reversedText;
    ...

    @Override
    public void activate() throws Exception {
        String text = get("text", String.class);
        reversedText = new StringBuilder(text).reverse().toString();

        ...
    }

    public String getReversedText() {
        return reversedText;
    }
}
```

O parâmetro `text` é recuperado com `get("text", String.class)`, o valor é revertido e disponibilizado como o objeto HTL `reversedText` por meio do getter `getReversedText()`.

### Classe Java embutida {#bundled-java-class}

Com uma classe de uso de pacote, a classe deve ser compilada, empacotada e implantada em AEM usando o mecanismo de implantação de pacotes OSGi padrão. Em contraste com uma instalação local, a classe use **declaração do pacote** deve ser chamada normalmente:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-6}

```java
package org.example.app.components;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    ...
}
```

e, a instrução `data-sly-use` deve fazer referência ao nome de classe totalmente qualificado, em oposição ao nome de classe local:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-6}

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```

### Alternativas para `WCMUsePojo` {#alternatives-to-wcmusepojo}

A maneira mais comum de criar uma classe de uso Java é estender `WCMUsePojo`. No entanto, existem várias outras opções. Para entender essas variantes, é útil entender como a instrução HTL `data-sly-use` funciona sob o capô.

Suponha que você tenha a seguinte instrução `data-sly-use`:

**`<div data-sly-use.`** `localName`**`="`**`UseClass`**`">`**

O sistema processa a declaração da seguinte maneira:

(1)

* Se houver um arquivo local `UseClass.java` no mesmo diretório do arquivo HTL, tente compilar e carregar essa classe. Se tiver êxito, vá para (2).
* Caso contrário, interprete `UseClass` como um nome de classe totalmente qualificado e tente carregá-lo do ambiente OSGi. Se tiver êxito, vá para (2).
* Caso contrário, interprete `UseClass` como um caminho para um arquivo HTL ou JavaScript e carregue esse arquivo. Se o goto for bem-sucedido (4).

(2)

* Tente adaptar o `Resource` atual a `UseClass`. Se tiver êxito, vá para (3).
* Caso contrário, tente adaptar o `Request` atual a `UseClass`. Se tiver êxito, vá para (3).
* Caso contrário, tente instanciar `UseClass` com um construtor de argumento zero. Se tiver êxito, vá para (3).

(3)

* Em HTL, vincule o objeto recém-adaptado ou criado ao nome `localName`.
* Se `UseClass` implementar [`io.sightly.java.api.Use`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html), chame o método `init`, transmitindo o contexto de execução atual (na forma de um objeto `javax.scripting.Bindings`).

(4)

* Se `UseClass` for um caminho para um arquivo HTL contendo um `data-sly-template`, prepare o modelo.
* Caso contrário, se `UseClass` for um caminho para uma classe de uso do JavaScript, prepare a classe de uso (consulte [API de uso do JavaScript](use-api-javascript.md)).

Alguns pontos importantes sobre a descrição acima:

* Qualquer classe que seja adaptável de `Resource`, adaptável de `Request` ou que tenha um construtor de argumento zero pode ser uma classe use. A classe não precisa estender `WCMUsePojo` ou mesmo implementar `Use`.
* Entretanto, se a classe use *do* implementar `Use`, seu método `init` será chamado automaticamente com o contexto atual, permitindo que você coloque o código de inicialização lá que dependa desse contexto.
* Uma classe use que estende `WCMUsePojo` é apenas um caso especial de implementação `Use`. Ele fornece os métodos de contexto de conveniência e seu método `activate` é chamado automaticamente de `Use.init`.

### Implementar diretamente o uso da interface {#directly-implement-interface-use}

Embora a maneira mais comum de criar uma classe de uso seja estender `WCMUsePojo`, também é possível implementar diretamente a própria interface [`io.sightly.java.api.Use`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html).

A interface `Use` define apenas um método:

[`public void init(javax.script.Bindings bindings)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use#init(javax.script.Bindings))

O método `init` será chamado na inicialização da classe com um objeto `Bindings` que armazena todos os objetos de contexto e quaisquer parâmetros transmitidos para a classe use.

Toda funcionalidade adicional (como o equivalente de `WCMUsePojo.getProperties()`) deve ser implementada explicitamente usando o objeto [`javax.script.Bindings`](http://docs.oracle.com/javase/7/docs/api/javax/script/Bindings.html). Por exemplo:

### `Info.java` {#info-java}

```java
import io.sightly.java.api.Use;

public class MyComponent implements Use {
   ...
    @Override
    public void init(Bindings bindings) {

        // All standard objects/binding are available
        Resource resource = (Resource)bindings.get("resource");
        ValueMap properties = (ValueMap)bindings.get("properties");
        ...

        // Parameters passed to the use-class are also available
        String param1 = (String) bindings.get("param1");
    }
    ...
}
```

O caso principal para implementar a interface `Use` em vez de estender `WCMUsePojo` é quando você deseja usar uma subclasse de uma classe já existente como a classe use.

### Adaptável a partir do recurso {#adaptable-from-resource}

Outra opção é usar uma classe auxiliar que seja adaptável de `org.apache.sling.api.resource.Resource`.

Digamos que você precise gravar um script HTL que exiba o tipo de mimepe de um ativo DAM. Nesse caso, você sabe que quando seu script HTL é chamado, ele estará dentro do contexto de um `Resource` que envolve um JCR `Node` com o tipo de nó `dam:Asset`.

Você sabe que um nó `dam:Asset` tem uma estrutura como esta:

### Estrutura do repositório {#repository-structure}

```java
{
  "content": {
    "dam": {
      "geometrixx": {
        "portraits": {
          "jane_doe.jpg": {
            ...
          "jcr:content": {
            ...
            "metadata": {
              ...
            },
            "renditions": {
              ...
              "original": {
                ...
                "jcr:content": {
                  "jcr:primaryType": "nt:resource",
                  "jcr:lastModifiedBy": "admin",
                  "jcr:mimeType": "image/jpeg",
                  "jcr:lastModified": "Fri Jun 13 2014 15:27:39 GMT+0200",
                  "jcr:data": ...,
                  "jcr:uuid": "22e3c598-4fa8-4c5d-8b47-8aecfb5de399"
                }
              },
              "cq5dam.thumbnail.319.319.png": {
                  ...
              },
              "cq5dam.thumbnail.48.48.png": {
                  ...
              },
              "cq5dam.thumbnail.140.100.png": {
                  ...
              }
            }
          }  
        }
      }
    }
  }
}
```

Aqui, mostramos o ativo (uma imagem JPEG) que vem com uma instalação padrão de AEM como parte do exemplo geometrixx do projeto. O ativo é chamado `jane_doe.jpg` e seu tipo mimetype é `image/jpeg`.

Para acessar o ativo de dentro do HTL, você pode declarar [`com.day.cq.dam.api.Asset`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/granite/asset/api/Asset.html) como a classe na instrução `data-sly-use` e, em seguida, usar um método get de `Asset` para recuperar as informações desejadas. Por exemplo:

### `mimetype.html` {#mimetype-html}

```xml
<div data-sly-use.asset="com.day.cq.dam.api.Asset">
  <p>${asset.mimeType}</p>
</div>
```

A instrução `data-sly-use` direciona o HTL para adaptar o `Resource` atual a um `Asset` e dá-lhe o nome local `asset`. Em seguida, ele chama o método `getMimeType` de `Asset` usando o encurtador de caracteres HTL: `asset.mimeType`.

### Adaptável a partir da solicitação {#adaptable-from-request}

Também é possível empregar como classe de uso qualquer classe que seja adaptável de [`org.apache.sling.api.SlingHttpServletRequest`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html)

Como no caso acima de um adaptador de classe de uso de `Resource`, um adaptador de classe de uso de [`SlingHttpServletRequest`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) pode ser especificado na instrução `data-sly-use`. Após a execução, a solicitação atual será adaptada à classe fornecida e o objeto resultante será disponibilizado dentro de HTL.
