---
title: Introdução ao HTL
seo-title: Introdução ao HTL
description: O HTL compatível com o AEM assume o lugar do JSP como o sistema de modelo
  do lado do servidor recomendado e recomendado para HTML no AEM.
seo-description: O Linguagem de modelo HTML - HTL - compatível com o Adobe Experience
  Manager assume o local do JSP como o sistema de modelo do lado do servidor preferencial
  e recomendado para HTML no AEM.
uuid: 4 a 7 d 6748-8 cdf -4280-a 85 d -6 c 5319 abf 487
content-type: referência
topic-tags: introdução
discoiquuid: 3 bf 2 ca 75-0 d 68-489 d-bd 1 c -1 d 4 fd 730 c 61 a
mwpw-migration-script-version: 2017-10-12 T 21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 796c55d3d85e6b5a3efaa5c04a25be1b0b4e54dd

---


# Introdução ao HTL {#getting-started-with-htl}

O HTML Template Language (HTL) suportado pelo Adobe Experience Manager (AEM) assume o lugar do JSP (javaserver Pages) como o sistema de modelo de modelo de HTML preferido e recomendado para HTML.

>[!NOTE]
>
>Para executar a maioria dos exemplos fornecidos nesta página, é possível usar um ambiente de execução ativa chamado [Loop](https://github.com/Adobe-Marketing-Cloud/aem-htl-repl) de impressão eval.
>
>A Comunidade do AEM gerou uma série de [artigos, vídeos e webinars](related-community-articles.md) relacionados ao uso de HTL.

## HTL em JSP {#htl-over-jsp}

Recomenda-se que os novos projetos do AEM usem o Linguagem de modelo HTML, pois oferece vários benefícios em comparação com o JSP. No entanto, para projetos existentes, uma migração só faz sentido se for estimada para menor esforço do que manter os jsps atuais para os próximos anos.

Mas mover para HTL não é necessariamente uma escolha tudo, porque os componentes escritos no HTL são compatíveis com componentes escritos no JSP ou ESP. Ou seja, os projetos existentes podem sem problemas usar HTL para novos componentes, mantendo o JSP para componentes existentes.

Mesmo no mesmo componente, os arquivos HTL podem ser usados junto com jsps e esps. O exemplo a seguir mostra na **linha 1** como incluir um arquivo HTL a partir de um arquivo JSP e na **linha 2** como um arquivo JSP pode ser incluído a partir de um arquivo HTL:

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

### Perguntas frequentes{#frequently-asked-questions}

Antes de começar a usar o Idioma do modelo HTML, vamos começar com a resposta à frente algumas perguntas relacionadas ao tópico JSP vs HTL.

**O HTL possui limitações que o JSP não possui?**O HTL não tem realmente limitações em comparação ao JSP, pois o que pode ser feito com JSP também deve ser possível com HTL. No entanto, o HTL é por padrão mais restrito que o JSP em vários aspectos e o que pode ser obtido em um único arquivo JSP, pode ser necessário separar em uma classe Java ou um arquivo javascript para ser possível no HTL. No entanto, isso geralmente é desejável para garantir uma boa separação das preocupações entre a lógica e a marcação.

**O HTL é compatível com Bibliotecas de tags JSP?**Não, mas como mostrado na seção [Carregando bibliotecas](getting-started.md#loading-client-libraries) do cliente, as [instruções de modelo e chamada](block-statements.md#template-call) oferecem um padrão semelhante.

**Os recursos HTL podem ser estendidos em um projeto do AEM?**** Não, mas como mostrado na seção [Carregando bibliotecas](getting-started.md#loading-client-libraries) do cliente, as [instruções de modelo e chamada](block-statements.md#template-call) oferecem um padrão semelhante.
Não, não. A HTL tem mecanismos avançados de extensão para reutilização de lógica - a [Utilização - API](getting-started.md#use-api-for-accessing-logic) - e de marcação (as [declarações de modelo e chamada](block-statements.md#template-call) ), que podem ser usadas para modulular o código de projetos.

**Quais são os principais benefícios do HTL sobre JSP?**A segurança e a eficiência do projeto são os principais benefícios, que são detalhados sobre [a Visão geral](overview.md).

**JSP acabará?**Na data atual, não há planos nessas linhas.

## Conceitos fundamentais do HTL {#fundamental-concepts-of-htl}

O Linguagem de modelo HTML usa um idioma de expressão para inserir partes de conteúdo na marcação renderizada, e os atributos de dados HTML 5 para definir declarações sobre blocos de marcação (como condições ou iterações). À medida que o HTL é compilado em servlets Java, as expressões e os atributos de dados HTL são avaliados totalmente no lado do servidor e nada permanece visível no HTML resultante.

### Blocos e expressões {#blocks-and-expressions}

Aqui está um primeiro exemplo, que pode estar contido em um **`template.html`** arquivo:

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

Dois tipos diferentes de sintaxes podem ser diferenciar:

* **[Declarações
de bloco](block-statements.md)**Para exibir condicionalmente a **& amp; lt; h 1 & amp; gt;** , um `[data-sly-test](block-statements.md#test)` atributo de dados HTML 5 é usado. O HTL fornece vários desses atributos, que permitem anexar comportamento a qualquer elemento HTML, e todos têm `data-sly`o prefixo.

* **[As expressões HTL do idioma](expression-language.md)**
da expressão são delimitadas por caracteres `${` e `}`. No tempo de execução, essas expressões são avaliadas e seu valor é inserido no fluxo HTML de saída.

As duas páginas vinculadas acima fornecem a lista detalhada dos recursos disponíveis para sintaxe.

### O elemento SLY {#the-sly-element}

>[!NOTE]
>
>O elemento SLY foi introduzido com o AEM 6.1 ou HTL 1.1.
>
>Antes disso, era necessário usar o `[data-sly-unwrap](block-statements.md)` atributo.

Um conceito central do HTL é oferecer a possibilidade de reutilizar elementos HTML existentes para definir declarações de bloco, o que evita a necessidade de inserir delimitadores adicionais para definir onde a declaração começa e termina. Essa anotação não lucrativa da marcação para transformar um HTML estático em um modelo dinâmico funcional oferece a vantagem de não quebrar a validade do código HTML e, portanto, para exibir corretamente, mesmo como arquivos estáticos.

No entanto, pode não haver um elemento existente no local exato onde uma declaração de bloco deve ser inserida. Para tais casos, é possível inserir um elemento SLY especial que será removido automaticamente da saída, ao executar as declarações de bloco anexadas e exibir seu conteúdo de acordo.

Assim, a seguir:

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

fará algo como a seguinte HTML, mas somente se houver, uma **`jcr:title`** e uma **`jcr:decription`** propriedade definida e se nenhuma delas estiver vazia:

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

Uma coisa para tomar cuidado é usar apenas o elemento SLY quando nenhum elemento existente possa ter sido anotado com a declaração block, pois os elementos SLY dissuam o valor oferecido pelo idioma para que não altere o HTML estático ao torná-lo dinâmico.

Por exemplo, se o exemplo anterior já tivesse sido colocado em um elemento DIV, o elemento SLY adicionado seria abusivo:

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

O exemplo a seguir mostra na **linha 1** um comentário HTL e **na linha 2** um comentário HTML:

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

Comentários HTL são comentários HTML com uma sintaxe semelhante ao javascript. Todo o comentário HTL, e qualquer coisa dentro será completamente ignorada pelo processador e removida da saída.

O conteúdo dos comentários HTML padrão será passado e as expressões no comentário serão avaliadas.

Comentários HTML não podem conter comentários HTL e vice-versa.

### Contextos especiais {#special-contexts}

Para poder aproveitar o melhor uso do HTL, é importante compreender as consequências dele com base na sintaxe HTML.

### Nomes de elemento e atributo {#element-and-attribute-names}

As expressões podem ser colocadas apenas em valores de texto HTML ou atributo, mas não em nomes de elementos ou nomes de atributos, ou não seriam mais HTML válidos. Para definir dinamicamente os nomes de elementos, a [`data-sly-element`](block-statements.md#element) declaração pode ser usada nos elementos desejados e, para definir dinamicamente os nomes de atributos, mesmo definindo vários atributos simultaneamente, a [`data-sly-attribute`](block-statements.md#attribute) declaração pode ser usada.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### Contextos sem bloco de declarações {#contexts-without-block-statements}

Como o HTL usa atributos de dados para definir declarações de bloco, não é possível definir essas declarações de bloco dentro das seguintes contextos, e apenas expressões podem ser usadas lá:

* Comentários HTML
* Elementos de script
* Elementos de estilo

O motivo para isso é que o conteúdo desses contextos é texto e não HTML, e continha elementos HTML seria considerado como dados de caracteres simples. Assim, sem elementos HTML reais, também não pode haver **`data-sly`** atributos executados.

Isso pode soar como uma grande restrição, no entanto, é uma grande restrição, pois o idioma do modelo HTML não deve ser usado para gerar a saída que não é HTML. A seção [Usar a API para acessar a lógica](getting-started.md#use-api-for-accessing-logic) abaixo apresenta como uma lógica adicional pode ser chamada a partir do modelo, que pode ser usada se for necessária para preparar uma saída complexa para esses contextos. Por exemplo, uma maneira fácil de enviar dados do back-end para um script front-end é ter a lógica do componente para gerar uma string JSON, que pode ser colocada em um atributo de dados com uma expressão HTL simples.

O exemplo a seguir ilustra o comportamento de comentários HTML, mas em elementos de script ou estilo, o mesmo comportamento seria observado:

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

fará algo como o seguinte HTML:

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### Contextos explícitos obrigatórios {#explicit-contexts-required}

Como explicado na [seção Escape](getting-started.md#automatic-context-aware-escaping) sensível ao contexto automático abaixo, um objetivo do HTL é reduzir os riscos de apresentar vulnerabilidades de cross-site scripting (XSS) aplicando automaticamente o escape sensível ao contexto para todas as expressões. Embora HTL possa detectar automaticamente o contexto de expressões colocadas dentro da marcação HTML, ele não analisa a sintaxe de javascript ou CSS em linha, e portanto depende do desenvolvedor para especificar explicitamente o contexto exato que deve ser aplicado a essas expressões.

Como não aplicar os resultados corretos de escape nas vulnerabilidades XSS, o HTL remove a saída de todas as expressões que estão no contexto de script e estilo quando o contexto não foi declarado.

Este é um exemplo de como definir o contexto para expressões colocadas em scripts e estilos:

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

Para obter mais detalhes sobre como controlar o escape, consulte a [seção Contexto](expression-language.md#display-context) de exibição do idioma da expressão.

### Limitações de limitações de contextos especiais {#lifting-limitations-of-special-contexts}

Nos casos especiais em que é necessário ignorar as restrições do script, do estilo e do contexto do comentário, é possível isolar o conteúdo em um arquivo HTL separado. Tudo o que estiver localizado em seu próprio arquivo será interpretado por HTL como um fragmento HTML normal, esquecendo o contexto de limitação do qual pode ter sido incluído.

Consulte a seção [Trabalhar com modelos](getting-started.md#working-with-client-side-templates) do cliente para ver um exemplo.

>[!CAUTION]
>
>Essa técnica pode introduzir vulnerabilidades de cross-site scripting (XSS), e os aspectos de segurança devem ser cuidadosamente estudados se forem usados. Normalmente, há maneiras melhores de implementar a mesma coisa do que confiar nesta prática.

## Recursos gerais do HTL {#general-capabilities-of-htl}

Esta seção aborda rapidamente os recursos gerais do Linguagem de modelo HTML.

### Usar a API para acessar lógica {#use-api-for-accessing-logic}

Considere o exemplo a seguir:

```xml
<p data-sly-use.logic="logic.js">${logic.title}</p>
```

E o arquivo `logic.js` javascript executado no lado do servidor, localizado ao lado de:

```
use(function () {
    return {
        title: currentPage.getTitle().substring(0, 10) + "..."
    };
});
```

Como o Idioma do modelo HTML não permite misturar código dentro da marcação, ele oferece, em vez disso, o mecanismo de extensão Usar-API para executar o código com facilidade a partir do modelo.

O exemplo acima usa javascript executado do lado do servidor para reduzir o título para 10 caracteres, mas também pode ter usado o código Java para fazer o mesmo fornecendo um nome de classe Java totalmente qualificado. Geralmente, a lógica comercial deve ser criada em Java, mas quando o componente precisa de algumas alterações específicas de exibição do que a API do Java fornece, pode ser conveniente usar alguns javascript executados no servidor para fazer isso.

Mais sobre isso nas seguintes seções:

* A seção na declaração [de análise de big data](block-statements.md#use) explica tudo o que pode ser feito com essa declaração.
* A página [Usar API](use-api.md) fornece algumas informações para ajudar a escolher entre a gravação da lógica em Java ou em javascript.
* E para detalhar como gravar a lógica, a API de uso [do javascript](use-api-javascript.md) e as páginas [de API de uso](use-api-java.md) do Java devem ajudar.

### Escape automático sensível ao contexto {#automatic-context-aware-escaping}

Considere o exemplo a seguir:

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

Na maioria dos idiomas de modelo, este exemplo cria potencialmente uma vulnerabilidade de scripts entre sites (XSS), pois mesmo quando todas as variáveis são omitidas automaticamente em HTML, o `href` atributo ainda deve ser evitado especificamente no URL. Essa omissão é um dos erros mais comuns, pois pode ser facilmente esquecida, e é difícil apontar de forma automatizada.

Para ajudar nisso, o Linguagem de modelo HTML automaticamente remove cada variável de acordo com o contexto em que é colocado. Isso é possível graças ao fato da HTL compreender a sintaxe de HTML.

Presumindo `logic.js` o seguinte arquivo:

```
use(function () {
    return {
        link:  "#my link's safe",
        title: "my title's safe",
        text:  "my text's safe"
    };
});
```

O exemplo inicial resultará em uma saída a seguir:

```xml
<p>
    <a href="#my%20link%27s%20safe" title="my title&#39;s safe">
        my text&#39;s safe
    </a>
</p>
```

Observe como o atributo se escapou de forma diferente, pois o HTL sabe que `href` e `src` os atributos devem ser evitados para o contexto URI. Além disso, se o URI começou **`javascript:`**, o atributo teria sido removido totalmente, a menos que o contexto fosse explicitamente alterado para outro item.

Para obter mais detalhes sobre como controlar o escape, consulte a [seção Contexto](expression-language.md#display-context) de exibição do idioma da expressão.

### Remoção automática de atributos vazios {#automatic-removal-of-empty-attributes}

Considere o exemplo a seguir:

```xml
<p class="${properties.class}">some text</p>
```

Se o valor da `class` propriedade estiver vazio, o Modelo de modelo HTML removerá automaticamente o `class` atributo inteiro da saída.

Novamente, isso é possível, pois a HTL entende a sintaxe HTML e, portanto, pode mostrar condicionalmente atributos com valores dinâmicos somente se o valor não estiver vazio. Isso é extremamente conveniente, pois evita adicionar um bloco de condição ao redor de atributos, o que tornaria a marcação inválida e ilegível.

Além disso, o tipo da variável colocada em uma expressão importante:

* **Sequência de caracteres:**
   * **não vazio:** Define a string como valor do atributo.
   * **vazio:** Remove todo o atributo.

* **Número:** Define o valor como valor do atributo.

* **Booleano:**
   * **true:** Exibe o atributo sem valor (como um atributo HTML booliano)
   * **false:** Remove todo o atributo.

Veja um exemplo de como uma expressão Booleana permitiria controlar um atributo HTML booliano:

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

Para definir atributos, a [`data-sly-attribute`](block-statements.md#attribute) declaração também pode ser útil.

## Padrões comuns com HTL {#common-patterns-with-htl}

Esta seção apresenta alguns cenários comuns e como melhor resolvê-los com o Linguagem de modelo HTML.

### Carregando bibliotecas do cliente {#loading-client-libraries}

No HTL, as bibliotecas do cliente são carregadas por meio de um modelo de ajuda fornecido pelo AEM, que pode ser acessado [`data-sly-use`](block-statements.md#use). Três modelos estão disponíveis neste arquivo, que podem ser chamados por [`data-sly-call`](block-statements.md#template-call)meio de:

* **`css`** - Carrega apenas os arquivos CSS das bibliotecas clientes referenciadas.
* **`js`** - Carrega apenas os arquivos javascript das bibliotecas do cliente referenciadas.
* **`all`** - Carrega todos os arquivos das bibliotecas clientes referenciadas (CSS e javascript).

Cada modelo de ajuda espera uma **`categories`** opção para referenciar as bibliotecas cliente desejadas. Essa opção pode ser uma matriz de valores de string ou uma string contendo uma lista de valores separados por vírgulas.

Estes são dois exemplos breves:

### Carregando várias bibliotecas do cliente ao mesmo tempo {#loading-multiple-client-libraries-fully-at-once}

```xml
<sly data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html"
     data-sly-call="${clientlib.all @ categories=['myCategory1', 'myCategory2']}"/>
```

### Referência a uma biblioteca cliente em diferentes seções de uma página {#referencing-a-client-library-in-different-sections-of-a-page}

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

No segundo exemplo acima, caso o HTML **`head`** e **`body`** os elementos sejam colocados em arquivos diferentes, o **`clientlib.html`** modelo deverá ser carregado em cada arquivo que precise.

A seção nas declarações de [modelo e chamada](block-statements.md#template-call) fornece mais detalhes sobre como a declaração e a chamada desses modelos funcionam.

### Transmitir dados para o cliente {#passing-data-to-the-client}

A melhor e mais elegante maneira de passar dados para o cliente em geral, mas ainda mais com o HTL, é usar os atributos de dados.

O exemplo a seguir mostra como a lógica (que também pode ser escrita em Java) pode ser usada para serializar muito convenientemente o objeto que deve ser transmitido ao cliente, que pode ser colocado facilmente em um atributo de dados:

```xml
<!--/* template.html file: */-->
<div data-sly-use.logic="logic.js" data-json="${logic.json}">...</div>
```

```
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

Daí, é fácil imaginar como um javascript do cliente pode acessar esse atributo e analisar novamente o JSON. Isso, por exemplo, seria o javascript correspondente a ser colocado em uma biblioteca cliente:

```
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### Trabalhar com modelos do cliente {#working-with-client-side-templates}

Um caso especial, em que a técnica explicada na seção Limitações [de elevação de contextos especiais](getting-started.md#lifting-limitations-of-special-contexts) pode ser legitimamente usada, é escrever modelos do cliente (como Handlebars, por exemplo) que estão localizados em **elementos de script** . O motivo pelo qual essa técnica pode ser usada com segurança nesse caso é porque o **elemento de script** não contém javascript como supõe, mas outros elementos HTML. Veja um exemplo de como isso funcionará:

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

Como mostrado acima, a marcação que será incluída no **`script`** elemento pode conter instruções de bloco HTL e as expressões não precisam fornecer contextos explícitos, pois o conteúdo do modelo Handlebars foi isolado em seu próprio arquivo. Além disso, este exemplo mostra como o HTL executado pelo lado do servidor (como no **`h2`** elemento) pode ser misturado com uma linguagem de modelo executada pelo lado do cliente, como Handlebars (mostrada no **`h3`** elemento).

Uma técnica mais moderna, no entanto, seria usar o elemento HTML **`template`** em vez disso, pois o benefício seria que não era necessário isolar o conteúdo dos modelos em arquivos separados.

**Ler em seguida:**

* [Idioma da expressão](expression-language.md) - para saber detalhadamente o que pode ser feito em expressões HTL.
* [Declarações de bloco](block-statements.md) - para descobrir todas as declarações bloqueadas disponíveis no HTL e como usá-las.
