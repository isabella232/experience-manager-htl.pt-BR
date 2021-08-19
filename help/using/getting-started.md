---
title: Introdução ao HTL
description: O HTL suportado pelo AEM substitui o JSP como o sistema de modelo preferencial e recomendado do lado do servidor para HTML no AEM.
exl-id: c95eb1b3-3b96-4727-8f4f-d54e7136a8f9
source-git-commit: 8e70ee4921a7ea071ab7e06947824c371f4013d8
workflow-type: ht
source-wordcount: '2471'
ht-degree: 100%

---

# Introdução ao HTL {#getting-started-with-htl}

A Linguagem de modelo HTML (HTL) suportado pelo Adobe Experience Manager (AEM) é o sistema de modelo preferencial e recomendado do lado do servidor para HTML no AEM. Substitui as páginas JSP (JavaServer Pages), conforme usadas em versões anteriores do AEM.

>[!NOTE]
>
>Para executar a maioria dos exemplos fornecidos nesta página, um ambiente de execução ativo chamado [Read Eval Print Loop](https://github.com/Adobe-Marketing-Cloud/aem-htl-repl) pode ser usado.

## HTL em vez de JSP {#htl-over-jsp}

Recomenda-se que novos projetos AEM usem a Linguagem de modelo HTML, pois ela oferece vários benefícios em comparação ao JSP. No entanto, para projetos existentes, a migração só faz sentido se for estimado que o esforço será menor do que manter os JSPs existentes para os próximos anos.

Mas mudar para HTL não é, necessariamente, uma opção radical, pois os componentes gravados em HTL são compatíveis com componentes gravados em JSP ou ESP. Isso significa que os projetos existentes podem usar HTL para novos componentes, mantendo o JSP para os componentes existentes.

Inclusive no mesmo componente, os arquivos HTL podem ser usados junto a JSPs e ESPs. O exemplo a seguir mostra, na **linha 1**, como incluir um arquivo HTL de um arquivo JSP; e na **linha 2**, como um arquivo JSP pode ser incluído de um arquivo HTL:

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

### Perguntas frequentes {#frequently-asked-questions}

Antes de introduzir a Linguagem de modelo HTML, vamos responder algumas perguntas relacionadas ao tópico JSP vs. HTL.

**O HTL tem alguma limitação que o JSP não tem?** - Na verdade, o HTL não tem limitações em comparação com o JSP. O que pode ser feito com o JSP também deve ser realizável com o HTL. No entanto, por design, o HTL é mais rigoroso que o JSP em vários aspectos, e o que pode ser obtido em um único arquivo JSP pode precisar ser separado em uma classe de Java ou em um arquivo JavaScript para ser realizável no HTL. Mas isso é geralmente desejado para garantir uma boa separação de interesses entre a lógica e a marcação.

**O HTL suporta Bibliotecas de tags JSP?** - Não, mas conforme mostrado na seção [Carregamento de bibliotecas de clientes](getting-started.md#loading-client-libraries), as instruções de [modelo e chamada](block-statements.md#template-call) oferecem um padrão semelhante.

**Os recursos do HTL podem ser estendidos em um projeto AEM?** - Não podem. O HTL tem mecanismos de extensão avançados para reutilização da lógica, a [API de uso](getting-started.md#use-api-for-accessing-logic), e de marcação (as instruções [modelo e chamada](block-statements.md#template-call)), que podem ser usadas para modular o código de projetos.

**Quais são os principais benefícios do HTL em relação ao JSP?** - A segurança e a eficiência dos projetos são os principais benefícios, detalhados na [Visão geral](overview.md).

**O JSP vai acabar?** - Na data atual, não há planos nesse sentido.

## Conceitos fundamentais do HTL {#fundamental-concepts-of-htl}

A Linguagem de modelo HTML usa uma linguagem de expressão para inserir partes de conteúdo na marcação renderizada, e atributos de dados HTML5 para definir instruções sobre blocos de marcação (como condições ou iterações). À medida que o HTL é compilado em Servlets Java, as expressões e os atributos de dados HTL são avaliados totalmente no lado do servidor e nada permanece visível no HTML resultante.

### Blocos e expressões {#blocks-and-expressions}

Este é um primeiro exemplo, que pode estar contido como está em um arquivo **`template.html`**:

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

Pode distinguir-se dois tipos diferentes de sintaxes:

* **[Instruções em bloco](block-statements.md)** - para exibir condicionalmente o elemento **&lt;h1>**, um atributo de dados [`data-sly-test`](block-statements.md#test) HTML5 é usado. O HTL fornece vários atributos como esse, que permitem anexar comportamento a qualquer elemento HTML, e todos recebem o prefixo `data-sly`.

* **[Linguagem de expressão](expression-language.md)** - as expressões HTL são delimitadas pelos caracteres `${` e `}`. No tempo de execução, essas expressões são avaliadas e seu valor é inserido no fluxo HTML de saída.

As duas páginas vinculadas acima fornecem a lista detalhada de recursos disponíveis para sintaxe.

### O elemento SLY {#the-sly-element}

Um conceito central de HTL é oferecer a possibilidade de reutilizar elementos HTML existentes para definir instruções em bloco, o que evita a necessidade de inserir delimitadores adicionais para definir onde a instrução começa e termina. Essa anotação não invasiva da marcação para transformar um HTML estático em um modelo dinâmico funcional oferece a vantagem de não romper a validade do código HTML e, portanto, ainda exibir corretamente, mesmo como arquivos estáticos.

No entanto, às vezes, pode não haver um elemento existente no local exato em que uma instrução em bloco deve ser inserida. Para esses casos, é possível inserir um elemento SLY especial, que será removido automaticamente da saída, enquanto executa as instruções em bloco anexadas e exibe seu conteúdo adequadamente.

Assim, o exemplo a seguir:

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

resultará em algo como o HTML abaixo, mas somente se houver uma propriedade **`jcr:title`** e uma propriedade **`jcr:description`** definidas e se nenhuma delas estiver vazia:

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

Lembre-se de somente usar o elemento SLY quando nenhum elemento existente puder ter sido anotado com a instrução em bloco, porque os elementos SLY impedem o valor oferecido pela linguagem de não alterar o HTML estático ao torná-lo dinâmico.

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

### Comentários HTL {#htl-comments}

O exemplo a seguir mostra, na **linha 1** um comentário HTL, e na **linha 2**, um comentário HTML:

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

Os comentários HTL são comentários HTML com uma sintaxe adicional semelhante a JavaScript. O comentário HTL inteiro e qualquer coisa dentro dele será totalmente ignorado pelo processador e removido da saída.

No entanto, o conteúdo dos comentários HTML padrão será transmitido, e as expressões dentro do comentário serão avaliadas.

Comentários HTML não podem conter comentários HTL e vice-versa.

### Contextos especiais {#special-contexts}

Para poder usar melhor o HTL, é importante entender bem as consequências de seu uso ser baseado na sintaxe HTML.

### Nomes de elementos e atributos {#element-and-attribute-names}

As expressões só podem ser colocadas em texto HTML ou valores de atributo, mas não dentro de nomes de elementos ou nomes de atributos, ou não seriam mais HTML válido. Para definir nomes de elementos dinamicamente, a instrução [`data-sly-element`](block-statements.md#element) pode ser usada nos elementos desejados, e para definir dinamicamente nomes de atributos, mesmo configurando vários atributos de uma só vez, a instrução [`data-sly-attribute`](block-statements.md#attribute) pode ser usada.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### Contextos sem instruções em bloco {#contexts-without-block-statements}

Como o HTL usa atributos de dados para definir instruções em bloco, não é possível definir essas instruções dentro dos seguintes contextos, e somente as expressões podem ser usadas lá:

* Comentários HTML
* Elementos de script
* Elementos de estilo

O motivo para isso é que o conteúdo desses contextos é texto e não HTML, e os elementos HTML contidos seriam considerados como dados de caracteres simples. Portanto, sem elementos HTML reais, também não pode haver atributos **`data-sly`** executados.

Pode parecer uma grande restrição, no entanto, é uma restrição desejada, visto que a Linguagem de modelo HTML não deve ser usada para gerar saída que não seja HTML. Abaixo, a seção [API de uso para acessar a lógica](getting-started.md#use-api-for-accessing-logic) apresenta como a lógica adicional pode ser chamada a partir do modelo, que pode ser usada se for necessária para preparar saídas complexas para esses contextos. Por exemplo, uma maneira fácil de enviar dados do back-end para um script de front-end é ter a lógica do componente para gerar uma cadeia de caracteres JSON, que pode ser colocada em um atributo de dados com uma expressão HTL simples.

O exemplo a seguir ilustra o comportamento de comentários HTML, mas em scripts ou elementos de estilo, o mesmo comportamento seria observado:

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

gerará algo como o seguinte HTML:

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### Contextos explícitos necessários {#explicit-contexts-required}

Conforme explicado abaixo, na seção [Escape automático sensível ao contexto](getting-started.md#automatic-context-aware-escaping), um objetivo do HTL é reduzir os riscos de introdução de vulnerabilidades de script entre sites (XSS), aplicando automaticamente o escape com reconhecimento de contexto a todas as expressões. Embora o HTL possa detectar automaticamente o contexto de expressões colocadas dentro da marcação HTML, ele não analisa a sintaxe do JavaScript ou CSS em linha e, portanto, depende do desenvolvedor para especificar explicitamente o contexto exato a ser aplicado a essas expressões.

Não aplicar o escape correto resulta em vulnerabilidades XSS. Por isso, o HTL remove a saída de todas as expressões que estão em contextos de script e estilo, quando o contexto não foi declarado.

Este é um exemplo de como definir o contexto de expressões colocadas dentro de scripts e estilos:

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

Para mais detalhes sobre como controlar o escape, consulte a seção [Contexto de exibição da linguagem de expressão](expression-language.md#display-context).

### Supressão das limitações de contextos especiais {#lifting-limitations-of-special-contexts}

Nos casos especiais em que é necessário ignorar as restrições dos contextos de script, estilo e comentário, é possível isolar o conteúdo em um arquivo HTL separado. Tudo que estiver localizado nesse arquivo será interpretado pelo HTL como um fragmento HTML normal, esquecendo o contexto de limitação do qual ele pode ter sido incluído.

Mais abaixo, consulte a seção [Trabalhar com modelos do lado do cliente](getting-started.md#working-with-client-side-templates), para obter um exemplo.

>[!CAUTION]
>
>Essa técnica pode apresentar vulnerabilidades de script entre sites (XSS), e os aspectos de segurança devem ser cuidadosamente estudados se isso for usado. Geralmente há melhores maneiras de implementar a mesma coisa do que confiar nessa prática.

## Recursos gerais do HTL {#general-capabilities-of-htl}

Esta seção aborda rapidamente os recursos gerais da Linguagem de modelo HTML.

### API de uso para acessar a lógica {#use-api-for-accessing-logic}

Considere o exemplo a seguir:

```xml
<p data-sly-use.logic="logic.js">${logic.title}</p>
```

E o seguinte arquivo JavaScript executado do lado do servidor `logic.js` colocado ao lado dele:

```javascript
use(function () {
    return {
        title: currentPage.getTitle().substring(0, 10) + "..."
    };
});
```

Como a Linguagem de modelo HTML não permite misturar código dentro da marcação, ela oferece o mecanismo de extensão API de uso para executar facilmente o código do modelo.

O exemplo acima usa o JavaScript executado no lado do servidor para reduzir o título para 10 caracteres, mas também poderia ter usado o código Java para fazer o mesmo fornecendo um nome de classe de Java totalmente qualificado. Geralmente, a lógica comercial deve ser incorporada ao Java, mas quando o componente precisa de algumas alterações específicas de exibição do que a API do Java fornece, pode ser conveniente usar algum JavaScript executado do lado do servidor para fazer isso.

Mais informações sobre isso nas seguintes seções:

* A seção na instrução [`data-sly-use` ](block-statements.md#use) explica tudo o que pode ser feito com essa instrução.
* A [página API de uso](use-api.md) fornece algumas informações para ajudar a escolher entre escrever a lógica em Java ou em JavaScript.
* E para detalhar como escrever a lógica, as páginas [API de uso do JavaScript](use-api-javascript.md) e [API de uso do Java](use-api-java.md) podem ajudar.

### Escape automático sensível ao contexto {#automatic-context-aware-escaping}

Considere o exemplo a seguir:

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

Na maioria das linguagens de modelo, esse exemplo poderia ocasionar uma vulnerabilidade de criação de script entre sites (XSS), pois mesmo quando todas as variáveis são automaticamente escapadas por HTML, o atributo `href` ainda deve ter um escape de URL específico. Essa omissão é um dos erros mais comuns, porque pode ser muito facilmente esquecida, e é difícil detectar de forma automatizada.

Para ajudar nisso, a Linguagem de modelo HTML escapa automaticamente cada variável de acordo com o contexto em que é colocada. Isso é possível graças ao fato de que o HTL entende a sintaxe do HTML.

Considerando o seguinte arquivo `logic.js`:

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

Observe como os dois atributos foram escapados de forma diferente, porque o HTL sabe que os atributos `href` e `src` devem ser escapados para o contexto do URI. Além disso, se o URI começasse com **`javascript:`**, o atributo teria sido totalmente removido, a menos que o contexto fosse explicitamente alterado para algo diferente.

Para mais detalhes sobre como controlar o escape, consulte a seção [Contexto de exibição da linguagem de expressão](expression-language.md#display-context).

### Remoção automática de atributos vazios {#automatic-removal-of-empty-attributes}

Considere o exemplo a seguir:

```xml
<p class="${properties.class}">some text</p>
```

Se o valor da propriedade `class` estiver vazio, a Linguagem de modelo HTML removerá automaticamente todo o atributo `class` da saída.

Novamente, isso é possível, porque o HTL entende a sintaxe HTML e, portanto, pode mostrar condicionalmente atributos com valores dinâmicos somente se o valor não estiver vazio. Isso é extremamente conveniente, pois evita adicionar um bloco de condição ao redor dos atributos, o que tornaria a marcação inválida e ilegível.

Além disso, o tipo da variável colocada na expressão é importante:

* **Sequência de caracteres:**
   * **não vazio:** define a cadeia de caracteres como valor de atributo.
   * **vazio:** remove o atributo completamente.

* **Número:** define o valor como valor de atributo.

* **Booleano:**
   * **true:** exibe o atributo sem valor (como um atributo HTML booleano)
   * **false:** remove o atributo completamente.

Este é um exemplo de como uma expressão booleana permitiria controlar um atributo HTML booleano:

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

Para definir atributos, a instrução [`data-sly-attribute`](block-statements.md#attribute) também pode ser útil.

## Padrões comuns com HTL {#common-patterns-with-htl}

Esta seção apresenta alguns cenários comuns e como resolvê-los da melhor maneira com a Linguagem de modelo HTML.

### Carregamento de bibliotecas de clientes {#loading-client-libraries}

No HTL, as bibliotecas do cliente são carregadas por meio de um modelo auxiliar fornecido pelo AEM, que pode ser acessado por meio de [`data-sly-use`](block-statements.md#use). Três modelos estão disponíveis nesse arquivo, que pode ser chamado por meio de [`data-sly-call`](block-statements.md#template-call):

* **`css`** - carrega somente os arquivos CSS das bibliotecas de clientes referenciadas.
* **`js`** - carrega somente os arquivos JavaScript das bibliotecas de clientes referenciadas.
* **`all`** - carrega todos os arquivos das bibliotecas de clientes referenciadas (CSS e JavaScript).

Cada modelo auxiliar espera uma opção **`categories`** para fazer referência às bibliotecas de clientes desejadas. Essa opção pode ser uma matriz de valores de cadeias de caracteres ou uma cadeia contendo uma lista de valores separados por vírgula.

Veja dois exemplos simples:

### Carregamento de várias bibliotecas de clientes de uma só vez {#loading-multiple-client-libraries-fully-at-once}

```xml
<sly data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html"
     data-sly-call="${clientlib.all @ categories=['myCategory1', 'myCategory2']}"/>
```

### Referência a uma biblioteca do cliente em diferentes seções de uma página {#referencing-a-client-library-in-different-sections-of-a-page}

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

No segundo exemplo acima, caso os elementos HTML **`head`** e **`body`** sejam colocados em arquivos diferentes, o modelo **`clientlib.html`** deverá ser carregado em cada arquivo que precisar.

A seção sobre as instruções [modelo e chamada](block-statements.md#template-call) fornece mais detalhes sobre como a declaração e a chamada desses modelos funcionam.

### Enviar dados para o cliente {#passing-data-to-the-client}

A melhor e mais elegante maneira de enviar dados para o cliente em geral, mas ainda mais com HTL, é usar atributos de dados.

O exemplo a seguir mostra como a lógica (que também poderia ser gravada em Java) pode ser usada para serializar para JSON de maneira conveniente o objeto a ser passado para o cliente, que pode ser colocado muito facilmente em um atributo de dados:

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

A partir daí, é fácil imaginar como um JavaScript do lado do cliente pode acessar esse atributo e analisar novamente o JSON. Isso seria, por exemplo, o JavaScript correspondente a ser colocado em uma biblioteca do cliente:

```javascript
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### Trabalhar com modelos do lado do cliente {#working-with-client-side-templates}

Um caso especial, em que a técnica explicada na seção [Supressão das limitações de contextos especiais](getting-started.md#lifting-limitations-of-special-contexts) pode ser usada legitimamente, é escrever modelos do lado do cliente (como Handlebars, por exemplo) que estão localizados em elementos de **script**. Essa técnica poder ser usada com segurança nesse caso, porque o elemento de **script** não contém JavaScript como pressuposto, mas outros elementos HTML. Veja um exemplo de como isso funcionaria:

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

Como mostrado acima, a marcação que será incluída no elemento **`script`** pode conter instruções em bloco HTL, e as expressões não precisam fornecer contextos explícitos, pois o conteúdo do modelo Handlebars foi isolado em seu próprio arquivo. Além disso, este exemplo mostra como o HTL executado do lado do servidor (como no elemento **`h2`**) pode ser misturado com uma linguagem de modelo executada do lado do cliente, como Handlebars (mostrado no elemento **`h3`**).

Uma técnica mais moderna seria, no entanto, usar o elemento HTML **`template`**, cujo benefício é não ser necessário isolar o conteúdo dos modelos em arquivos separados.

**Leia a seguir:**

* [Linguagem de expressão](expression-language.md) - para saber detalhadamente o que isso pode fazer dentro de expressões HTL.
* [Instruções em bloco](block-statements.md) - para descobrir todas as instruções em bloco disponíveis no HTL e como usá-las.
