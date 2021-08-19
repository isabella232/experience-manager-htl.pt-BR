---
title: API de uso do Java do HTL
description: A API de uso do Java da Linguagem de modelo HTML (HTL) permite que um arquivo de HTL acesse métodos de ajuda em uma classe de Java personalizada.
exl-id: 9a9a2bf8-d178-4460-a3ec-cbefcfc09959
source-git-commit: 8e70ee4921a7ea071ab7e06947824c371f4013d8
workflow-type: ht
source-wordcount: '2558'
ht-degree: 100%

---

# API de uso do Java do HTL {#htl-java-use-api}

A API de uso do Java da Linguagem de modelo HTML (HTL) permite que um arquivo de HTL acesse métodos de ajuda em uma classe de Java personalizada `data-sly-use`. Isso permite que toda lógica de negócios complexa seja encapsulada no código Java, enquanto o código HTL lida somente com a produção de marcação direta.

Um objeto da API de uso do Java pode ser um POJO simples, instanciado por uma implementação específica por meio do construtor padrão do POJO.

Os POJOs da API de uso também podem expor um método público, chamado init, com a seguinte assinatura:

```java
    /**
     * Initializes the Use bean.
     *
     * @param bindings All bindings available to the HTL scripts.
     **/
    public void init(javax.script.Bindings bindings);
```

O mapa de `bindings` pode conter objetos que fornecem contexto para o script HTL executado no momento que o objeto da API de uso pode usar para processamento.

## Um exemplo simples {#a-simple-example}

Começaremos com um componente HTL que não tem uma classe de uso. Ele consiste em um único arquivo, `/apps/my-example/components/info.html`

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html}

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

Também adicionamos conteúdo para esse componente renderizar em `/content/my-example/`:

### `http://<host>:<port>/content/my-example.json` {#http-localhost-content-my-example-json}

```java
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

Quando esse conteúdo é acessado, o arquivo HTL é executado. No código HTL, usamos o objeto de contexto `properties` para acessar o `title` e a `description` do recurso atual e exibi-los. O HTML de saída será:

### `view-source:http://<host>:<port>/content/my-example.html` {#view-source-http-localhost-content-my-example-html}

```xml
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Adicionar uma classe de uso {#adding-a-use-class}

O componente **info** na sua versão atual não precisa de uma classe de uso para executar sua função (muito simples). Entretanto, há casos em que é necessário fazer coisas que não podem ser feitas no HTL e, portanto, você precisa de uma classe de uso. Mas lembre-se:

>[!NOTE]
>
>Uma classe de uso só deve ser usada quando algo não pode ser feito somente em HTL.

Por exemplo, suponha que você deseja que o componente `info` exiba as propriedades `title` e `description` do recurso, mas todas em minúsculas. Como o HTL não tem um método para cadeias de caracteres em minúsculas, você precisará de uma classe de uso. Podemos fazer isso adicionando uma classe de uso de Java e alterando o `info.html` da seguinte maneira:

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

Nas seções a seguir, analisaremos as diferentes partes do código.

### Classe de Java local vs. pacote {#local-vs-bundle-java-class}

A classe de uso do Java pode ser instalada de duas maneiras: **local** ou **pacote**. Este exemplo usa uma instalação local.

Em uma instalação local, o arquivo de origem Java é colocado junto com o arquivo HTL, na mesma pasta do repositório. A origem é automaticamente compilada sob demanda. Não é necessária nenhuma etapa de compilação ou empacotamento separada.

Em uma instalação de pacote, a classe de Java deve ser compilada e implantada em um pacote OSGi usando o mecanismo padrão de implantação de pacote do AEM (consulte [Classe de Java agrupada](#bundled-java-class)).

>[!NOTE]
>
>Uma **classe de uso local do Java** é recomendada quando a classe de uso é específica para o componente em questão.
>
>Uma **classe de uso em pacote do Java** é recomendada quando o código Java implementa um serviço acessado de vários componentes HTL.

### O pacote Java é o caminho do repositório {#java-package-is-repository-path}

Quando uma instalação local é usada, o nome do pacote da classe de uso deve corresponder ao do local da pasta do repositório, com quaisquer hifens no caminho substituídos por sublinhados no nome do pacote.

Neste caso, `Info.java` está localizado em `/apps/my-example/components/info`, portanto, o pacote é `apps.my_example.components.info`:

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
>Usar hifens nos nomes dos itens do repositório é uma prática recomendada no desenvolvimento do AEM. No entanto, os hifens são inválidos nos nomes dos pacotes Java. Por esse motivo, **todos os hifens no caminho do repositório devem ser convertidos em sublinhados no nome do pacote**.

### Extensão `WCMUsePojo` {#extending-wcmusepojo}

Embora existam várias maneiras de incorporar uma classe de Java com HTL (consulte Alternativas para `WCMUsePojo`), o mais simples é estender a classe `WCMUsePojo`:

#### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-2}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo

    ...
}
```

### Inicializar a classe {#initializing-the-class}

Quando a classe de uso é estendida de `WCMUsePojo`, a inicialização é executada substituindo o método `activate`:

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

Normalmente, o método [ativar](https://helpx.adobe.com/br/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) é usado para pré-calcular e armazenar (em variáveis de membro) os valores necessários no código HTL, com base no contexto atual (na solicitação e no recurso atuais, por exemplo).

A classe `WCMUsePojo` fornece acesso ao mesmo conjunto de objetos de contexto que estão disponíveis em um arquivo HTL (consulte [Objetos Globais](global-objects.md)).

Em uma classe que estende `WCMUsePojo`, os objetos de contexto podem ser acessados pelo nome usando

[`<T> T get(String name, Class<T> type)`](https://helpx.adobe.com/br/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)

Como alternativa, os objetos de contexto comumente usados podem ser acessados diretamente pelo **método de conveniência** apropriado:

|  |  |
|---|---|
| [PageManager](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/PageManager.html) | [getPageManager()](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageManager()) |
| [Página](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getCurrentPage()](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentPage()) |
| [Página](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getResourcePage()](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourcePage()) |
| [ValueMap](https://helpx.adobe.com/br/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getPageProperties()](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageProperties()) |
| [ValueMap](https://helpx.adobe.com/br/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getProperties()](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getProperties()) |
| [Designer](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [getDesigner()](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getDesigner()) |
| [Design](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Design.html) | [getCurrentDesign()](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentDesign()) |
| [Estilo](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Style.html) | [getCurrentStyle()](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentStyle()) |
| [Componente](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/components/Component.html) | [getComponent()](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getComponent()) |
| [ValueMap](https://helpx.adobe.com/br/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getInheritedProperties()](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getInheritedProperties) |
| [Recurso](https://helpx.adobe.com/br/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/Resource.html) | [getResource()](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResource()) |
| [ResourceResolver](https://helpx.adobe.com/br/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [getResourceResolver()](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourceResolver()) |
| [SlingHttpServletRequest](https://helpx.adobe.com/br/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [getRequest()](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getRequest()) |
| [SlingHttpServletResponse](https://helpx.adobe.com/br/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [getResponse()](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResponse()) |
| [SlingScriptHelper](https://helpx.adobe.com/br/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [getSlingScriptHelper()](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getSlingScriptHelper()) |

### Métodos Getter {#getter-methods}

Depois que a classe de uso for inicializada, o arquivo HTL será executado. Durante esse estágio, o HTL normalmente obtém o estado de diversas variáveis de membro da classe de uso e as renderiza para a apresentação.

Para fornecer acesso a esses valores a partir do arquivo HTL, você deve definir métodos Getter personalizados na classe de uso de acordo com a seguinte convenção de nomenclatura:

* Um método do formulário `getXyz` exporá no arquivo HTL uma propriedade de objeto chamada `xyz`.

No exemplo a seguir, os métodos `getTitle` e `getDescription` resultam nas propriedades do objeto `title` e `description` tornando-se acessíveis no contexto do arquivo HTL:

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

### Atributo data-sly-use {#data-sly-use-attribute}

O atributo `data-sly-use` é usado para inicializar a classe de uso no seu código HTL. No nosso exemplo, o atributo `data-sly-use` declara que queremos usar a classe `Info`. Podemos usar apenas o nome local da classe, porque estamos usando uma instalação local (uma vez que o arquivo de origem Java tenha sido colocado na mesma pasta que o arquivo HTL). Se estivéssemos usando uma instalação de pacote, precisaríamos especificar o nome de classe totalmente qualificado.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-2}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Identificador local {#local-identifier}

O identificador `info` (após o ponto em `data-sly-use.info`) é usado no arquivo HTL para identificar a classe. O escopo desse identificador é global dentro do arquivo, depois que ele tiver sido declarado. Não está limitado ao elemento que contém a instrução `data-sly-use`.

### `/apps/my-example/component/info/info.html`{#apps-my-example-component-info-info-html-3}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Obter propriedades {#getting-properties}

O identificador `info` é então usado para acessar as propriedades do objeto `title` e `description`, que foram expostas por meio dos métodos Getter `Info.getTitle` e `Info.getDescription`.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-4}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Saída {#output}

Agora, ao acessar `/content/my-example.html`, ele retornará o seguinte HTML:

### `view-source:http://<host>:<port>/content/my-example.html` {#view-source-http-localhost-content-my-example-html-1}

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

## Além das noções básicas {#beyond-the-basics}

Nesta seção, introduziremos outros recursos que vão além do exemplo simples acima:

* Passar parâmetros para uma classe de uso.
* Classe de uso de Java agrupada.
* Alternativas para `WCMUsePojo`

### Passagem de parâmetros {#passing-parameters}

Os parâmetros podem ser passados para uma classe de uso mediante inicialização. No caso, podemos fazer algo como no exemplo a seguir:

### `/content/my-example/component/info/info.html` {#content-my-example-component-info-info-html}

```xml
<div data-sly-use.info="${'Info' @ text='Some text'}">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
    <p>${info.upperCaseText}</p>
</div>
```

Aqui, estamos passando um parâmetro chamado `text`. A classe de uso passa para maiúscula a cadeia de caracteres recuperada e exibe o resultado com `info.upperCaseText`. Esta é a classe de uso ajustada:

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

O parâmetro é acessado por meio do método `WCMUsePojo` [`<T> T get(String paramName, Class<T> type)`](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

No nosso caso, a instrução:

`get("text", String.class)`

A cadeia de caracteres é então revertida e exposta pelo método:

`getReverseText()`

### Passar parâmetros somente a partir de data-sly-template {#only-pass-parameters-from-data-sly-template}

Embora o exemplo acima esteja tecnicamente correto, não faz muito sentido passar um valor a partir de HTL para inicializar uma classe de uso, quando o valor em questão está disponível no contexto de execução do código HTL (ou, trivialmente, o valor é estático, conforme acima).

O motivo é que a classe de uso sempre terá acesso ao mesmo contexto de execução que o código HTL. Isso traz à tona um ponto de importação de prática recomendada:

>[!NOTE]
>
>Passar um parâmetro para uma classe de uso só deve ser feita quando a classe de uso for usada em um arquivo `data-sly-template`, sendo ele mesmo chamado a partir de outro arquivo HTL com parâmetros que precisam ser passados.

Por exemplo, vamos criar um arquivo `data-sly-template` separado junto com nosso exemplo existente. Chamaremos o novo arquivo de `extra.html`. Ele contém um bloco `data-sly-template` chamado `extra`:

### `/apps/my-example/component/info/extra.html` {#apps-my-example-component-info-extra-html}

```xml
<template data-sly-template.extra="${@ text}"
          data-sly-use.extraHelper="${'ExtraHelper' @ text=text}">
  <p>${extraHelper.reversedText}</p>
</template>
```

O modelo `extra` toma um único parâmetro, `text`. Em seguida, ele inicializa a classe de uso do Java `ExtraHelper`, com o nome local `extraHelper`, e passa o valor do parâmetro de modelo `text` como o parâmetro de classe de uso `text`.

O corpo do modelo obtém a propriedade `extraHelper.reversedText` (que, por baixo dos panos, na verdade é chamada de `ExtraHelper.getReversedText()`) e exibe esse valor.

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

O arquivo `info.html` agora contém duas instruções `data-sly-use`: a original, que importa a classe de uso do Java `Info`; e uma nova, que importa o arquivo de modelo sob o nome local `extra`.

Observe que poderíamos ter colocado o bloco do modelo dentro do arquivo `info.html` para evitar o segundo `data-sly-use`, mas um arquivo de modelo separado é mais comum e mais reutilizável.

A classe `Info` é empregada como antes, chamando seus métodos Getter `getLowerCaseTitle()` e `getLowerCaseDescription()` por meio de suas propriedades HTL correspondentes `info.lowerCaseTitle` e `info.lowerCaseDescription`.

Em seguida, executamos um `data-sly-call` para o modelo `extra` e passamos o valor `properties.description` como o parâmetro `text`.

A classe de uso do Java `Info.java` é alterada para lidar com o novo parâmetro de texto:

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

O parâmetro `text` é recuperado com `get("text", String.class)`, o valor é revertido e disponibilizado como o objeto HTL `reversedText` por meio do Getter `getReversedText()`.

### Classe de Java agrupada {#bundled-java-class}

Com uma classe de uso agrupada, a classe deve ser compilada, empacotada e implantada no AEM usando o mecanismo de implantação de pacote OSGi padrão. Ao contrário de uma instalação local, a classe de uso **declaração do pacote** deve ser nomeada normalmente:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-6}

```java
package org.example.app.components;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    ...
}
```

e a instrução `data-sly-use` deve fazer referência ao nome de classe totalmente qualificado, em vez de apenas ao nome de classe local:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-6}

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```

### Alternativas para `WCMUsePojo` {#alternatives-to-wcmusepojo}

A maneira mais comum de criar uma classe de uso do Java é estender `WCMUsePojo`. No entanto, existem várias outras opções. Para entendê-las, seria de ajuda entender como a instrução HTL `data-sly-use` funciona por baixo dos panos.

Suponhamos que você tenha a seguinte instrução `data-sly-use`:

**`<div data-sly-use.`** `localName`**`="`**`UseClass`**`">`**

O sistema processa a instrução da seguinte maneira:

(1)

* Se houver um arquivo local `UseClass.java` no mesmo diretório do arquivo HTL, tente compilar e carregar essa classe. Se tiver êxito, acesse a etapa (2).
* Caso contrário, interprete `UseClass` como um nome de classe totalmente qualificado e tente carregá-lo do ambiente OSGi. Se tiver êxito, acesse a etapa (2).
* Caso contrário, interprete `UseClass` como um caminho para um arquivo HTL ou JavaScript e carregue esse arquivo. Se tiver êxito, acesse a etapa (4).

(2)

* Tente adaptar o `Resource` atual para `UseClass`. Se tiver êxito, acesse a etapa (3).
* Caso contrário, tente adaptar a `Request` atual para `UseClass`. Se tiver êxito, acesse a etapa (3).
* Caso contrário, tente instanciar `UseClass` com um construtor de argumento zero. Se tiver êxito, acesse a etapa (3).

(3)

* Dentro do HTL, vincule o objeto recém-adaptado ou criado ao nome `localName`.
* Se `UseClass` implementar [`io.sightly.java.api.Use`](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html), chame o método `init`, transmitindo o contexto de execução atual (no formato de um objeto `javax.scripting.Bindings`).

(4)

* Se `UseClass` for um caminho para um arquivo HTL contendo um `data-sly-template`, prepare o modelo.
* Caso contrário, se `UseClass` for um caminho para uma classe de uso do JavaScript, prepare a classe de uso (consulte [API de uso do JavaScript](use-api-javascript.md)).

Alguns pontos importantes sobre a descrição acima:

* Qualquer classe que seja adaptável a partir de `Resource`, adaptável a partir de `Request` ou que tenha um construtor de argumento zero pode ser uma classe de uso. A classe não precisa estender `WCMUsePojo`, nem mesmo implementar `Use`.
* No entanto, se a classe de uso *de fato* implementar `Use`, seu método `init` será chamado automaticamente com o contexto atual, permitindo que você coloque o código de inicialização que dependa desse contexto.
* Uma classe de uso que estende o `WCMUsePojo` é apenas um caso especial de implementação `Use`. Ela fornece os métodos de contexto de conveniência e seu método `activate` é chamado automaticamente a partir de `Use.init`.

### Implementar o uso da interface diretamente {#directly-implement-interface-use}

Embora a maneira mais comum de criar uma classe de uso seja estender `WCMUsePojo`, também é possível implementar diretamente a própria interface [`io.sightly.java.api.Use`](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html).

A interface `Use` define apenas um método:

[`public void init(javax.script.Bindings bindings)`](https://helpx.adobe.com/br/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use#init(javax.script.Bindings))

O método `init` será chamado na inicialização da classe com um objeto `Bindings` que armazena todos os objetos de contexto e quaisquer parâmetros transmitidos para a classe de uso.

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

O principal caso para implementar a interface de `Use` por conta própria em vez de estender o `WCMUsePojo` é quando você deseja usar uma subclasse de uma classe já existente como a classe de uso.

### Adaptável do recurso {#adaptable-from-resource}

Outra opção é usar uma classe auxiliar que seja adaptável de `org.apache.sling.api.resource.Resource`.

Digamos que você precise gravar um script HTL que exiba o MIMEtype de um ativo DAM. Nesse caso, você sabe que quando seu script HTL é chamado, ele estará no contexto de um `Resource` que envolve um `Node` JCR com o Nodetype `dam:Asset`.

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

Aqui mostramos o ativo (uma imagem JPEG) que vem com uma instalação padrão do AEM como parte do projeto de exemplo Geometrixx. O ativo é chamado de `jane_doe.jpg` e o MIMEtype é `image/jpeg`.

Para acessar o ativo de dentro do HTL, você pode declarar [`com.day.cq.dam.api.Asset`](https://helpx.adobe.com/br/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/granite/asset/api/Asset.html) como a classe na instrução `data-sly-use` e usar um método get de `Asset` para recuperar as informações desejadas. Por exemplo:

### `mimetype.html` {#mimetype-html}

```xml
<div data-sly-use.asset="com.day.cq.dam.api.Asset">
  <p>${asset.mimeType}</p>
</div>
```

A instrução `data-sly-use` direciona o HTL para adaptar o `Resource` atual a um `Asset` e dar a ele o nome local `asset`. Em seguida, ele chama o método `getMimeType` de `Asset`, usando a abreviação getter HTL: `asset.mimeType`.

### Adaptável desde a solicitação {#adaptable-from-request}

Também é possível empregar como classe de uso qualquer classe que seja adaptável de [`org.apache.sling.api.SlingHttpServletRequest`](https://helpx.adobe.com/br/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html)

Similar ao caso acima, de uma classe de uso adaptável de `Resource`, uma classe de uso adaptável de [`SlingHttpServletRequest`](https://helpx.adobe.com/br/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) pode ser especificada na instrução `data-sly-use`. Após a execução, a solicitação atual será adaptada à classe fornecida e o objeto resultante será disponibilizado no HTL.
