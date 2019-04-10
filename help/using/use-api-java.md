---
title: HTL Java Use-API
seo-title: HTL Java Use-API
description: Linguagem de modelo HTML - HTL - A API de uso de Java habilita um arquivo
  de HTL para acessar métodos de ajuda em uma classe de Java personalizada.
seo-description: Linguagem de modelo HTML - HTL - A API de uso de Java habilita um
  arquivo de HTL para acessar métodos de ajuda em uma classe de Java personalizada.
uuid: b 340 f 8 f 7-a 193-45 c 8-aa 39-5 c 6 e 2 c 0194 EA
contentOwner: Usuário
products: SG_ EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: referência
discoiquuid: 126 ebc 9 d -5 f 7 b -47 a 4-aea 2-c 8840 d 34864 c
mwpw-migration-script-version: 2017-10-12 T 21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 271c355ae56e16e309853b02b8ef09f2ff971a2e

---


# HTL Java Use-API{#htl-java-use-api}

A Linguagem de Modelo HTML (HTL) do Java Use-API ativa um arquivo HTL para acessar métodos de ajuda em uma classe Java personalizada. Isso permite que toda a lógica comercial complexa seja encapsulada no código Java, enquanto o código HTL trata apenas da produção de marcação direta.

## Um exemplo simples {#a-simple-example}

Começaremos com um componente HTL que *não* tem uma classe usada. Ele consiste em um único arquivo, `/apps/my-example/components/info.html`

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html}

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

Também adicionamos algum conteúdo para este componente renderizar em **`/content/my-example/`**:

### `http://localhost:4502/content/my-example.json` {#http-localhost-content-my-example-json}

```java
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

Quando este conteúdo é acessado, o arquivo HTL é executado. No código HTL, usamos o objeto de contexto****`properties`para acessar o recurso atual `title` e `description` exibi-lo. O HTML de saída será:

### `view-source:http://localhost:4502/content/my-example.html` {#view-source-http-localhost-content-my-example-html}

```xml
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Adicionar uma classe de uso {#adding-a-use-class}

O componente **de informações** como ele se encontra não requer uma classe de uso para executar sua função (muito simples). No entanto, há casos em que você precisa fazer coisas que não podem ser feitas no HTL e, portanto, precisam de uma classe de uso. Mas lembre-se do seguinte:

>[!NOTE]
>
>*Uma classe usada só deve ser usada quando algo não puder ser feito somente em HTL.*

Por exemplo, suponha que você queira que `info` o componente exiba as `title`**`description`** propriedades do recurso, mas todas em minúsculas. Como o HTL não tem um método para strings em minúsculas, você precisará de uma classe de utilização. Podemos fazer isso adicionando uma classe de uso do Java e alterando o **`info.html`** seguinte:

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

Nas seções a seguir, vamos percorrer as diferentes partes do código.

### Local de Java local vs Bundle {#local-vs-bundle-java-class}

A classe de uso do Java pode ser instalada de duas formas: **local** ou **pacote**. *Este exemplo usa uma instalação local.*

Em uma instalação local, o arquivo de origem Java é colocado junto com o arquivo HTL, na mesma pasta de repositório. A fonte é automaticamente compilada sob demanda. Nenhuma etapa de compilação ou compactação separada é necessária.

Em uma instalação de pacote, a classe Java deve ser compilada e implantada em um pacote osgi usando o mecanismo de implantação padrão do AEM (consulte [Classe Java embutida](#bundled-java-class)).

>[!NOTE]
>
>Uma classe de uso de Java **local** é recomendada quando a classe use é específica para o componente em questão.
>
>Uma **classe de uso Java usada** é recomendada quando o código Java implementa um serviço acessado de vários componentes HTL.

### O pacote Java é um caminho de repositório {#java-package-is-repository-path}

Quando uma instalação local é usada, o nome do pacote da classe use deve corresponder ao do local da pasta de repositório, com qualquer hífen no caminho substituído por sublinhado no nome do pacote.

Nesse caso **`Info.java`** , está localizado no **`/apps/my-example/components/info`** pacote **`apps.my_example.components.info`** :

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
>O uso de hífens nos nomes dos itens de repositório é uma prática recomendada no AEM developement. No entanto, os hífens são ilegais em nomes de pacotes Java. Por esse motivo, **todos os hífens no caminho de repositório devem ser convertidos em sublinhados no nome do pacote**.

### Extensão `WCMUsePojo`{#extending-wcmusepojo}

Embora haja várias maneiras de incorporar uma classe Java com HTL (consulte Alternativas para `WCMUsePojo`), a mais simples é estender a `WCMUsePojo` classe:

#### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-2}

```java
package apps.my_example.components.info;
 
import com.adobe.cq.sightly.WCMUsePojo;
 
public class Info extends WCMUsePojo {
    
    ...
}
```

### Inicialização da classe {#initializing-the-class}

Quando a classe use é estendida, **`WCMUsePojo`**a inicialização é realizada substituindo o **`activate`** método:

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

Normalmente, o método [de ativação](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html) é usado para pré-computar e armazenar (nas variáveis de membro) os valores necessários no código HTL, com base no contexto atual (a solicitação e o recurso atuais, por exemplo).

`WCMUsePojo` A classe fornece acesso ao mesmo conjunto de objetos de contexto que estão disponíveis em um arquivo HTL (consulte [Objetos globais](global-objects.md)).

Em uma classe que se estende **`WCMUsePojo`**, os objetos de contexto podem ser acessados *por nome* usando

[`<T> T get(String name, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

Como alternativa, objetos de contexto frequentemente usados podem ser acessados diretamente pelo método **de conveniência apropriado**:

|  |
|---|---|
| [Pagemanager](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/PageManager.html) | [Getpagemanager ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageManager()) |
| [Modos](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [Getcurrentpage ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentPage()) |
| [Modos](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [Getresourcepage ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourcePage()) |
| [Valuemap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [Getpageproperties ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageProperties()) |
| [Valuemap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [Getproperties ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getProperties()) |
| [Designer](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [Getdesigner ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getDesigner()) |
| [Design](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Design.html) | [Getcurrentdesign ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentDesign()) |
| [Estilo](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Style.html) | [Getcurrentstyle ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentStyle()) |
| [Componente](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/components/Component.html) | [Getcomponent ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getComponent()) |
| [Valuemap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [Getinheritedproperties ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse#getInheritedProperties.html()) |
| [Recurso](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/Resource.html) | [Getresource ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResource()) |
| [Resourceresolver](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [Getresourceresolver ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourceResolver()) |
| [Slinghttpservletrequest](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [Getrequest ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getRequest()) |
| [Slinghttpservletresponse](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [Getresponse ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResponse()) |
| [Slingscripthelper](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [Getslingscripthelper ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getSlingScriptHelper()) |

### Métodos de Getter {#getter-methods}

Depois que a classe use é inicializada, o arquivo HTL é executado. Durante esse estágio, o HTL normalmente obterá o estado de várias variáveis de membro da classe use e as renderizará para apresentação.

Para fornecer acesso a esses valores a partir do arquivo HTL, é necessário definir métodos getter personalizados na classe use **, de acordo com a seguinte convenção de nomenclatura**:

* Um método do formulário **`getXyz`** será exposto no arquivo HTL uma propriedade de objeto chamada ***xyz***.

Por exemplo, no exemplo a seguir, os métodos **`getTitle`** e **`getDescription`** o resultado nas propriedades do objeto **`title`** e **`description`** se tornam acessíveis no contexto do arquivo HTL:

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

### atributo de data-sly use {#data-sly-use-attribute}

O **`data-sly-use`** atributo é usado para inicializar a classe de uso dentro do código HTL. Em nosso exemplo, o `data-sly-use` atributo declara que queremos usar a classe **`Info`**. Podemos usar apenas o nome local da classe porque estamos usando uma instalação local (tendo colocado o arquivo de origem Java na mesma pasta que o arquivo HTL). Se usarmos uma instalação de pacote, precisaríamos especificar o nome de classe totalmente qualificado (consulte [Instalação](#LocalvsBundleJavaClass)de pacotes de classe).

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-2}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Identificador local {#local-identifier}

O identificador "**`info`**(após o ponto in) **`data-sly-use.info`**é usado no arquivo HTL para identificar a classe. O escopo desse identificador é global dentro do arquivo, depois que ele foi declarado. Não está limitado ao elemento que contém a `data-sly-use` declaração.

### `/apps/my-example/component/info/info.html`{#apps-my-example-component-info-info-html-3}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Obtenção de propriedades {#getting-properties}

O identificador `info` é usado para acessar as propriedades **`title`** do objeto e **`description`** que foram expostas pelos métodos getter `Info.getTitle` e **`Info.getDescription`**.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-4}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Saída {#output}

Agora, ao acessá **`/content/my-example.html`** -la, retornará o seguinte HTML:

### `view-source:http://localhost:4502/content/my-example.html` {#view-source-http-localhost-content-my-example-html-1}

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

## Além das noções básicas {#beyond-the-basics}

Nesta seção, vamos explorar alguns recursos adicionais que vão além do exemplo simples acima:

* Passar parâmetros para uma classe use.
* Classe de uso Java bund.
* Alternativas para `WCMUsePojo`

### Passar parâmetros {#passing-parameters}

Parâmetros podem ser passados para uma classe de uso após a inicialização. Por exemplo, podemos fazer algo desta forma:

### `/content/my-example/component/info/info.html` {#content-my-example-component-info-info-html}

```xml
<div data-sly-use.info="${'Info' @ text='Some text'}">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
    <p>${info.upperCaseText}</p>
</div>
```

Aqui estamos transmitindo um parâmetro chamado **`text`**. The use-class then uppercases the string we retrieve and display the result with `info.upperCaseText`. Esta é a classe de uso ajustada:

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

Em nosso caso, a declaração

`get("text", String.class)`

A string é então revertida e exposta pelo método

**`getReverseText()`**

### Enviar somente parâmetros a partir de um modelo de dados {#only-pass-parameters-from-data-sly-template}

Embora o exemplo acima esteja tecnicamente correto, na verdade não faz muito sentido passar um valor do HTL para inicializar uma classe de uso, quando o valor em questão está disponível no contexto de execução do código HTL (ou, trivialmente, o valor é estático, como acima).

O motivo é que a classe use sempre terá acesso ao mesmo contexto de execução do código HTL. Isso exibe um ponto de importação da melhor prática:

>[!NOTE]
>
>Passar um parâmetro para uma classe use apenas deve ser feito quando a classe use é usada em um arquivo **de modelo de dados que** é chamado de outro arquivo HTL com parâmetros que precisam ser passados.

Por exemplo, vamos criar um arquivo separado `data-sly-template` junto com nosso exemplo existente. Nós chamaremos o novo arquivo `extra.html`. Ele contém **`data-sly-template`** um bloco chamado **`extra`**:

### `/apps/my-example/component/info/extra.html` {#apps-my-example-component-info-extra-html}

```xml
<template data-sly-template.extra="${@ text}"
          data-sly-use.extraHelper="${'ExtraHelper' @ text=text}">
  <p>${extraHelper.reversedText}</p>
</template>
```

The template **`extra`**, takes a single parameter, **`text`**. Em seguida, ela inicializa a classe use `ExtraHelper` Java com o nome local **`extraHelper`** e a transmite o valor do parâmetro modelo **`text`** como o parâmetro **`text`**de classe de uso.

O corpo do modelo recebe a propriedade `extraHelper.reversedText` (que, sob a alça, na verdade chama `ExtraHelper.getReversedText()`) e exibe esse valor.

Também adaptar nosso existente **`info.html`** para usar este novo modelo:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-5}

```xml
<div data-sly-use.info="Info" 
     data-sly-use.extra="extra.html">
    
  <h1>${info.lowerCaseTitle}</h1>
  <p>${info.lowerCaseDescription}</p>
    
  <div data-sly-call="${extra.extra @ text=properties.description}"></div>

</div>
```

O arquivo `info.html` agora contém duas **`data-sly-use`** declarações, o original que importa a **`Info`** classe de uso do Java e uma nova que importa o arquivo de modelo sob o nome `extra`local.

Observe que podíamos ter colocado o bloco de modelo dentro do **`info.html`** arquivo para evitar o segundo **`data-sly-use`**, mas um arquivo de modelo separado é mais comum e reutilizável.

A **`Info`** classe é empregada como antes, chamando seus métodos **`getLowerCaseTitle()`** getter e `getLowerCaseDescription()` pelas propriedades HTL correspondentes `info.lowerCaseTitle` e **`info.lowerCaseDescription`**.

Em seguida, realizamos o **`data-sly-call`** modelo **`extra`** e enviamos o valor `properties.description` como o parâmetro **`text`**.

A classe de uso do Java é `Info.java` alterada para lidar com o novo parâmetro de texto:

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

O **`text`** parâmetro é recuperado, **`get("text", String.class)`**o valor é revertido e disponibilizado como objeto HTL `reversedText` por meio do getter `getReversedText()`.

### Classe Java embutida {#bundled-java-class}

Com uma classe de uso de pacote, a classe deve ser compilada, empacotada e implantada no AEM usando o mecanismo padrão de implantação do pacote osgi. Em contraste com uma instalação local, a declaração **do pacote de classe usada** deve ser nomeada normalmente:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-6}

```java
package org.example.app.components;
 
import com.adobe.cq.sightly.WCMUsePojo;
 
public class Info extends WCMUsePojo {
    ...
}
```

e, a `data-sly-use` declaração deve fazer referência ao *nome de classe totalmente qualificado*, ao contrário do nome da classe local:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-6}

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```

### Alternativas para `WCMUsePojo`{#alternatives-to-wcmusepojo}

A maneira mais comum de criar uma classe de uso de Java é estender `WCMUsePojo`. No entanto, há várias outras opções. Para compreender essas variantes, ajuda a compreender como a `data-sly-use` declaração HTL funciona sob a aleta.

Suponha que você tenha a `data-sly-use` seguinte declaração:

**`<div data-sly-use.`** `localName`**`="`** `UseClass`**`">`**

O sistema processa a declaração da seguinte maneira:

(1)

* Se existir um arquivo local *useclass. java* no mesmo diretório que o arquivo HTL, tente compilar e carregar essa classe. Se for bem-sucedido, vá para (2).

* Caso contrário, interprete *useclass* como um nome de classe totalmente qualificado e tente carregá-lo a partir do ambiente osgi. Se for bem-sucedido, vá para (2).
* Caso contrário, interprete *useclass* como um caminho para um arquivo HTL ou javascript e carregue esse arquivo. Caso tenha êxito (4).

(2)

* Tente adaptar a atual **`Resource`** em *`UseClass.`* caso de êxito, vá para (3).

* Otherwise, try to adapt the current **`Request`** to *`UseClass`*. Se for o caso, vá para (3).

* Caso contrário, tente instanciar *`UseClass`* com um construtor de argumento zero. Se for o caso, vá para (3).

(3)

* Dentro do HTL, vincule o objeto recentemente adaptado ou criado ao nome *`localName`*.
* Se *`UseClass`* implantar [`io.sightly.java.api.Use`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html) , chama o `init` método, passando o contexto de execução atual (na forma de um `javax.scripting.Bindings` objeto).

(4)

* Se *`UseClass`* for um caminho para um arquivo HTL contendo a `data-sly-template`, prepare o modelo.

* Caso contrário, se *`UseClass`* for um caminho para uma classe de uso do javascript, prepare a classe use (consulte [Usar Javascript Use-API](use-api-javascript.md)).

Alguns pontos significativos sobre a descrição acima:

* Qualquer classe adaptável de `Resource`, adaptável ou `Request`com um construtor de argumento zero pode ser uma classe usada. A classe não precisa estender estender `WCMUsePojo` ou até implementar `Use`.

* No entanto, se a classe usar ** for implementada `Use`, seu **`init`** método será chamado automaticamente com o contexto atual, permitindo que você coloque o código de inicialização que depende desse contexto.

* Uma classe de uso que se estende `WCMUsePojo` é apenas um caso especial de implementação **`Use`**. Fornece os métodos de contexto de conveniência e seu **`activate`** método é chamado automaticamente `Use.init`.

### Implementar diretamente a utilização da interface {#directly-implement-interface-use}

Embora a maneira mais comum de criar uma classe use seja estendida `WCMUsePojo`, também é possível implementar diretamente a `[io.sightly.java.api.Use](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html)`própria interface.

A `Use` interface define apenas um método:

`[public void init(javax.script.Bindings bindings)](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use#init(javax.script.Bindings))`

O **`init`** método será chamado na inicialização da classe com um **`Bindings`** objeto que mantém todos os objetos de contexto e qualquer parâmetro passado para a classe de uso.

Toda funcionalidade adicional (como o equivalente a `WCMUsePojo.getProperties()`) deve ser implmentada explicitamente usando ` [javax.script.Bindings](http://docs.oracle.com/javase/7/docs/api/javax/script/Bindings.html)` o objeto. Por exemplo:

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

O caso principal para implementar a `Use` interface em vez de estender `WCMUsePojo` é quando você deseja usar uma subclasse de uma classe já existente como a classe de uso.

### Adaptável a partir do recurso {#adaptable-from-resource}

Outra opção é usar uma classe helper com **`org.apache.sling.api.resource.Resource`**base em uma tabela adaptável.

Considere que é necessário gravar um script HTL que exibe o mimetype de um ativo DAM. Nesse caso, você sabe que quando seu script HTL é chamado, ele estará no contexto de um **`Resource`** JCR **`Node`** com nó **`dam:Asset`**.

Você sabe que um **`dam:Asset`** nó tem uma estrutura como esta:

### Estrutura de repositório {#repository-structure}

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

Aqui, mostramos o ativo (uma imagem JPEG) que vem com uma instalação padrão do AEM como parte do projeto de exemplo geometrixx. O ativo é chamado **`jane_doe.jpg`** e seu mimetype é **`image/jpeg`**.

Para acessar o ativo no HTL, é possível declarar ` [com.day.cq.dam.api.Asset](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/granite/asset/api/Asset.html)` como a classe na **`data-sly-use`** declaração: e, em seguida, use um método get para **`Asset`** recuperar as informações desejadas. Por exemplo:

### `mimetype.html` {#mimetype-html}

```xml
<div data-sly-use.asset="com.day.cq.dam.api.Asset">
  <p>${asset.mimeType}</p>
</div>
```

A `data-sly-use` declaração direciona o HTL para adaptar a atual **`Resource`** a um **`Asset`** e dar a ele o nome **`asset`**local. Em seguida, chama o **`getMimeType`** método de `Asset` uso do HTL getter shorthand: `asset.mimeType`.

### Adaptável a partir da solicitação {#adaptable-from-request}

Também é possível definir como uma classe de uso qualquer classe adaptável de **` [org.apache.sling.api.SlingHttpServletRequest](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html)`**

Como ocorre com o caso acima de uma tabela adaptável de utilização de classe `Resource`, é [`SlingHttpServletRequest`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) possível especificar uma tabela de adaptador de classe use na `data-sly-use` declaração. Na execução, a solicitação atual será adaptada à classe fornecida e o objeto resultante será disponibilizado dentro do HTL.
