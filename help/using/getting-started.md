---
title: Introdução ao HTL
description: O HTL suportado pelo AEM substitui o JSP como o sistema de modelo preferencial e recomendado do lado do servidor para HTML no AEM.
translation-type: tm+mt
source-git-commit: ee712ef61018b5e05ea052484e2a9a6b12e6c5c8
workflow-type: tm+mt
source-wordcount: '2490'
ht-degree: 0%

---


# Introdução ao HTL {#getting-started-with-htl}

O HTML Template Language (HTL) suportado pelo Adobe Experience Manager (AEM) é o sistema de modelo preferencial e recomendado do lado do servidor para HTML no AEM. Ele ocupa o lugar do JSP (JavaServer Pages), conforme usado em versões anteriores do AEM.

>[!NOTE]
>
>Para executar a maioria dos exemplos fornecidos nesta página, é possível usar um ambiente de execução ao vivo chamado Loop [de impressão](https://github.com/Adobe-Marketing-Cloud/aem-htl-repl) Leitura Eval.

## HTL sobre JSP {#htl-over-jsp}

Recomenda-se que os novos projetos do AEM usem a Linguagem de modelo HTML, já que oferta vários benefícios em relação ao JSP. No entanto, no caso dos projetos existentes, a migração só faz sentido se se estimar que seja menos esforço do que manter os JSP existentes para os próximos anos.

Mas mudar para HTL não é necessariamente uma opção de tudo ou nada, porque os componentes escritos em HTL são compatíveis com componentes escritos em JSP ou ESP. Isso significa que os projetos existentes podem, sem problemas, usar o HTL para novos componentes, mantendo o JSP para os componentes existentes.

Mesmo dentro do mesmo componente, os arquivos HTL podem ser usados junto com JSPs e ESPs. O exemplo a seguir mostra na **linha 1** como incluir um arquivo HTL de um arquivo JSP e na **linha 2** como um arquivo JSP pode ser incluído de um arquivo HTL:

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

### Perguntas frequentes{#frequently-asked-questions}

Antes de começar a usar a Linguagem de modelo HTML, vamos nos start de responder antecipadamente a algumas perguntas relacionadas ao tópico JSP vs HTL.

**O HTL tem alguma limitação que o JSP não tem?** - Na verdade, o HTL não tem limitações em relação ao JSP, no sentido de que o que pode ser feito com o JSP também deve ser possível com o HTL. No entanto, o HTL é mais estrito do que o JSP em vários aspectos, e o que pode ser alcançado em um único arquivo JSP pode precisar ser separado em uma classe Java ou em um arquivo JavaScript para ser alcançável em HTL. Mas isso é geralmente desejado para garantir uma boa separação de preocupações entre a lógica e a marcação.

**O HTL suporta as bibliotecas de tags JSP?** - Não, mas como mostrado na seção [Carregando bibliotecas](getting-started.md#loading-client-libraries) clientes, as instruções [template &amp; call](block-statements.md#template-call) ofertas um padrão semelhante.

**Os recursos HTL podem ser estendidos em um projeto do AEM?** Não, não podem. O HTL tem poderosos mecanismos de extensão para reutilizar a lógica - a [Use-API](getting-started.md#use-api-for-accessing-logic) - e de marcação (as declarações de [modelo e chamada](block-statements.md#template-call) ), que podem ser usadas para modular o código de projetos.

**Quais são os principais benefícios do HTL em relação ao JSP?** - A segurança e a eficiência dos projetos são os principais benefícios, que estão detalhados na [visão geral](overview.md).

**O JSP eventualmente vai embora?** - Na data atual, não existem planos nesse sentido.

## Conceitos fundamentais do HTL {#fundamental-concepts-of-htl}

A Linguagem de modelo HTML usa uma linguagem de expressão para inserir partes do conteúdo na marcação renderizada e os atributos de dados HTML5 para definir instruções sobre blocos de marcação (como condições ou iterações). À medida que HTL é compilado em Servlets Java, as expressões e os atributos de dados HTL são avaliados totalmente no lado do servidor e nada permanece visível no HTML resultante.

### Blocos e Expressões {#blocks-and-expressions}

Este é um primeiro exemplo, que pode ser contido como está em um **`template.html`** arquivo:

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

Podem distinguir-se dois tipos diferentes de sintaxes:

* **[Instruções](block-statements.md)**em bloco - para exibir condicionalmente o elemento **&lt;h1>**, um atributo de dados[`data-sly-test`](block-statements.md#test)HTML5 é usado. HTL fornece vários atributos desse tipo, que permitem anexar o comportamento a qualquer elemento HTML, e todos recebem o prefixo`data-sly`.

* **[Idioma](expression-language.md)**da Expressão - as expressões HTL são delimitadas por caracteres`${`e`}`. No tempo de execução, essas expressões são avaliadas e seu valor é inserido no fluxo HTML de saída.

As duas páginas vinculadas acima fornecem a lista detalhada dos recursos disponíveis para sintaxe.

### O elemento SLY {#the-sly-element}

Um conceito central do HTL é oferta da possibilidade de reutilizar elementos HTML existentes para definir instruções em bloco, o que evita a necessidade de inserir delimitadores adicionais para definir onde a instrução start e termina. Essa anotação discreta da marcação para transformar um HTML estático em um modelo dinâmico em funcionamento oferta a vantagem de não quebrar a validade do código HTML e, portanto, ainda exibir corretamente, mesmo como arquivos estáticos.

No entanto, às vezes, pode não haver um elemento existente no local exato em que uma declaração de bloco deve ser inserida. Para esses casos, é possível inserir um elemento SLY especial que será removido automaticamente da saída, enquanto executam as declarações de bloqueio anexadas e exibem seu conteúdo de acordo.

Assim, o exemplo a seguir:

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

resultará em algo como o seguinte HTML, mas somente se houver uma propriedade definida **`jcr:title`** **`jcr:description`** e uma propriedade definida, e se nenhuma delas estiver vazia:

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

Uma coisa a ser lembrada é usar somente o elemento SLY quando nenhum elemento existente poderia ter sido anotado com a declaração de bloco, pois os elementos SLY dissuadem o valor oferecido pelo idioma para não alterar o HTML estático ao torná-lo dinâmico.

Por exemplo, se o exemplo anterior já tivesse sido encapsulado dentro de um elemento DIV, o elemento SLY adicionado seria abusivo:

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

e o elemento DIV poderia ter sido anotado com a condição:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

>[!NOTE]
>
>O elemento SLY foi introduzido com o AEM 6.1 ou HTL 1.1.
>
>Antes disso, era necessário utilizar o [`data-sly-unwrap`](block-statements.md) atributo.

### Comentários HTL {#htl-comments}

O exemplo a seguir mostra na **linha 1** um comentário HTL e na **linha 2** um comentário HTML:

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

Os comentários HTL são comentários HTML com uma sintaxe adicional semelhante a JavaScript. O comentário HTL inteiro, e qualquer coisa dentro, será totalmente ignorado pelo processador e removido da saída.

No entanto, o conteúdo dos comentários HTML padrão será transmitido e as expressões dentro do comentário serão avaliadas.

Comentários HTML não podem conter comentários HTL e vice-versa.

### Contextos especiais {#special-contexts}

Para poder usar o HTL da melhor maneira, é importante entender bem as consequências de sua utilização na sintaxe HTML.

### Nomes de elementos e atributos {#element-and-attribute-names}

As Expressões só podem ser colocadas em texto HTML ou valores de atributo, mas não em nomes de elementos ou nomes de atributos, ou não seriam mais HTML válido. Para definir nomes de elementos dinamicamente, a [`data-sly-element`](block-statements.md#element) instrução pode ser usada nos elementos desejados e para definir dinamicamente nomes de atributos, mesmo configurando vários atributos ao mesmo tempo, a [`data-sly-attribute`](block-statements.md#attribute) instrução pode ser usada.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### Contextos sem declarações de bloqueio {#contexts-without-block-statements}

Como o HTL usa atributos de dados para definir declarações de bloco, não é possível definir essas declarações de bloco dentro dos seguintes contextos, e apenas expressões podem ser usadas lá:

* Comentários HTML
* Elementos de script
* Elementos de estilo

O motivo para isso é que o conteúdo desses contextos é texto e não HTML, e os elementos HTML contidos seriam considerados como dados de caractere simples. Portanto, sem elementos HTML reais, também não pode haver **`data-sly`** atributos executados.

Isso pode parecer uma grande restrição, no entanto, é uma restrição desejada, pois a Linguagem de modelo HTML não deve ser abusada para gerar saída que não seja HTML. A seção [Use-API para acessar lógica](getting-started.md#use-api-for-accessing-logic) abaixo apresenta como a lógica adicional pode ser chamada do modelo, que pode ser usada se for necessária para preparar a saída complexa para esses contextos. Por exemplo, uma maneira fácil de enviar dados do back-end para um script front-end é ter a lógica do componente para gerar uma string JSON, que pode ser colocada em um atributo de dados com uma expressão HTL simples.

O exemplo a seguir ilustra o comportamento de comentários HTML, mas em elementos de script ou estilo, o mesmo comportamento seria observado:

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

resultará em algo como o seguinte HTML:

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### Contextos explícitos necessários {#explicit-contexts-required}

Como explicado na seção Escaping [](getting-started.md#automatic-context-aware-escaping) automático sensível ao contexto abaixo, um dos objetivos do HTL é reduzir os riscos de introdução de vulnerabilidades de scripts entre sites (XSS) aplicando automaticamente o escape sensível ao contexto a todas as expressões. Embora o HTL possa detectar automaticamente o contexto de expressões inseridas dentro da marcação HTML, ele não analisa a sintaxe do JavaScript ou CSS em linha e, portanto, depende do desenvolvedor para especificar explicitamente qual contexto exato deve ser aplicado a essas expressões.

Como não aplicar os resultados de escape corretos em vulnerabilidades XSS, o HTL remove a saída de todas as expressões que estão em contextos de script e estilo quando o contexto não foi declarado.

Este é um exemplo de como definir o contexto para expressões inseridas dentro de scripts e estilos:

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

Para obter mais detalhes sobre como controlar o escape, consulte a seção Contexto [de Exibição de Idioma de](expression-language.md#display-context) Expressão.

### Limitações de remoção de Contextos especiais {#lifting-limitations-of-special-contexts}

Nos casos especiais em que é necessário ignorar as restrições dos contextos de script, estilo e comentário, é possível isolar o conteúdo em um arquivo HTL separado. Tudo o que estiver localizado em seu próprio arquivo será interpretado por HTL como um fragmento HTML normal, esquecendo o contexto de limitação do qual ele pode ter sido incluído.

Consulte a seção [Trabalhando com modelos](getting-started.md#working-with-client-side-templates) do lado do cliente mais abaixo para ver um exemplo.

>[!CAUTION]
>
>Essa técnica pode introduzir vulnerabilidades de script entre sites (XSS), e os aspectos de segurança devem ser cuidadosamente estudados se isso for usado. Geralmente, há melhores maneiras de implementar a mesma coisa do que contar com essa prática.

## Capacidades gerais de HTL {#general-capabilities-of-htl}

Esta seção aborda rapidamente os recursos gerais da Linguagem de modelo HTML.

### Use-API para acessar a lógica {#use-api-for-accessing-logic}

Considere o seguinte exemplo:

```xml
<p data-sly-use.logic="logic.js">${logic.title}</p>
```

E depois do arquivo JavaScript executado no servidor colocado ao lado dele: `logic.js`

```javascript
use(function () {
    return {
        title: currentPage.getTitle().substring(0, 10) + "..."
    };
});
```

Como a Linguagem de modelo HTML não permite misturar código dentro da marcação, ela oferta o mecanismo de extensão Use-API para executar facilmente o código do modelo.

O exemplo acima usa o JavaScript executado no servidor para encurtar o título para 10 caracteres, mas também pode ter usado o código Java para fazer o mesmo fornecendo um nome de classe Java totalmente qualificado. Geralmente, a lógica comercial deve ser incorporada no Java, mas quando o componente precisa de algumas alterações específicas da visualização do que a API Java fornece, pode ser conveniente usar algum JavaScript executado pelo servidor para fazer isso.

Saiba mais sobre isso nas seguintes seções:

* A seção sobre a [`data-sly-use` declaração](block-statements.md#use) explica tudo o que pode ser feito com essa declaração.
* A página [](use-api.md) Use-API fornece algumas informações para ajudar a escolher entre escrever a lógica em Java ou JavaScript.
* E para detalhar como escrever a lógica, as páginas [JavaScript Use-API](use-api-javascript.md) e [Java Use-API](use-api-java.md) devem ajudar.

### Fuga automática sensível ao contexto {#automatic-context-aware-escaping}

Considere o seguinte exemplo:

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

Na maioria das linguagens de modelo, esse exemplo poderia criar uma vulnerabilidade de script entre sites (XSS), pois mesmo quando todas as variáveis são automaticamente escapadas por HTML, o `href` atributo ainda deve ter uma saída específica por URL. Essa omissão é um dos erros mais comuns, porque pode ser muito facilmente esquecida, e é difícil de detectar de forma automatizada.

Para ajudar nisso, a Linguagem de modelo HTML automaticamente escapa cada variável de acordo com o contexto em que é colocada. Isso é possível graças ao fato de que o HTL entende a sintaxe do HTML.

Assumindo o seguinte `logic.js` arquivo:

```javascript
use(function () {
    return {
        link:  "#my link's safe",
        title: "my title's safe",
        text:  "my text's safe"
    };
});
```

O exemplo inicial resultará na seguinte saída:

```xml
<p>
    <a href="#my%20link%27s%20safe" title="my title&#39;s safe">
        my text&#39;s safe
    </a>
</p>
```

Observe como os dois atributos foram escapados de forma diferente, porque o HTL sabe que `href` e `src` os atributos devem ser escapados para o contexto do URI. Além disso, se o URI começasse com **`javascript:`**, o atributo teria sido removido totalmente, a menos que o contexto fosse explicitamente alterado para outro.

Para obter mais detalhes sobre como controlar o escape, consulte a seção Contexto [de Exibição de Idioma de](expression-language.md#display-context) Expressão.

### Remoção automática de atributos vazios {#automatic-removal-of-empty-attributes}

Considere o seguinte exemplo:

```xml
<p class="${properties.class}">some text</p>
```

Se o valor da `class` propriedade estiver vazio, o Linguagem de modelo HTML removerá automaticamente todo o `class` atributo da saída.

Novamente, isso é possível, pois o HTL entende a sintaxe HTML e, portanto, pode mostrar condicionalmente atributos com valores dinâmicos somente se seu valor não estiver vazio. Isso é extremamente conveniente, pois evita adicionar um bloco de condição ao redor dos atributos, o que tornaria a marcação inválida e ilegível.

Além disso, o tipo da variável colocada na expressão é importante:

* **Sequência de caracteres:**
   * **não vazio:** Define a string como valor de atributo.
   * **vazio:** Remove completamente o atributo.

* **Número:** Define o valor como valor de atributo.

* **Booleano:**
   * **true:** Exibe o atributo sem valor (como um atributo HTML booliano)
   * **false:** Remove completamente o atributo.

Este é um exemplo de como uma expressão Booleana permitiria controlar um atributo HTML booliano:

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

Para definir atributos, a [`data-sly-attribute`](block-statements.md#attribute) instrução também pode ser útil.

## Padrões comuns com HTL {#common-patterns-with-htl}

Esta seção apresenta alguns cenários comuns e como resolvê-los melhor com a Linguagem de modelo HTML.

### Carregando bibliotecas de clientes {#loading-client-libraries}

Em HTL, as bibliotecas do cliente são carregadas por meio de um modelo auxiliar fornecido pelo AEM, que pode ser acessado por meio [`data-sly-use`](block-statements.md#use). Três modelos estão disponíveis neste arquivo, que pode ser chamado por meio de [`data-sly-call`](block-statements.md#template-call):

* **`css`** - Carrega somente os arquivos CSS das bibliotecas de clientes referenciadas.
* **`js`** - Carrega somente os arquivos JavaScript das bibliotecas de clientes referenciadas.
* **`all`** - Carrega todos os arquivos das bibliotecas de cliente referenciadas (CSS e JavaScript).

Cada modelo de ajuda espera uma **`categories`** opção para fazer referência às bibliotecas de clientes desejadas. Essa opção pode ser uma matriz de valores de string ou uma string contendo uma lista de valores separados por vírgula.

Estes são dois pequenos exemplos:

### Carregando várias bibliotecas de clientes de uma só vez {#loading-multiple-client-libraries-fully-at-once}

```xml
<sly data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html"
     data-sly-call="${clientlib.all @ categories=['myCategory1', 'myCategory2']}"/>
```

### Referência a uma biblioteca de cliente em diferentes seções de uma página {#referencing-a-client-library-in-different-sections-of-a-page}

```xml
<!doctype html>
<html data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html">
    <head>
        <!-- HTML meta-data -->
        <sly data-sly-call="${clientlib.css @ categories='myCategory'}"/>
    </head>
    <body>
        <!-- page content -->
        <sly data-sly-call="${clientlib.js @ categories='myCategory'}"/>
    </body>
</html>
```

No segundo exemplo acima, caso o HTML **`head`** e os elementos **`body`** sejam colocados em arquivos diferentes, o **`clientlib.html`** modelo deverá ser carregado em cada arquivo que precisar dele.

A seção sobre as declarações de [modelo e chamada](block-statements.md#template-call) fornece mais detalhes sobre como a declaração e a chamada desses modelos funcionam.

### Transmissão de dados para o cliente {#passing-data-to-the-client}

A melhor e mais elegante maneira de passar dados para o cliente em geral, mas ainda mais com HTL, é usar atributos de dados.

O exemplo a seguir mostra como a lógica (que também pode ser gravada em Java) pode ser usada para serializar para JSON de maneira muito conveniente o objeto que deve ser passado para o cliente, que pode ser colocado facilmente em um atributo de dados:

```xml
<!--/* template.html file: */-->
<div data-sly-use.logic="logic.js" data-json="${logic.json}">...</div>
```

```javascript
/* logic.js file: */
use(function () {
    var myData = {
        str: "foo",
        arr: [1, 2, 3]
    };

    return {
        json: JSON.stringify(myData)
    };
});
```

A partir daí, é fácil imaginar como um JavaScript do lado do cliente pode acessar esse atributo e analisar novamente o JSON. Por exemplo, este seria o JavaScript correspondente a ser colocado em uma biblioteca do cliente:

```javascript
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### Trabalhar com modelos do lado do cliente {#working-with-client-side-templates}

Um caso especial, em que a técnica explicada na seção [Levantamento de limitações de Contextos](getting-started.md#lifting-limitations-of-special-contexts) especiais pode ser legitimamente usada, é escrever modelos do lado do cliente (como Handlebars, por exemplo) localizados em elementos de **script** . A razão pela qual essa técnica pode ser usada com segurança nesse caso é porque o elemento de **script** não contém JavaScript como pressuposto, mas outros elementos HTML. Eis um exemplo de como isso funcionaria:

```xml
<!--/* template.html file: */-->
<script id="entry-section" type="text/template"
    data-sly-include="entry-section.html"></script>

<!--/* entry-section.html file: */-->
<div class="entry-section">
    <h2 data-sly-test="${properties.entrySectionTitle}">
        ${properties.entrySectionTitle}
    </h2>
    {{#each entry}}<h3><a href="{{link}}">{{title}}</a></h3>{{/each}}
</div>
```

Como mostrado acima, a marcação que será incluída no **`script`** elemento pode conter declarações de bloco HTL e as expressões não precisam fornecer contextos explícitos, pois o conteúdo do modelo Handlebars foi isolado em seu próprio arquivo. Além disso, este exemplo mostra como o servidor executou o HTL (como no **`h2`** elemento) pode ser misturado com uma linguagem de modelo executada do lado do cliente, como o Handlebars (mostrado no **`h3`** elemento).

No entanto, uma técnica mais moderna seria usar o **`template`** elemento HTML, já que a vantagem seria que ele não precisaria isolar o conteúdo dos modelos em arquivos separados.

**Leia a seguir:**

* [Idioma](expression-language.md) da Expressão - para saber em detalhes o que isso pode ser feito dentro das expressões HTL.
* [Bloquear declarações](block-statements.md) - para descobrir todas as declarações em bloco disponíveis em HTL e como usá-las.
