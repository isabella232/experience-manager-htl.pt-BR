---
title: API de uso do Java HTL
seo-title: API de uso do Java HTL
description: 'Linguagem de modelo HTML - HTL - A API de uso de Java habilita um arquivo de HTL para acessar métodos de ajuda em uma classe de Java personalizada. '
seo-description: 'Linguagem de modelo HTML - HTL - A API de uso de Java habilita um arquivo de HTL para acessar métodos de ajuda em uma classe de Java personalizada. '
uuid: b340f8f7-a193-45c8-aa39-5c6e2c0194ea
contentOwner: Usuário
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: referência
discoiquuid: 126ebc9d-5f7b-47a4-aea2-c8840d34864c
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 48f09cfbdc5ef0bc5b951de1444ce40ea53dc07f

---


# API de uso do Java HTL{#htl-java-use-api}

A HTML Template Language (HTL) Java Use-API permite que um arquivo HTL acesse métodos auxiliares em uma classe Java personalizada. Isso permite que toda a lógica comercial complexa seja encapsulada no código Java, enquanto o código HTL trata somente da produção de marcação direta.

## Um exemplo simples {#a-simple-example}

Começaremos com um componente HTL que *não* tem uma classe de uso. Consiste num único ficheiro. `/apps/my-example/components/info.html`

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html}

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

Também adicionamos algum conteúdo para este componente para renderizar em **`/content/my-example/`**:

### `http://localhost:4502/content/my-example.json` {#http-localhost-content-my-example-json}

```java
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

Quando esse conteúdo é acessado, o arquivo HTL é executado. No código HTL, usamos o objeto de contexto **`properties`**para acessar o recurso atual `title` e exibi-lo `description` . O HTML de saída será:

### `view-source:http://localhost:4502/content/my-example.html` {#view-source-http-localhost-content-my-example-html}

```xml
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Adicionar uma classe de uso {#adding-a-use-class}

O componente **info** na sua versão atual não precisa de uma classe use para executar sua função (muito simples). Há casos, no entanto, em que você precisa fazer coisas que não podem ser feitas em HTL e, portanto, você precisa de uma classe de uso. Mas lembre-se do seguinte:

>[!NOTE]
>
>*Uma classe use só deve ser usada quando algo não pode ser feito somente em HTL.*

Por exemplo, suponha que você deseja que o `info` componente exiba as `title` e **`description`** propriedades do recurso, mas tudo em minúsculas. Como o HTL não tem um método para sequências de caracteres em minúsculas, você precisará de uma classe de uso. Podemos fazer isso adicionando uma classe de uso Java e alterando a **`info.html`** seguinte maneira:

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

### Classe Java Local vs Bundle {#local-vs-bundle-java-class}

A classe de uso do Java pode ser instalada de duas formas: **local** ou **pacote**. *Este exemplo usa uma instalação local.*

Em uma instalação local, o arquivo de origem Java é colocado junto ao arquivo HTL, na mesma pasta do repositório. A fonte é compilada automaticamente sob demanda. Nenhuma etapa separada de compilação ou embalagem é necessária.

Em uma instalação de pacote, a classe Java deve ser compilada e implantada em um pacote OSGi usando o mecanismo de implantação de conjunto AEM padrão (consulte Classe [](#bundled-java-class)Java embutida).

>[!NOTE]
>
>Uma classe **** local de uso do Java é recomendada quando a classe de uso é específica para o componente em questão.
>
>Uma classe **de uso do Java de** pacote é recomendada quando o código Java implementa um serviço que é acessado de vários componentes HTL.

### O pacote Java é o caminho do repositório {#java-package-is-repository-path}

Quando uma instalação local é usada, o nome do pacote da classe use deve corresponder ao do local da pasta do repositório, com todos os hífens no caminho substituídos pelos sublinhados no nome do pacote.

Nesse caso, **`Info.java`** está localizado em **`/apps/my-example/components/info`** ordem decrescente para o pacote **`apps.my_example.components.info`** :

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
>Usar hífens nos nomes de itens do repositório é uma prática recomendada no desenvolvimento do AEM. No entanto, os hífens são ilegais nos nomes dos pacotes Java. Por isso, **todos os hífens no caminho do repositório devem ser convertidos em sublinhados no nome** do pacote.

### Extensão `WCMUsePojo`{#extending-wcmusepojo}

Embora haja várias maneiras de incorporar uma classe Java a HTL (consulte Alternativas para `WCMUsePojo`), o mais simples é estender a `WCMUsePojo` classe:

#### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-2}

```java
package apps.my_example.components.info;
 
import com.adobe.cq.sightly.WCMUsePojo;
 
public class Info extends WCMUsePojo {
    
    ...
}
```

### Inicializando a classe {#initializing-the-class}

Quando a classe use é estendida de **`WCMUsePojo`**, a inicialização é realizada substituindo o **`activate`** método:

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

Geralmente, o método [ativate](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html) é usado para pré-calcular e armazenar (em variáveis de membro) os valores necessários no código HTL, com base no contexto atual (a solicitação atual e o recurso, por exemplo).

A `WCMUsePojo` classe fornece acesso ao mesmo conjunto de objetos de contexto que estão disponíveis em um arquivo HTL (consulte Objetos [globais](global-objects.md)).

Em uma classe que se estende **`WCMUsePojo`**, objetos de contexto podem ser acessados *pelo nome* usando

[`<T> T get(String name, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

Como alternativa, os objetos de contexto usados normalmente podem ser acessados diretamente pelo método **de** conveniência apropriado:

|  |  |
|---|---|
| [PageManager](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/PageManager.html) | [getPageManager()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageManager()) |
| [Modos](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getCurrentPage()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentPage()) |
| [Modos](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getResourcePage()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourcePage()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getPageProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageProperties()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getProperties()) |
| [Designer](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [getDesigner()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getDesigner()) |
| [Design](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Design.html) | [getCurrentDesign()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentDesign()) |
| [Estilo](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Style.html) | [getCurrentStyle()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentStyle()) |
| [Componente](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/components/Component.html) | [getComponent()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getComponent()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getInheritedProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse#getInheritedProperties.html()) |
| [Recurso](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/Resource.html) | [getResource()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResource()) |
| [ResourceResolver](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [getResourceResolver()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourceResolver()) |
| [SlingHttpServletRequest](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [getRequest()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getRequest()) |
| [SlingHttpServletResponse](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [getResponse()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResponse()) |
| [SlingScriptHelper](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [getSlingScriptHelper()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getSlingScriptHelper()) |

### Métodos Getter {#getter-methods}

Depois que a classe use for inicializada, o arquivo HTL será executado. Durante esse estágio, o HTL normalmente obtém o estado de várias variáveis membro da classe de uso e as renderiza para apresentação.

Para fornecer acesso a esses valores a partir do arquivo HTL, é necessário definir métodos getter personalizados na classe use de **acordo com a seguinte convenção** de nomenclatura:

* Um método do formulário **`getXyz`** exporá no arquivo HTL uma propriedade de objeto chamada ***xyz***.

Por exemplo, no exemplo a seguir, os métodos **`getTitle`** e **`getDescription`** resultam nas propriedades do objeto **`title`** e **`description`** tornam-se acessíveis no contexto do arquivo HTL:

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

O **`data-sly-use`** atributo é usado para inicializar a classe use dentro do código HTL. Em nosso exemplo, o `data-sly-use` atributo declara que queremos usar a classe **`Info`**. Podemos usar apenas o nome local da classe, pois estamos usando uma instalação local (uma vez que o arquivo de origem Java foi colocado na mesma pasta do arquivo HTL). Se estivéssemos usando uma instalação de pacote, teríamos que especificar o nome de classe totalmente qualificado (Consulte Instalação [de pacote da classe](#LocalvsBundleJavaClass)Use).

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-2}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Identificador local {#local-identifier}

O identificador '**`info`**' (após o ponto em **`data-sly-use.info`**) é usado no arquivo HTL para identificar a classe. O escopo desse identificador é global dentro do arquivo, depois que ele é declarado. Não está limitado ao elemento que contém a `data-sly-use` declaração.

### `/apps/my-example/component/info/info.html`{#apps-my-example-component-info-info-html-3}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Obter propriedades {#getting-properties}

O identificador `info` é usado para acessar as propriedades do objeto **`title`** e **`description`** que foram expostas por meio dos métodos getter `Info.getTitle` e **`Info.getDescription`**.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-4}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Saída {#output}

Agora, quando acessamos **`/content/my-example.html`** ele retornará o seguinte HTML:

### `view-source:http://localhost:4502/content/my-example.html` {#view-source-http-localhost-content-my-example-html-1}

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

## Além dos fundamentos {#beyond-the-basics}

Nesta seção, apresentaremos outros recursos que vão além do exemplo simples acima:

* Passando parâmetros para uma classe de uso.
* Classe de uso do Java embutida.
* Alternativas para `WCMUsePojo`

### Passar parâmetros {#passing-parameters}

Os parâmetros podem ser passados para uma classe use na inicialização. Por exemplo, nós poderíamos fazer algo assim:

### `/content/my-example/component/info/info.html` {#content-my-example-component-info-info-html}

```xml
<div data-sly-use.info="${'Info' @ text='Some text'}">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
    <p>${info.upperCaseText}</p>
</div>
```

Aqui estamos passando um parâmetro chamado **`text`**. Em seguida, a classe use maiúscula a string que recuperamos e exibe o resultado com `info.upperCaseText`. Esta é a classe de uso ajustada:

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

O parâmetro é acessado pelo `WCMUsePojo` método

[ `<T> T get(String paramName, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

No nosso caso, a declaração

`get("text", String.class)`

A string é então revertida e exposta pelo método

**`getReverseText()`**

### Enviar apenas parâmetros a partir de modelo de dados {#only-pass-parameters-from-data-sly-template}

Embora o exemplo acima esteja tecnicamente correto, não faz muito sentido passar um valor de HTL para inicializar uma classe use, quando o valor em questão está disponível no contexto de execução do código HTL (ou, trivialmente, o valor é estático, como acima).

O motivo é que a classe use sempre terá acesso ao mesmo contexto de execução que o código HTL. Isto traz à tona um ponto de importação das melhores práticas:

>[!NOTE]
>
>A transmissão de um parâmetro para uma classe de uso só deve ser feita quando a classe de uso for usada em um arquivo de modelo **** de dados que é chamado de outro arquivo HTL com parâmetros que precisam ser transmitidos.

Por exemplo, vamos criar um `data-sly-template` arquivo separado junto com nosso exemplo existente. Ligaremos para o novo arquivo `extra.html`. Ele contém um **`data-sly-template`** bloco chamado **`extra`**:

### `/apps/my-example/component/info/extra.html` {#apps-my-example-component-info-extra-html}

```xml
<template data-sly-template.extra="${@ text}"
          data-sly-use.extraHelper="${'ExtraHelper' @ text=text}">
  <p>${extraHelper.reversedText}</p>
</template>
```

O modelo **`extra`**, usa um único parâmetro, **`text`**. Em seguida, inicializa a classe de uso do Java `ExtraHelper` com o nome local **`extraHelper`** e passa o valor do parâmetro template **`text`** como o parâmetro use-class **`text`**.

O corpo do modelo obtém a propriedade `extraHelper.reversedText` (que, sob o capô, realmente chama `ExtraHelper.getReversedText()`) e exibe esse valor.

Além disso, adaptamos nosso modelo existente **`info.html`** para usar esse novo modelo:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-5}

```xml
<div data-sly-use.info="Info" 
     data-sly-use.extra="extra.html">
    
  <h1>${info.lowerCaseTitle}</h1>
  <p>${info.lowerCaseDescription}</p>
    
  <div data-sly-call="${extra.extra @ text=properties.description}"></div>

</div>
```

O arquivo `info.html` agora contém duas **`data-sly-use`** declarações, a original que importa a classe de uso **`Info`** Java e uma nova que importa o arquivo de modelo sob o nome local `extra`.

Observe que podíamos ter colocado o bloco de modelo dentro do **`info.html`** arquivo para evitar o segundo **`data-sly-use`**, mas um arquivo de modelo separado é mais comum e mais reutilizável.

A **`Info`** classe é empregada como antes, chamando seus métodos getter **`getLowerCaseTitle()`** e `getLowerCaseDescription()` por meio de suas propriedades HTL `info.lowerCaseTitle` e **`info.lowerCaseDescription`**.

Em seguida, executamos um teste **`data-sly-call`** para o modelo **`extra`** e passamos o valor `properties.description` como parâmetro **`text`**.

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

O **`text`** parâmetro é recuperado com **`get("text", String.class)`**, o valor é revertido e disponibilizado como o objeto HTL `reversedText` por meio do getter `getReversedText()`.

### Classe Java embutida {#bundled-java-class}

Com uma classe de uso de pacote, a classe deve ser compilada, empacotada e implantada no AEM usando o mecanismo de implantação de pacotes OSGi padrão. Em contraste com uma instalação local, a declaração **do** pacote use-class deve ser chamada normalmente:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-6}

```java
package org.example.app.components;
 
import com.adobe.cq.sightly.WCMUsePojo;
 
public class Info extends WCMUsePojo {
    ...
}
```

e, a `data-sly-use` declaração deve mencionar o nome *da classe* totalmente qualificada, em vez de apenas o nome da classe local:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-6}

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```

### Alternativas para `WCMUsePojo`{#alternatives-to-wcmusepojo}

A maneira mais comum de criar uma classe de uso Java é estender `WCMUsePojo`. No entanto, existem várias outras opções. Para entender essas variantes, é útil entender como a `data-sly-use` declaração HTL funciona sob o capô.

Suponha que você tenha a seguinte `data-sly-use` afirmação:

**`<div data-sly-use.`** `localName`**`="`** `UseClass`**`">`**

O sistema processa a declaração da seguinte maneira:

(1)

* Se houver um arquivo local *UseClass.java* no mesmo diretório do arquivo HTL, tente compilar e carregar essa classe. Se tiver êxito, vá para (2).

* Caso contrário, interprete *UseClass* como um nome de classe totalmente qualificado e tente carregá-lo do ambiente OSGi. Se tiver êxito, vá para (2).
* Caso contrário, interprete *UseClass* como um caminho para um arquivo HTL ou JavaScript e carregue esse arquivo. Se o goto for bem-sucedido (4).

(2)

* Tente adaptar a corrente **`Resource`** para *`UseClass.`* Se bem-sucedido, vá para (3).

* Caso contrário, tente adaptar a corrente **`Request`** para *`UseClass`*. Se tiver êxito, vá para (3).

* Caso contrário, tente instanciar *`UseClass`* com um construtor de argumento zero. Se tiver êxito, vá para (3).

(3)

* Em HTL, vincule o objeto recém-adaptado ou criado ao nome *`localName`*.
* Se *`UseClass`* implementa [ então chame o `io.sightly.java.api.Use`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html) método, transmitindo o contexto de execução atual (na forma de um `init` `javax.scripting.Bindings` objeto).

(4)

* Se *`UseClass`* for um caminho para um arquivo HTL contendo um `data-sly-template`, prepare o modelo.

* Caso contrário, se *`UseClass`* for um caminho para uma classe de uso do JavaScript, prepare a classe de uso (consulte [JavaScript Use-API](use-api-javascript.md)).

Alguns pontos importantes sobre a descrição acima:

* Qualquer classe que seja adaptável, adaptável `Resource``Request`ou que tenha um construtor de argumento zero pode ser uma classe use. A classe não precisa estender `WCMUsePojo` ou nem mesmo implementar `Use`.

* No entanto, se a classe use *não* implementar `Use`, seu **`init`** método será chamado automaticamente com o contexto atual, permitindo que você coloque o código de inicialização lá que dependa desse contexto.

* Uma classe de uso que se estende `WCMUsePojo` é apenas um caso especial de implementação **`Use`**. Ele fornece os métodos de contexto de conveniência e seu **`activate`** método é chamado automaticamente de `Use.init`.

### Implementação direta do uso da interface {#directly-implement-interface-use}

Embora a maneira mais comum de criar uma classe de uso seja estender `WCMUsePojo`, também é possível implementar diretamente a própria `[io.sightly.java.api.Use](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html)`interface.

A `Use` interface define apenas um método:

`[public void init(javax.script.Bindings bindings)](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use#init(javax.script.Bindings))`

O **`init`** método será chamado na inicialização da classe com um **`Bindings`** objeto que armazena todos os objetos de contexto e quaisquer parâmetros transmitidos para a classe use.

Toda funcionalidade adicional (como o equivalente de `WCMUsePojo.getProperties()`) deve ser implementada explicitamente usando o ` [javax.script.Bindings](http://docs.oracle.com/javase/7/docs/api/javax/script/Bindings.html)` objeto. Por exemplo:

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

O caso principal para implementar a `Use` interface em vez de estender `WCMUsePojo` é quando você deseja usar uma subclasse de uma classe já existente como a classe use.

### Adaptável a partir do recurso {#adaptable-from-resource}

Outra opção é usar uma classe auxiliar adaptável **`org.apache.sling.api.resource.Resource`**.

Digamos que você precise gravar um script HTL que exiba o tipo de mimepe de um ativo DAM. Nesse caso, você sabe que quando seu script HTL for chamado, ele estará dentro do contexto de um **`Resource`** que envolve um JCR **`Node`** com um tipo de nó **`dam:Asset`**.

Você sabe que um **`dam:Asset`** nó tem uma estrutura como esta:

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

Aqui, mostramos o ativo (uma imagem JPEG) que vem com uma instalação padrão do AEM como parte do exemplo geometrixx do projeto. O ativo é chamado **`jane_doe.jpg`** e seu tipo mimético é **`image/jpeg`**.

Para acessar o ativo de dentro do HTL, você pode declarar ` [com.day.cq.dam.api.Asset](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/granite/asset/api/Asset.html)` a classe na **`data-sly-use`** declaração: e use um método get para recuperar **`Asset`** as informações desejadas. Por exemplo:

### `mimetype.html` {#mimetype-html}

```xml
<div data-sly-use.asset="com.day.cq.dam.api.Asset">
  <p>${asset.mimeType}</p>
</div>
```

A `data-sly-use` declaração direciona o HTL para adaptar o atual **`Resource`** a um **`Asset`** e dar-lhe o nome local **`asset`**. Em seguida, ele chama o **`getMimeType`** método de `Asset` uso do comando HTL getter shorthand: `asset.mimeType`.

### Adaptável a partir da solicitação {#adaptable-from-request}

Também é possível usar como classe de usuário qualquer classe que seja adaptável de **` [org.apache.sling.api.SlingHttpServletRequest](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html)`**

Como no caso acima de um adaptador de classe de uso de `Resource`, um adaptador de classe de uso de [`SlingHttpServletRequest`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) pode ser especificado na `data-sly-use` declaração. Após a execução, a solicitação atual será adaptada à classe fornecida e o objeto resultante será disponibilizado dentro de HTL.
