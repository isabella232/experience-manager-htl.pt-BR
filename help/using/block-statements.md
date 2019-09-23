---
title: Declarações em bloco HTL
seo-title: Declarações em bloco HTL
description: Instruções de bloco HTML Template Language (HTL) são atributos de dados personalizados adicionados diretamente ao HTML existente.
seo-description: 'Instruções de bloco HTML Template Language (HTL) são atributos de dados personalizados adicionados diretamente ao HTML existente. '
uuid: 0624fb6e-6989-431b-aabc-1138325393f1
contentOwner: Usuário
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: referência
discoiquuid: 58aa6ea8-1d45-4f6f-a77e-4819f593a19d
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: afc29cbad83caeb549097da3fc33fd9147f1157a

---


# Declarações em bloco HTL {#htl-block-statements}

Instruções de bloco HTML Template Language (HTL) são atributos personalizados adicionados diretamente `data` ao HTML existente. Isso permite a anotação fácil e discreta de uma página HTML estática de protótipo, convertendo-a em um modelo dinâmico funcional sem quebrar a validade do código HTML.

## elemento sly {#sly-element}

O elemento **** &lt;sly&gt; não é exibido no HTML resultante e pode ser usado em vez da quebra automática de dados. O objetivo do elemento &lt;sly&gt; é tornar mais óbvio que o elemento não foi publicado. Se desejar, você ainda poderá usar a quebra automática de dados.

```xml
<sly data-sly-test.varone="${properties.yourProp}"/>
```

Assim como com a quebra automática de dados, tente minimizar o uso disso.

## use {#use}

**`data-sly-use`**: Inicializa um objeto auxiliar (definido em JavaScript ou Java) e o expõe por meio de uma variável.

Inicialize um objeto JavaScript, onde o arquivo de origem está localizado no mesmo diretório do modelo. Observe que o nome do arquivo deve ser usado:

```xml
<div data-sly-use.nav="navigation.js">${nav.foo}</div>
```

Inicialize uma classe Java, onde o arquivo de origem está localizado no mesmo diretório do modelo. Observe que o nome da classe deve ser usado, não o nome do arquivo:

```xml
        <div data-sly-use.nav="Navigation">${nav.foo}</div>
```

Inicialize uma classe Java, onde essa classe é instalada como parte de um pacote OSGi. Observe que seu nome de classe totalmente qualificado deve ser usado:

```xml
        <div data-sly-use.nav="org.example.Navigation">${nav.foo}</div>
```

Parâmetros podem ser passados para a inicialização usando *opções*. Geralmente, esse recurso deve ser usado somente pelo código HTL que está em um `data-sly-template` bloco:

```xml
<div data-sly-use.nav="${'navigation.js' @parentPage=currentPage}">${nav.foo}</div>
```

Inicialize outro modelo HTL que pode ser chamado usando **`data-sly-call`**:

```xml
<div data-sly-use.nav="navTemplate.html" data-sly-call="${nav.foo}"></div>
```

>[!NOTE]
>
>Para obter mais informações sobre a Use-API, consulte:
>
>* [Java Use-API](use-api-java.md)
>* [JavaScript Use-API](use-api-javascript.md)
>



## desenrolar {#unwrap}

**`data-sly-unwrap`**: Remove o elemento host da marcação gerada enquanto mantém seu conteúdo. Isso permite a exclusão de elementos necessários como parte da lógica de apresentação HTL, mas que não são desejados na saída real.

No entanto, esta afirmação deve ser usada com moderação. Em geral, é melhor manter a marcação HTL o mais próximo possível da marcação de saída pretendida. Em outras palavras, ao adicionar instruções de bloqueio HTL, tente o máximo possível para simplesmente anotar o HTML existente, sem introduzir novos elementos.

Por exemplo, este

```xml
<p data-sly-use.nav="navigation.js">Hello World</p>
```

produzirá

```xml
<p>Hello World</p>
```

Considerando que,

```xml
<p data-sly-use.nav="navigation.js" data-sly-unwrap>Hello World</p>
```

produzirá

```xml
Hello World
```

Também é possível desvincular condicionalmente um elemento:

```xml
<div class="popup" data-sly-unwrap="${isPopup}">content</div>
```

## text {#text}

**`data-sly-text`**: Substitui o conteúdo de seu elemento host pelo texto especificado.

Por exemplo, este

```xml
<p>${properties.jcr:description}</p>
```

equivale a

```xml
<p data-sly-text="${properties.jcr:description}">Lorem ipsum</p>
```

Ambos exibirão o valor de **`jcr:description`** como texto de parágrafo. A vantagem do segundo método é que permite a anotação discreta do HTML, além de manter o conteúdo estático de espaço reservado do designer original.

## attribute {#attribute}

**atributo** de sly de dados: Adiciona atributos ao elemento host.

Por exemplo, este

```xml
<div title="${properties.jcr:title}"></div>
```

equivale a

```xml
<div title="Lorem Ipsum" data-sly-attribute.title="${properties.jcr:title}"></div>
```

Ambos definirão o `title` atributo com o valor de **`jcr:title`**. A vantagem do segundo método é que permite a anotação discreta do HTML, além de manter o conteúdo estático de espaço reservado do designer original.

Os atributos são resolvidos da esquerda para a direita, com a instância mais à direita de um atributo (literal ou definido via **`data-sly-attribute`**) tendo precedência sobre qualquer instância do mesmo atributo (definido literalmente ou via **`data-sly-attribute`**) definido à esquerda.

Observe que um atributo (seja **`literal`** ou definido por **`data-sly-attribute`**) cujo valor *seja avaliado* para a string vazia será removido na marcação final. A única exceção a essa regra é que um atributo *literal* definido para uma string vazia *literal* será preservado. Por exemplo,

```xml
<div class="${''}" data-sly-attribute.id="${''}"></div>
```

produza,

```xml
<div></div>
```

mas,

```xml
<div class="" data-sly-attribute.id=""></div>
```

produza,

```xml
<div class=""></div>
```

Para definir vários atributos, passe um objeto de mapa para manter pares de valores chave correspondentes aos atributos e seus valores. Por exemplo, assumindo,

```xml
attrMap = {
    title: "myTitle",
    class: "myClass",
    id: "myId"
}
```

Então,

```xml
<div data-sly-attribute="${attrMap}"></div>
```

produza,

```xml
<div title="myTitle" class="myClass" id="myId"></div>
```

## elemento {#element}

**`data-sly-element`**: Substitui o nome do elemento do elemento host.

Por exemplo,

```xml
<h1 data-sly-element="${titleLevel}">text</h1>
```

Substitui o **`h1`** pelo valor de **`titleLevel`**.

Por motivos de segurança, `data-sly-element` aceita apenas os seguintes nomes de elementos:

```xml
a abbr address article aside b bdi bdo blockquote br caption cite code col colgroup
data dd del dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header i ins
kbd li main mark nav ol p pre q rp rt ruby s samp section small span strong sub 
sup table tbody td tfoot th thead time tr u var wbr
```

Para definir outros elementos, a segurança XSS deve ser desativada ( `@context='unsafe'`).

## testar {#test}

**`data-sly-test`** : Remove condicionalmente o elemento host e seu conteúdo. Um valor de `false` remove o elemento; um valor de `true` retém o elemento.

Por exemplo, o `p` elemento e seu conteúdo serão renderizados somente se `isShown` for `true`:

```xml
<p data-sly-test="${isShown}">text</p>
```

O resultado de um teste pode ser atribuído a uma variável que pode ser usada posteriormente. Normalmente, isso é usado para construir a lógica "if else", já que não há outra declaração explícita:

```xml
<p data-sly-test.abc="${a || b || c}">is true</p>
<p data-sly-test="${!abc}">or not</p>
```

Uma vez definida, a variável tem escopo global dentro do arquivo HTL.

Veja a seguir alguns exemplos de comparação de valores:

```xml
<div data-sly-test="${properties.jcr:title == 'test'}">TEST</div>
<div data-sly-test="${properties.jcr:title != 'test'}">NOT TEST</div>

<div data-sly-test="${properties['jcr:title'].length > 3}">Title is longer than 3</div>
<div data-sly-test="${properties['jcr:title'].length >= 0}">Title is longer or equal to zero </div> 

<div data-sly-test="${properties['jcr:title'].length > aemComponent.MAX_LENGTH}">
    Title is longer than the limit of ${aemComponent.MAX_LENGTH}
</div>
```

## repeat {#repeat}

Com a repetição do envio de dados, é possível *repetir* um elemento várias vezes com base na lista especificada.

```xml
<div data-sly-repeat="${currentPage.listChildren}">${item.name}</div>
```

Isso funciona da mesma forma que a lista à esquerda de dados, exceto que você não precisa de um elemento de contêiner.

O exemplo a seguir mostra que você também pode fazer referência ao *item* dos atributos:

```xml
<div data-sly-repeat="${currentPage.listChildren}" data-sly-attribute.class="${item.name}">${item.name}</div>
```

## list {#list}

**`data-sly-list`**: Repete o conteúdo do elemento host para cada propriedade enumerável no objeto fornecido.

Este é um loop simples:

```xml
<dl data-sly-list="${currentPage.listChildren}">
    <dt>index: ${itemList.index}</dt>
    <dd>value: ${item.title}</dd>
</dl>
```

As seguintes variáveis padrão estão disponíveis no escopo da lista:

**`item`**: O item atual na iteração.

**`itemList`**: Objeto com as seguintes propriedades:

**`index`**: contador com base em zero ( `0..length-1`).

**`count`**: contador com base em um ( `1..length`).

`first`: `true` se o item atual for o primeiro item.

**`middle`**: `true` se o item atual não for o primeiro nem o último item.

**`last`**: `true` se o item atual for o último item.

**`odd`**: `true` se `index` for estranho.

**`even`**: `true` se `index` for par.

A definição de um identificador na `data-sly-list` instrução permite renomear as variáveis **`itemList`** e `item` . **`item`** será *** `<variable>`*** e **`itemList`** se tornará **`*<variable>*List`**.

```xml
<dl data-sly-list.child="${currentPage.listChildren}">
    <dt>index: ${childList.index}</dt>
    <dd>value: ${child.title}</dd>
</dl>
```

Também é possível acessar as propriedades dinamicamente:

```xml
<dl data-sly-list.child="${myObj}">
    <dt>key: ${child}</dt>
    <dd>value: ${myObj[child]}</dd>
</dl>
```

## recurso {#resource}

**`data-sly-resource`**: Inclui o resultado da renderização do recurso indicado por meio da resolução de sling e do processo de renderização.

Um recurso simples inclui:

```xml
<article data-sly-resource="path/to/resource"></article>
```

As opções permitem várias variantes adicionais:

Manipular o caminho do recurso:

```xml
<article data-sly-resource="${ @ path='path/to/resource'}"></article>
<article data-sly-resource="${'resource' @ prependPath='my/path'}"></article>
<article data-sly-resource="${'my/path' @ appendPath='resource'}"></article>
```

Adicione (ou substitua) um seletor:

```xml
<article data-sly-resource="${'path/to/resource' @ selectors='selector'}"></article>
```

Adicionar, substituir ou remover vários seletores:

```xml
<article data-sly-resource="${'path/to/resource' @ selectors=['s1', 's2']}"></article>
```

Adicione um seletor aos existentes:

```xml
<article data-sly-resource="${'path/to/resource' @ addSelectors='selector'}"></article>
```

Remova alguns seletores dos existentes:

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors='selector1'}"></article>
```

Remova todos os seletores:

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors}"></article>
```

Substitui o tipo de recurso do recurso:

```xml
<article data-sly-resource="${'path/to/resource' @ resourceType='my/resource/type'}"></article>
```

Altera o modo WCM:

```xml
<article data-sly-resource="${'path/to/resource' @ wcmmode='disabled'}"></article>
```

Por padrão, as tags de decoração do AEM são desativadas, a opção decorationTagName permite trazê-las de volta e o cssClassName para adicionar classes a esse elemento.

```xml
<article data-sly-resource="${'path/to/resource' @ decorationTagName='span',
cssClassName='className'}"></article>
```

>[!NOTE]
>
>O AEM oferece lógica clara e simples controlando as tags de decoração que envolvem elementos incluídos. Para obter detalhes, consulte [Marca](https://helpx.adobe.com/experience-manager/6-4/sites/developing/using/decoration-tag.html) de decoração na documentação dos componentes em desenvolvimento.

## include {#include}

**`data-sly-include`**: Substitui o conteúdo do elemento host pela marcação gerada pelo arquivo de modelo HTML indicado (HTL, JSP, ESP etc.) quando for processado pelo mecanismo de modelo correspondente. O contexto de renderização do arquivo ** incluído não incluirá o contexto HTL atual (o contexto do arquivo ** incluído); Consequentemente, para a inclusão de arquivos HTL, a corrente **`data-sly-use`** teria que ser repetida no arquivo incluído (nesse caso, normalmente é melhor usar **`data-sly-template`** e `data-sly-call`)

Uma simples inclusão:

```xml
<section data-sly-include="path/to/template.html"></section>
```

Os JSPs podem ser incluídos da mesma maneira:

```xml
<section data-sly-include="path/to/template.jsp"></section>
```

As opções permitem manipular o caminho do arquivo:

```xml
<section data-sly-include="${ @ file='path/to/template.html'}"></section>
<section data-sly-include="${'template.html' @ prependPath='my/path'}"></section>
<section data-sly-include="${'my/path' @ appendPath='template.html'}"></section>
```

Você também pode alterar o modo WCM:

```xml
<section data-sly-include="${'template.html' @ wcmmode='disabled'}"></section>
```

## modelo &amp; chamada {#template-call}

`data-sly-template`: Define um modelo. O elemento host e seu conteúdo não são exibidos por HTL

`data-sly-call`: Chama um modelo definido com modelo de envio de dados. O conteúdo do modelo chamado (opcionalmente parametrizado) substitui o conteúdo do elemento host da chamada.

Defina um modelo estático e chame-o:

```xml
<template data-sly-template.one>blah</template>
<div data-sly-call="${one}"></div>
```

Defina um modelo dinâmico e chame-o com parâmetros:

```xml
<template data-sly-template.two="${ @ title}"><h1>${title}</h1></template>
<div data-sly-call="${two @ title=properties.jcr:title}"></div>
```

Os modelos localizados em um arquivo diferente podem ser inicializados com `data-sly-use`. Observe que, nesse caso, `data-sly-use` e também `data-sly-call` pode ser colocado no mesmo elemento:

```xml
<div data-sly-use.lib="templateLib.html">
    <div data-sly-call="${lib.one}"></div>
    <div data-sly-call="${lib.two @ title=properties.jcr:title}"></div>
</div>
```

A recursão de modelo é suportada:

```xml
<template data-sly-template.nav="${ @ page}">
    <ul data-sly-list="${page.listChildren}">
        <li>
            <div class="title">${item.title}</div>
            <div data-sly-call="${nav @ page=item}" data-sly-unwrap></div>
        </li>
    </ul>
</template>
<div data-sly-call="${nav @ page=currentPage}" data-sly-unwrap></div>
```

## objetos i18n e locale {#i-n-and-locale-objects}

Ao usar o i18n e o HTL, agora você também pode enviar objetos de localidade personalizados.

```xml
${'Hello World' @ i18n, locale=request.locale}
```

## Manipulação de URL {#url-manipulation}

Um novo conjunto de manipulações de url está disponível.

Consulte os seguintes exemplos de uso:

Adiciona a extensão html a um caminho.

```xml
<a href="${item.path @ extension = 'html'}">${item.name}</a>
```

Adiciona a extensão html e um seletor a um caminho.

```xml
<a href="${item.path @ extension = 'html', selectors='products'}">${item.name}</a>
```

Adiciona a extensão html e um fragmento (#value) a um caminho.

```xml
<a href="${item.path @ extension = 'html', fragment=item.name}">${item.name}</a>
```

## Recursos HTL suportados no AEM 6.3 {#htl-features-supported-in-aem}

Os novos recursos HTL a seguir são suportados no Adobe Experience Manager (AEM) 6.3:

### Formatação de números/datas {#number-date-formatting}

O AEM 6.3 oferece suporte à formatação nativa de números e datas, sem a gravação de código personalizado. Isso também suporta fuso horário e localidade.

Os exemplos a seguir mostram que o formato é especificado primeiro e o valor que precisa de formatação:

```xml
<h2>${ 'dd-MMMM-yyyy hh:mm:ss' @
           format=currentPage.lastModified,
           timezone='PST',
           locale='fr'}</h2>

<h2>${ '#.00' @ format=300}</h2>
```

>[!NOTE]
>
>Para obter detalhes completos sobre o formato que você pode usar, consulte a especificação [](https://github.com/Adobe-Marketing-Cloud/htl-spec/blob/master/SPECIFICATION.md)HTL.

### uso inteligente de dados com recursos {#data-sly-use-with-resources}

Isso permite obter recursos diretamente em HTL com uso inteligente de dados e não requer a gravação de código para obter o recurso.

Por exemplo:

```xml
<div data-sly-use.product=“/etc/commerce/product/12345”>
  ${ product.title }
</div>
```

### Atributos de solicitação {#request-attributes}

No *data-sly-include* e no *data-sly-resource* , agora é possível passar *requestAttributes* para usá-los no script HTL de recebimento.

Isso permite que você passe parâmetros corretamente para scripts ou componentes.

```xml
<sly data-sly-use.settings="com.adobe.examples.htl.core.hashmap.Settings" 
        data-sly-include="${ 'productdetails.html' @ requestAttributes=settings.settings}" />
```

Código Java da classe Settings, o Mapa é usado para passar em requestAttributes:

```xml
public class Settings extends WCMUsePojo {

  // used to pass is requestAttributes to data-sly-resource
  public Map<String, Object> settings = new HashMap<String, Object>();

  @Override
  public void activate() throws Exception {
    settings.put("layout", "flex");
  }
}
```

Por exemplo, por meio de um Sling-Model, é possível consumir o valor de requestAttributes especificado.

Neste exemplo, o *layout* é inserido pelo Mapa da classe Use:

```xml
@Model(adaptables=SlingHttpServletRequest.class)
public class ProductSettings {
  @Inject @Optional @Default(values="empty")
  public String layout;

}
```

### Correção para @extension {#fix-for-extension}

A extensão @funciona em todos os cenários no AEM 6.3, antes que você possa ter um resultado como *www.adobe.com.html* e também verifique se a extensão deve ser adicionada ou não.

```xml
${ link @ extension = 'html' }
```
