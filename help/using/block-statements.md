---
title: Instruções em bloco do HTL
description: As instruções em bloco da Linguagem de modelo HTML (HTL) são atributos de dados personalizados adicionados diretamente ao HTML existente.
exl-id: a517dcef-ab7a-4d4c-a1a9-2e57aad034f7
source-git-commit: 89b9e89254f341e74f1a5a7b99735d2e69c8a91e
workflow-type: tm+mt
source-wordcount: '1555'
ht-degree: 99%

---

# Instruções em bloco do HTL {#htl-block-statements}

As instruções em bloco da Linguagem de modelo HTML (HTL) são atributos de `data` personalizados adicionados diretamente ao HTML existente. Isso permite uma anotação simples e não invasiva de um protótipo de página HTML estática, convertendo-a em um modelo dinâmico funcional sem romper a validade do código HTML.

## Visão geral dos blocos {#overview}

Os plug-ins de blocos HTL são definidos pelos atributos `data-sly-*` definidos em elementos HTML. Os elementos podem ter uma tag de fechamento ou se fechar automaticamente. Os atributos podem ter valores (que podem ser cadeias de caracteres estáticas ou expressões), ou simplesmente ser atributos booleanos (sem um valor).

```xml
<tag data-sly-BLOCK></tag>                                 <!--/* A block is simply consists in a data-sly attribute set on an element. */-->
<tag data-sly-BLOCK/>                                      <!--/* Empty elements (without a closing tag) should have the trailing slash. */-->
<tag data-sly-BLOCK="string value"/>                       <!--/* A block statement usually has a value passed, but not necessarily. */-->
<tag data-sly-BLOCK="${expression}"/>                      <!--/* The passed value can be an expression as well. */-->
<tag data-sly-BLOCK="${@ myArg='foo'}"/>                   <!--/* Or a parametric expression with arguments. */-->
<tag data-sly-BLOCKONE="value" data-sly-BLOCKTWO="value"/> <!--/* Several block statements can be set on a same element. */-->
```

Todos os atributos `data-sly-*` avaliados são removidos da marcação gerada.

### Identificadores {#identifiers}

Uma instrução em bloco também pode ser seguida por um identificador:

```xml
<tag data-sly-BLOCK.IDENTIFIER="value"></tag>
```

O identificador pode ser usado pela instrução em bloco de várias maneiras. Veja a seguir alguns exemplos:

```xml
<!--/* Example of statements that use the identifier to set a variable with their result: */-->
<div data-sly-use.navigation="MyNavigation">${navigation.title}</div>
<div data-sly-test.isEditMode="${wcmmode.edit}">${isEditMode}</div>
<div data-sly-list.child="${currentPage.listChildren}">${child.properties.jcr:title}</div>
<div data-sly-template.nav>Hello World</div>

<!--/* The attribute statement uses the identifier to know to which attribute it should apply it's value: */-->
<div data-sly-attribute.title="${properties.jcr:title}"></div> <!--/* This will create a title attribute */-->
```

Os identificadores de nível superior não fazem distinção entre maiúsculas e minúsculas (porque podem ser definidos por atributos HTML, que não fazem essa distinção). Mas todas as propriedades fazem distinção entre maiúsculas e minúsculas.

## Instruções em bloco disponíveis {#available-block-statements}

Há várias instruções em bloco disponíveis. Quando usadas no mesmo elemento, a seguinte lista de prioridade define como as instruções em bloco são avaliadas:

1. `data-sly-template`
1. `data-sly-set`, `data-sly-test`, `data-sly-use`
1. `data-sly-call`
1. `data-sly-text`
1. `data-sly-element`,  `data-sly-include`,  `data-sly-resource`
1. `data-sly-unwrap`
1. `data-sly-list`, `data-sly-repeat`
1. `data-sly-attribute`

Quando duas instruções em bloco têm a mesma prioridade, sua ordem de avaliação é da esquerda para a direita.

### use {#use}

O `data-sly-use` inicializa um objeto auxiliar (definido em JavaScript ou Java) e o expõe por meio de uma variável.

Inicialize um objeto JavaScript, em que o arquivo de origem esteja localizado no mesmo diretório do modelo. Observe que o nome do arquivo deve ser usado:

```xml
<div data-sly-use.nav="navigation.js">${nav.foo}</div>
```

Inicialize uma classe de Java, em que o arquivo de origem esteja localizado no mesmo diretório do modelo. Observe que o nome da classe deve ser usado, não o nome do arquivo:

```xml
<div data-sly-use.nav="Navigation">${nav.foo}</div>
```

Inicialize uma classe de Java, em que essa classe é instalada como parte de um pacote OSGi. Observe que o nome de classe totalmente qualificado deve ser usado:

```xml
<div data-sly-use.nav="org.example.Navigation">${nav.foo}</div>
```

Os parâmetros podem ser passados para a inicialização usando opções. Geralmente, esse recurso deve ser usado somente pelo código HTL, que está contido em um bloco `data-sly-template`:

```xml
<div data-sly-use.nav="${'navigation.js' @parentPage=currentPage}">${nav.foo}</div>
```

Inicialize outro modelo HTL, que pode ser chamado usando `data-sly-call`:

```xml
<div data-sly-use.nav="navTemplate.html" data-sly-call="${nav.foo}"></div>
```

>[!NOTE]
>
>Para mais informações sobre a API de uso, consulte:
>
>* [API de uso do Java](use-api-java.md)
>* [API de uso do JavaScript](use-api-javascript.md)


#### data-sly-use com recursos {#data-sly-use-with-resources}

Permite obter recursos diretamente no HTL com `data-sly-use` e não requer código de gravação para obtê-lo.

Por exemplo:

```xml
<div data-sly-use.product=“/etc/commerce/product/12345”>
  ${ product.title }
</div>
```

>[!TIP]
>
>Consulte também a seção [Caminho nem sempre necessário.](#path-not-required)

### unwrap {#unwrap}

O `data-sly-unwrap` remove o elemento de host da marcação gerada, além de reter o conteúdo. Isso permite a exclusão de elementos necessários como parte da lógica de apresentação HTL, mas que não são desejados na saída efetiva.

No entanto, essa instrução deve ser usada com moderação. Em geral, é melhor manter a marcação HTL o mais próxima possível da marcação de saída desejada. Isto é, ao adicionar instruções em bloco HTL, tente o máximo possível apenas anotar o HTML existente, sem introduzir novos elementos.

Por exemplo, isto

```xml
<p data-sly-use.nav="navigation.js">Hello World</p>
```

Produzirá

```xml
<p>Hello World</p>
```

Ao passo que isto

```xml
<p data-sly-use.nav="navigation.js" data-sly-unwrap>Hello World</p>
```

produzirá

```xml
Hello World
```

Também é possível unwrap condicionalmente um elemento:

```xml
<div class="popup" data-sly-unwrap="${isPopup}">content</div>
```

### set {#set}

O `data-sly-set` define um novo identificador com um valor predefinido.

```xml
<span data-sly-set.profile="${user.profile}">Hello, ${profile.firstName} ${profile.lastName}!</span>
<a class="profile-link" href="${profile.url}">Edit your profile</a>
```

### text {#text}

O `data-sly-text` substitui o conteúdo de seu elemento de host pelo texto especificado.

Por exemplo, isto

```xml
<p>${properties.jcr:description}</p>
```

equivale a

```xml
<p data-sly-text="${properties.jcr:description}">Lorem ipsum</p>
```

Ambos exibirão o valor de `jcr:description` como texto de parágrafo. A vantagem do segundo método é que ele permite a anotação não invasiva de HTML, mantendo o conteúdo estático de espaço reservado do designer original.

### attribute {#attribute}

O `data-sly-attribute` adiciona atributos ao elemento de host.

Por exemplo, isto

```xml
<div title="${properties.jcr:title}"></div>
```

equivale a

```xml
<div title="Lorem Ipsum" data-sly-attribute.title="${properties.jcr:title}"></div>
```

Ambos definirão o atributo `title` para o valor de `jcr:title`. A vantagem do segundo método é que ele permite a anotação não invasiva de HTML, mantendo o conteúdo estático de espaço reservado do designer original.

Os atributos são resolvidos da esquerda para a direita, com a instância mais à direita de um atributo (literal ou definido por `data-sly-attribute`) tendo precedência sobre qualquer instância do mesmo atributo (definido literalmente ou por `data-sly-attribute`) definido à esquerda.

Observe que um atributo (`literal` ou definido por `data-sly-attribute`) cujo valor é avaliado para a cadeia de caracteres vazia será removido na marcação final. A única exceção para essa regra é que um atributo literal, definido como uma cadeia vazia literal será preservado. Por exemplo,

```xml
<div class="${''}" data-sly-attribute.id="${''}"></div>
```

produz

```xml
<div></div>
```

mas

```xml
<div class="" data-sly-attribute.id=""></div>
```

produz

```xml
<div class=""></div>
```

Para definir vários atributos, passe um objeto Map para manter pares de valores chave correspondentes aos atributos e seus valores. Por exemplo, supondo que

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

produz

```xml
<div title="myTitle" class="myClass" id="myId"></div>
```

### element {#element}

O `data-sly-element` substitui o nome do elemento do elemento de host.

Por exemplo,

```xml
<h1 data-sly-element="${titleLevel}">text</h1>
```

substitui `h1` pelo valor de `titleLevel`.

Por motivos de segurança, `data-sly-element` aceita apenas os seguintes nomes de elemento:

```xml
a abbr address article aside b bdi bdo blockquote br caption cite code col colgroup
data dd del dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header i ins
kbd li main mark nav ol p pre q rp rt ruby s samp section small span strong sub
sup table tbody td tfoot th thead time tr u var wbr
```

Para definir outros elementos, a segurança XSS deve ser desativada ( `@context='unsafe'`).

### test {#test}

O `data-sly-test` remove condicionalmente o elemento de host e seu conteúdo. Um valor de `false` remove o elemento; um valor de `true` retém o elemento.

Por exemplo, o elemento `p` e seu conteúdo só serão renderizados se `isShown` for `true`:

```xml
<p data-sly-test="${isShown}">text</p>
```

O resultado de um teste pode ser atribuído a uma variável que pode ser usada posteriormente. Geralmente, isso é usado para construir a lógica &quot;if else&quot;, já que não há instrução “else” explícita:

```xml
<p data-sly-test.abc="${a || b || c}">is true</p>
<p data-sly-test="${!abc}">or not</p>
```

Uma vez definida, a variável tem escopo global dentro do arquivo HTL.

A seguir estão alguns exemplos sobre a comparação de valores:

```xml
<div data-sly-test="${properties.jcr:title == 'test'}">TEST</div>
<div data-sly-test="${properties.jcr:title != 'test'}">NOT TEST</div>

<div data-sly-test="${properties['jcr:title'].length > 3}">Title is longer than 3</div>
<div data-sly-test="${properties['jcr:title'].length >= 0}">Title is longer or equal to zero </div>

<div data-sly-test="${properties['jcr:title'].length > aemComponent.MAX_LENGTH}">
    Title is longer than the limit of ${aemComponent.MAX_LENGTH}
</div>
```

### repeat {#repeat}

Com `data-sly-repeat`, você pode repetir um elemento várias vezes com base na lista especificada.

```xml
<div data-sly-repeat="${currentPage.listChildren}">${item.name}</div>
```

Isso funciona da mesma forma que `data-sly-list`, apenas não é necessário um elemento de contêiner.

O exemplo a seguir mostra que também pode-se referir ao *item* para atributos:

```xml
<div data-sly-repeat="${currentPage.listChildren}" data-sly-attribute.class="${item.name}">${item.name}</div>
```

### list {#list}

`data-sly-list` repete o conteúdo do elemento de host para cada propriedade enumerável no objeto fornecido.

Veja um loop simples:

```xml
<dl data-sly-list="${currentPage.listChildren}">
    <dt>index: ${itemList.index}</dt>
    <dd>value: ${item.title}</dd>
</dl>
```

As variáveis padrão a seguir estão disponíveis no escopo da lista:

* `item`: o item atual na iteração.
* `itemList`: objeto que contém as seguintes propriedades:
* `index`: contador baseado em zero ( `0..length-1`).
* `count`: contador com base em um ( `1..length`).
* `first`: `true` se o item atual for o primeiro item.
* `middle`: `true` se o item atual não for o primeiro nem o último item.
* `last`: `true` se o item atual for o último item.
* `odd`: `true` se `index` for ímpar.
* `even`: `true` se `index` for par.

Definir um identificador na instrução `data-sly-list` permite renomear as variáveis `itemList` e `item`. `item` se tornará `<variable>` e `itemList` se tornará `<variable>List`.

```xml
<dl data-sly-list.child="${currentPage.listChildren}">
    <dt>index: ${childList.index}</dt>
    <dd>value: ${child.title}</dd>
</dl>
```

Também é possível acessar propriedades dinamicamente:

```xml
<dl data-sly-list.child="${myObj}">
    <dt>key: ${child}</dt>
    <dd>value: ${myObj[child]}</dd>
</dl>
```

### resource {#resource}

`data-sly-resource` inclui o resultado da renderização do recurso indicado por meio da resolução de sling e do processo de renderização.

Um recurso simples inclui:

```xml
<article data-sly-resource="path/to/resource"></article>
```

#### Caminho nem sempre necessário {#path-not-required}

Observe que o uso de um caminho com `data-sly-resource` não é necessário se você já tiver o recurso. Se você já tiver o recurso, poderá usá-lo diretamente.

Portanto, o exemplo abaixo está correto.

```xml
<sly data-sly-resource="${resource.path @ decorationTagName='div'}"></sly>
```

No entanto, o exemplo seguinte também é perfeitamente aceitável.

```xml
<sly data-sly-resource="${resource @ decorationTagName='div'}"></sly>
```

É recomendável usar o recurso diretamente quando possível, devido aos seguintes motivos:

* Se você já tiver o recurso, resolver novamente usando o caminho é um trabalho adicional e desnecessário.
* Usar o caminho quando você já tem o recurso pode apresentar resultados inesperados, pois os recursos do Sling podem estar encapsulados ou podem ser sintéticos e não fornecidos no caminho determinado.

#### Opções {#resource-options}

As opções permitem diversas variantes adicionais:

Manipular o caminho do recurso:

```xml
<article data-sly-resource="${ @ path='path/to/resource'}"></article>
<article data-sly-resource="${'resource' @ prependPath='my/path'}"></article>
<article data-sly-resource="${'my/path' @ appendPath='resource'}"></article>
```

Adicionar (ou substituir) um seletor:

```xml
<article data-sly-resource="${'path/to/resource' @ selectors='selector'}"></article>
```

Adicionar, substituir ou remover vários seletores:

```xml
<article data-sly-resource="${'path/to/resource' @ selectors=['s1', 's2']}"></article>
```

Adicionar um seletor aos seletores existentes:

```xml
<article data-sly-resource="${'path/to/resource' @ addSelectors='selector'}"></article>
```

Remover alguns seletores dos seletores existentes:

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors='selector1'}"></article>
```

Remover todos os seletores:

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors}"></article>
```

Substituir o tipo de recurso do recurso:

```xml
<article data-sly-resource="${'path/to/resource' @ resourceType='my/resource/type'}"></article>
```

Alterar o modo WCM:

```xml
<article data-sly-resource="${'path/to/resource' @ wcmmode='disabled'}"></article>
```

Por padrão, as tags de decoração do AEM são desativadas, a opção decorationTagName permite trazê-las de volta e o cssClassName permite adicionar classes a esse elemento.

```xml
<article data-sly-resource="${'path/to/resource' @ decorationTagName='span',
cssClassName='className'}"></article>
```

>[!NOTE]
>
>O AEM oferece lógica simples e clara controlando as tags de decoração que envolvem elementos incluídos. Para mais detalhes, consulte [Tag de decoração](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/implementing/developing/full-stack/components-templates/decoration-tag.html) na documentação de componentes em desenvolvimento.

### include {#include}

`data-sly-include` substitui o conteúdo do elemento de host pela marcação gerada pelo arquivo de modelo HTML indicado (HTL, JSP, ESP etc.) quando ele é processado pelo mecanismo de modelo correspondente. O contexto de renderização do arquivo incluído não incluirá o contexto HTL atual (o do arquivo de inclusão). Consequentemente, para a inclusão de arquivos HTL, o `data-sly-use` atual precisaria ser repetido no arquivo incluído (nesse caso, geralmente é melhor usar `data-sly-template` e `data-sly-call`).

Uma inclusão simples:

```xml
<section data-sly-include="path/to/template.html"></section>
```

JSPs podem ser incluídos da mesma maneira:

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

### Atributos de solicitação {#request-attributes}

Nas `data-sly-include` e `data-sly-resource` você pode passar `requestAttributes` para usá-las no script HTL de recebimento.

Isso permite transmitir parâmetros corretamente em scripts ou componentes.

```xml
<sly data-sly-use.settings="com.adobe.examples.htl.core.hashmap.Settings"
        data-sly-include="${ 'productdetails.html' @ requestAttributes=settings.settings}" />
```

Código Java da classe Settings, o Map é usado para transmitir o requestAttributes:

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

Por exemplo, por meio de um modelo Sling, você pode consumir o valor do `requestAttributes` especificado.

Neste exemplo, o layout é inserido por meio do Map da classe de uso:

```xml
@Model(adaptables=SlingHttpServletRequest.class)
public class ProductSettings {
  @Inject @Optional @Default(values="empty")
  public String layout;

}
```

### template e call {#template-call}

Blocos de modelo podem ser usados como chamadas de função: em sua instrução, eles podem obter parâmetros, que podem ser passados quando chamados. Eles também permitem recursão.

`data-sly-template` define um modelo. O elemento de host e seu conteúdo não são gerados pelo HTL

`data-sly-call` chama um modelo definido com data-sly-template. O conteúdo do modelo chamado (opcionalmente parametrizado) substitui o conteúdo do elemento de host da chamada.

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

Os modelos localizados em um arquivo diferente podem ser inicializados com `data-sly-use`. Observe que, nesse caso, `data-sly-use` e `data-sly-call` também podem ser colocados no mesmo elemento:

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

## Elemento sly {#sly-element}

A tag HTML `<sly>` pode ser usada para remover o elemento atual, permitindo que somente seus filhos sejam exibidos. Sua funcionalidade é semelhante ao elemento de bloco `data-sly-unwrap`:

```xml
<!--/* This will display only the output of the 'header' resource, without the wrapping <sly> tag */-->
<sly data-sly-resource="./header"></sly>
```

Embora não seja uma tag HTML 5 válida, a tag `<sly>` pode ser exibida na saída final usando `data-sly-unwrap`:

```xml
<sly data-sly-unwrap="${false}"></sly> <!--/* outputs: <sly></sly> */-->
```

O objetivo do elemento `<sly>` é tornar mais óbvio que o elemento não seja de saída. Se desejar, ainda poderá usar `data-sly-unwrap`.

Assim como com `data-sly-unwrap`, tente minimizar o uso disso.
