---
title: Declarações de bloco HTL
seo-title: Declarações de bloco HTL
description: As declarações de bloco HTML do Modelo HTML são atributos de dados personalizados
  adicionados diretamente ao HTML existente.
seo-description: As declarações de bloco HTML do Modelo HTML são atributos de dados
  personalizados adicionados diretamente ao HTML existente.
uuid: 0624 fb 6 e -6989-431 b-aabc -1138325393 f 1
contentOwner: Usuário
products: SG_ EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: referência
discoiquuid: 58 aa 6 ea 8-1 d 45-4 f 6 f-a 77 e -4819 f 593 a 19 d
mwpw-migration-script-version: 2017-10-12 T 21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 7a94b0b010461b29d2b74c9c717e3b218d0ca5a8

---


# Declarações de bloco HTL {#htl-block-statements}

As declarações de bloco HTML do Modelo HTML são atributos personalizados `data` adicionados diretamente ao HTML existente. Isso permite a anotação fácil e não segura de uma página HTML estática de protótipo, convertendo-a em um modelo dinâmico funcional sem quebrar a validade do código HTML.

## elemento sly {#sly-element}

A **& amp; lt; sly & amp; gt; não** é exibido no HTML resultante e pode ser usado em vez do desvincular dos dados. A meta da & amp; lt; sly & amp; gt; é tornar mais óbvio que o elemento não tem saída. Se desejar que você ainda possa usar os dados com detecção de big data.

```xml
<sly data-sly-test.varone="${properties.yourProp}"/>
```

Assim como com a remoção de dados, tente minimizar o uso desse recurso.

## use {#use}

**`data-sly-use`**: Inicia um objeto helper (definido em javascript ou Java) e o expõe por meio de uma variável.

Inicialize um objeto javascript, onde o arquivo de origem está localizado no mesmo diretório que o modelo. Observe que o nome do arquivo deve ser usado:

```xml
<div data-sly-use.nav="navigation.js">${nav.foo}</div>
```

Inicialize uma classe Java, onde o arquivo de origem está localizado no mesmo diretório que o modelo. Observe que o nome da classe deve ser usado e não o nome do arquivo:

```xml
        <div data-sly-use.nav="Navigation">${nav.foo}</div>
```

Inicialize uma classe Java, onde essa classe é instalada como parte de um pacote osgi. Observe que o nome de classe totalmente qualificado deve ser usado:

```xml
        <div data-sly-use.nav="org.example.Navigation">${nav.foo}</div>
```

Os parâmetros podem ser enviados para a inicialização usando *opções*. Geralmente, esse recurso só deve ser usado pelo código HTL que está em `data-sly-template` um bloco:

```xml
<div data-sly-use.nav="${'navigation.js' @parentPage=currentPage}">${nav.foo}</div>
```

Inicialize outro modelo HTL que pode ser chamado usando **`data-sly-call`**:

```xml
<div data-sly-use.nav="navTemplate.html" data-sly-call="${nav.foo}"></div>
```

>[!NOTE]
>
>Para obter mais informações sobre a utilização da API, consulte:
>
>* [API de uso Java](use-api-java.md)
>* [Javascript Use-API](use-api-javascript.md)
>



## desvincular {#unwrap}

**`data-sly-unwrap`**: Remove o elemento host da marcação gerada enquanto mantém o conteúdo. Isso permite a exclusão de elementos necessários como parte da lógica de apresentação HTL, mas não são desejados na saída real.

No entanto, essa declaração deve ser usada com moderação. Em geral, é melhor manter a marcação HTL o mais próximo possível da marcação de saída pretendida. Em outras palavras, ao adicionar instruções de bloco HTL, tente o máximo possível para anotar o HTML existente, sem inserir novos elementos.

Por exemplo, este

```xml
<p data-sly-use.nav="navigation.js">Hello World</p>
```

produzirá

```xml
<p>Hello World</p>
```

Enquanto isso,

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

**`data-sly-text`**: Substitui o conteúdo do elemento host pelo texto especificado.

Por exemplo, este

```xml
<p>${properties.jcr:description}</p>
```

equivale a

```xml
<p data-sly-text="${properties.jcr:description}">Lorem ipsum</p>
```

Ambos exibirão o valor de **`jcr:description`** texto de parágrafo. A vantagem do segundo método é permitir a anotação não segura de HTML enquanto mantém o conteúdo de espaço reservado estático do designer original.

## atributo {#attribute}

**data-sly-attribute**: Adiciona atributos ao elemento de host.

Por exemplo, este

```xml
<div title="${properties.jcr:title}"></div>
```

equivale a

```xml
<div title="Lorem Ipsum" data-sly-attribute.title="${properties.jcr:title}"></div>
```

Both will set the `title` attribute to the value of **`jcr:title`**. A vantagem do segundo método é permitir a anotação não segura de HTML enquanto mantém o conteúdo de espaço reservado estático do designer original.

Os atributos são resolvidos da esquerda para a direita, com a instância extrema de um atributo (literal ou definido por **`data-sly-attribute`**) tendo precedência sobre qualquer instância do mesmo atributo (definida literalmente ou por meio **`data-sly-attribute`**) definida como sua esquerda.

Observe que um atributo (ou **`literal`** definido por **`data-sly-attribute`**) cujo valor *resulta* na string vazia será removido na marcação final. A exceção a essa regra é que um atributo *literal* definido como uma string vazia *literal* será preservado. Por exemplo,

```xml
<div class="${''}" data-sly-attribute.id="${''}"></div>
```

produz,

```xml
<div></div>
```

mas,

```xml
<div class="" data-sly-attribute.id=""></div>
```

produz,

```xml
<div class=""></div>
```

Para definir vários atributos, passe um objeto de mapa com pares de valores chave correspondentes aos atributos e seus valores. Por exemplo, supondo que,

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

produz,

```xml
<div title="myTitle" class="myClass" id="myId"></div>
```

## elemento {#element}

**`data-sly-element`**: Substitui o nome do elemento do elemento host.

Por exemplo,

```xml
<h1 data-sly-element="${titleLevel}">text</h1>
```

Substitui o **`h1`** valor de **`titleLevel`**.

Por motivos de segurança, `data-sly-element` aceita apenas os seguintes nomes de elemento:

```xml
a abbr address article aside b bdi bdo blockquote br caption cite code col colgroup
data dd del dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header i ins
kbd li main mark nav ol p pre q rp rt ruby s samp section small span strong sub 
sup table tbody td tfoot th thead time tr u var wbr
```

Para definir outros elementos, a segurança XSS deve ser desativada ( `@context='unsafe'`).

## testar {#test}

**`data-sly-test`:** Remove condicionalmente o elemento host e o conteúdo. O valor `false` remove o elemento; um valor `true` de reter o elemento.

Por exemplo, o `p` elemento e seu conteúdo serão renderizados apenas se `isShown` for `true`:

```xml
<p data-sly-test="${isShown}">text</p>
```

O resultado de um teste pode ser atribuído a uma variável que pode ser usada posteriormente. Geralmente, isso é usado para construir a lógica "if else", uma vez que não há nenhuma declaração explícita:

```xml
<p data-sly-test.abc="${a || b || c}">is true</p>
<p data-sly-test="${!abc}">or not</p>
```

A variável, uma vez definida, tem escopo global dentro do arquivo HTL.

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

Com a repetição de dados, é possível *repetir* um elemento várias vezes com base na lista especificada.

```xml
<div data-sly-repeat="${currentPage.listChildren}">${item.name}</div>
```

Isso funciona da mesma forma que a lista de dados, exceto que você não precisa de um elemento de contêiner.

O exemplo a seguir mostra que você também pode se referir ao *item* para atributos:

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

As variáveis padrão a seguir estão disponíveis no escopo da lista:

**`item`**: O item atual na iteração.

**`itemList`**: Objeto que contém as seguintes propriedades:

**`index`**: contador baseado em zero ( `0..length-1`).

**`count`**: contador baseado em um ( `1..length`).

`first`: `true` se o item atual for o primeiro item.

**`middle`**: `true` se o item atual não for o primeiro nem o último item.

**`last`**: `true` se o item atual for o último item.

**`odd`**: `true` se `index` for ímpar.

**`even`**: `true` se `index` for até mesmo.

A definição de um identificador na `data-sly-list` declaração permite que você renomeie as variáveis **`itemList`** e `item` variáveis. **`item`** será*** `<variable>`*** e **`itemList`** será **`*<variable>*List`**.

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

Adicione, substitua ou remova vários seletores:

```xml
<article data-sly-resource="${'path/to/resource' @ selectors=['s1', 's2']}"></article>
```

Adicione um seletor para os atuais:

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

Altera o modo de WCM:

```xml
<article data-sly-resource="${'path/to/resource' @ wcmmode='disabled'}"></article>
```

Por padrão, as tags de decoração do AEM são desativadas, a opção decorationtagname permite trazê-las de volta e o cssclassname para adicionar classes a esse elemento.

```xml
<article data-sly-resource="${'path/to/resource' @ decorationTagName='span',
cssClassName='className'}"></article>
```

>[!NOTE]
>
>O AEM oferece uma lógica clara e simples que controla as tags de decoração que envolvem os elementos incluídos. Para obter detalhes, consulte [Tag de decoração](https://helpx.adobe.com/experience-manager/6-4/sites/developing/using/decoration-tag.html) na documentação de componentes de desenvolvimento.

## include {#include}

**`data-sly-include`**: Substitui o conteúdo do elemento host pela marcação gerada pelo arquivo de modelo HTML indicado (HTL, JSP, ESP etc.) quando é processado pelo mecanismo de modelo correspondente. O contexto de renderização *do arquivo* incluído não incluirá o contexto HTL atual (o do arquivo *incluindo*); Consequentemente, para inclusão de arquivos HTL, a atual **`data-sly-use`** deve ser repetida no arquivo incluído (nesse caso, geralmente é melhor usar **`data-sly-template`** e `data-sly-call`)

Uma simples inclusão:

```xml
<section data-sly-include="path/to/template.html"></section>
```

Os jsps podem ser incluídos da mesma forma:

```xml
<section data-sly-include="path/to/template.jsp"></section>
```

As opções permitem manipular o caminho do arquivo:

```xml
<section data-sly-include="${ @ file='path/to/template.html'}"></section>
<section data-sly-include="${'template.html' @ prependPath='my/path'}"></section>
<section data-sly-include="${'my/path' @ appendPath='template.html'}"></section>
```

Também é possível alterar o modo de WCM:

```xml
<section data-sly-include="${'template.html' @ wcmmode='disabled'}"></section>
```

## modelo e chamada {#template-call}

`data-sly-template`: Define um modelo. O elemento host e seu conteúdo não são exibidos por HTL

`data-sly-call`: Chama um modelo definido com o modelo de dados. O conteúdo do modelo chamado (opcionalmente paramterterterado) substitui o conteúdo do elemento host da chamada.

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

Os modelos localizados em um arquivo diferente podem ser inicializados `data-sly-use`. Nesse caso `data-sly-use` , e `data-sly-call` também pode ser colocada no mesmo elemento:

```xml
<div data-sly-use.lib="templateLib.html">
    <div data-sly-call="${lib.one}"></div>
    <div data-sly-call="${lib.two @ title=properties.jcr:title}"></div>
</div>
```

Recursão de modelo é suportada:

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

## i 18 n e objetos de localidade {#i-n-and-locale-objects}

Ao usar i 18 n e HTL, agora você pode passar também objetos de localidade personalizados.

```xml
${'Hello World' @ i18n, locale=request.locale}
```

## Manipulação de URL {#url-manipulation}

Um novo conjunto de manipulações de url está disponível.

Consulte os seguintes exemplos sobre o uso:

Adiciona a extensão html a um caminho.

```xml
<a href="${item.path @ extension = 'html'}">${item.name}</a>
```

Adiciona a extensão html e um seletor para um caminho.

```xml
<a href="${item.path @ extension = 'html', selectors='products'}">${item.name}</a>
```

Adiciona a extensão html e um fragmento (# value) a um caminho.

```xml
<a href="${item.path @ extension = 'html', fragment=item.name}">${item.name}</a>
```

## Recursos HTL compatíveis com o AEM 6.3 {#htl-features-supported-in-aem}

Os novos recursos HTL a seguir são compatíveis com o Adobe Experience Manager (AEM) 6.3:

### Formatação de número/data {#number-date-formatting}

O AEM 6.3 é compatível com formatação nativa de números e datas, sem gravar código personalizado. Isso também é compatível com o fuso horário e a localidade.

Os exemplos a seguir mostram que o formato é especificado primeiro, em seguida, o valor que precisa de formatação:

```xml
<h2>${ 'dd-MMMM-yyyy hh:mm:ss' @
           format=currentPage.lastModified,
           timezone='PST',
           locale='fr'}</h2>

<h2>${ '#.00' @ format=300}</h2>
```

>[!NOTE]
>
>Para obter detalhes completos sobre o formato que pode ser usado, consulte [Especificação HTL](https://github.com/Adobe-Marketing-Cloud/htl-spec/blob/master/SPECIFICATION.md).

### utilização de dados com recursos {#data-sly-use-with-resources}

Isso permite obter recursos diretamente em HTL com uso de dados e não requer gravação de código para obter o recurso.

Por exemplo:

```xml
<div data-sly-use.product=“/etc/commerce/product/12345”>
  ${ product.title }
</div>
```

### Atributos de solicitação {#request-attributes}

No recurso *de inclusão de dados e* *de dados,* agora é possível passar *requestattributes* para usá-los no script HTL receptor.

Isso permite que você coloque parâmetros apropriadamente em scripts ou componentes.

```xml
<sly data-sly-use.settings="com.adobe.examples.htl.core.hashmap.Settings" 
        data-sly-include="${ 'productdetails.html' @ requestAttributes=settings.settings}" />
```

Código Java da classe Configurações, o Mapa é usado para enviar os requestattributes:

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

Por exemplo, por meio de um Modelo de Sling, você pode consumir o valor dos requestattributes especificados.

Neste exemplo, *o layout* é inserido por meio do Mapa da classe Use:

```xml
@Model(adaptables=SlingHttpServletRequest.class)
public class ProductSettings {
  @Inject @Optional @Default(values="empty")
  public String layout;

}
```

### Correção para @ extension {#fix-for-extension}

A extensão @ funciona em todos os cenários no AEM 6.3, antes que você possa ter um resultado como *www.adobe.com.ht ml* e também verifica se deseja adicionar ou não a extensão.

```xml
${ link @ extension = 'html' }
```
