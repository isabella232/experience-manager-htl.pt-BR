---
title: Introdução ao HTL
description: Saiba mais sobre HTL, o sistema de modelo do lado do servidor preferencial e recomendado para HTML no AEM, e entenda os principais conceitos dessa linguagem e seus elementos fundamentais.
exl-id: c95eb1b3-3b96-4727-8f4f-d54e7136a8f9
source-git-commit: 88edbd2fd66de960460df5928a3b42846d32066b
workflow-type: tm+mt
source-wordcount: '2170'
ht-degree: 100%

---


# Introdução ao HTL {#getting-started-with-htl}

O HTL (Linguagem de modelo HTML) é o sistema de modelo do lado do servidor preferencial e recomendado para HTML no Adobe Experience Manager. Como em todos os sistemas de modelos HTML do lado do servidor, um arquivo HTL define a saída enviada para o navegador especificando o próprio HTML, alguma lógica de apresentação básica e as variáveis a serem avaliadas no tempo de execução.

Este documento fornece uma visão geral da finalidade do HTL, bem como uma introdução aos conceitos e elementos fundamentais da linguagem.

>[!TIP]
>
>Este documento apresenta a finalidade do HTL e uma visão geral de sua estrutura e conceitos fundamentais. Em caso de dúvidas sobre sintaxes específicas, consulte a [Especificação do HTL.](specification.md)

## Camadas HTL {#layers}

O HTL, conforme utilizado no AEM, pode ser definido por várias camadas.

1. **[Especificação do HTL](specification.md)** - o HTL é uma especificação de código aberto e independente de plataforma que pode ser implementada livremente por qualquer pessoa.
1. **[Mecanismo de script HTL do Sling](specification.md)** - o projeto Sling criou a implementação de referência do HTL, que é usada pelo AEM.
1. **[Extensões do AEM](specification.md)** - o AEM se baseia no Mecanismo de script HTL do Sling para oferecer aos desenvolvedores recursos convenientes e específicos ao AEM.

Esta documentação se limita ao uso do HTL para desenvolvimento de soluções no AEM. Assim, ela abrange as três camadas, fornecendo links para recursos externos conforme necessário.

## Conceitos fundamentais do HTL {#fundamental-concepts-of-htl}

A Linguagem de modelo HTML usa uma linguagem de expressão para inserir partes de conteúdo na marcação renderizada, e atributos de dados HTML5 para definir instruções sobre blocos de marcação (como condições ou iterações). À medida que o HTL é compilado em Servlets Java, as expressões e os atributos de dados HTL são avaliados totalmente no lado do servidor e nada permanece visível no HTML resultante.

>[!TIP]
>
>Para executar a maioria dos exemplos fornecidos nesta página, um ambiente de execução ativo chamado [Read Eval Print Loop](https://github.com/adobe/aem-htl-repl) pode ser usado.

### Blocos e expressões {#blocks-and-expressions}

Este é um primeiro exemplo, que pode estar contido como está em um arquivo `template.html`:

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

Dois tipos diferentes de sintaxe podem ser identificados:

* **Declarações de bloco** - para exibir condicionalmente o elemento `<h1>`, um atributo de dados `data-sly-test` HTML5 é usado. O HTL fornece vários atributos como esse, que permitem anexar um comportamento a qualquer elemento HTML, e todos recebem o prefixo `data-sly`.
* **Linguagem de expressão** - as expressões HTL são delimitadas pelos caracteres `${` e `}`. No tempo de execução, essas expressões são avaliadas e seu valor é inserido no fluxo HTML de saída.

Consulte a [Especificação do HTL](specification.md) para obter detalhes sobre ambas as sintaxes.

### O elemento SLY {#the-sly-element}

Um objetivo central do HTL é oferecer a possibilidade de reutilizar elementos HTML existentes para definir declarações de bloco, evitando a necessidade de inserir delimitadores adicionais para definir onde a declaração começa e termina. Essa anotação não invasiva da marcação para transformar um HTML estático em um modelo dinâmico funcional oferece a vantagem de não romper a validade do código HTML e, portanto, ainda exibir corretamente, mesmo como arquivos estáticos.

No entanto, às vezes, pode não haver um elemento existente no local exato em que uma instrução em bloco deve ser inserida. Para esses casos, é possível inserir um elemento `sly` especial, que será removido automaticamente da saída enquanto as declarações de bloco anexadas são executadas e o conteúdo é exibido de acordo.

O exemplo a seguir...

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

...resultará em algo como o HTML abaixo, mas somente se houver uma propriedade `jcr:title` e uma propriedade `jcr:description` definidas e se nenhuma delas estiver vazia:

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

Lembre-se de usar o elemento `sly` somente quando nenhum elemento existente puder ser anotado com a declaração de bloco. Isso porque elementos `sly` bloqueiam o valor oferecido pela linguagem para não alterar o HTML estático ao torná-lo dinâmico.

Por exemplo, se o exemplo anterior já tivesse sido encapsulado dentro de um elemento `div`, o elemento `sly` adicionado seria abusivo:

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

e o elemento `div` poderia ter sido anotado com a condição:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

### Comentários HTL {#htl-comments}

O exemplo a seguir mostra um comentário HTL na primeira linha e um comentário HTML na segunda linha.

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

Os comentários HTL são comentários HTML com uma sintaxe adicional semelhante a JavaScript. O comentário HTL inteiro e qualquer coisa dentro dele será totalmente ignorado pelo processador e removido da saída.

No entanto, o conteúdo dos comentários HTML padrão será transmitido, e as expressões dentro do comentário serão avaliadas.

Comentários HTML não podem conter comentários HTL e vice-versa.

### Contextos especiais {#special-contexts}

Para poder usar melhor o HTL, é importante entender bem as consequências de seu uso ser baseado na sintaxe HTML.

Consulte a [seção Contexto de exibição](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#121-display-context) da especificação do HTL para obter mais detalhes.

### Nomes de elementos e atributos {#element-and-attribute-names}

As expressões só podem ser colocadas em um texto HTML ou em valores de atributo, mas não em nomes de elementos ou nomes de atributos, pois isso invalidaria o HTML. Para definir nomes de elementos dinamicamente, a instrução `data-sly-element` pode ser usada nos elementos desejados, e para definir dinamicamente nomes de atributos, mesmo configurando vários atributos de uma só vez, a instrução `data-sly-attribute` pode ser usada.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### Contextos sem instruções em bloco {#contexts-without-block-statements}

Como o HTL usa atributos de dados para definir instruções em bloco, não é possível definir essas instruções dentro dos seguintes contextos, e somente as expressões podem ser usadas lá:

* Comentários HTML
* Elementos de script
* Elementos de estilo

O motivo para isso é que o conteúdo desses contextos é texto e não HTML, e os elementos HTML contidos seriam considerados como dados de caracteres simples. Portanto, sem elementos HTML reais, também não pode haver atributos `data-sly` executados.

Essa pode parecer uma restrição significativa, mas é recomendada, visto que a Linguagem de modelo HTML não deve ser usada para gerar uma saída que não seja HTML. Abaixo, a seção [API de uso para acessar a lógica](#use-api-for-accessing-logic) apresenta como a lógica adicional pode ser chamada a partir do modelo, que pode ser usada se for necessária para preparar saídas complexas para esses contextos. Por exemplo, uma maneira fácil de enviar dados do back-end para um script de front-end é ter a lógica do componente para gerar uma cadeia de caracteres JSON, que pode ser colocada em um atributo de dados com uma expressão HTL simples.

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

Conforme explicado abaixo, na seção [Escape automático sensível ao contexto](#automatic-context-aware-escaping), um objetivo do HTL é reduzir os riscos de introdução de vulnerabilidades de script entre sites (XSS), aplicando automaticamente o escape com reconhecimento de contexto a todas as expressões. Embora o HTL possa detectar automaticamente o contexto de expressões colocadas dentro da marcação HTML, ele não analisa a sintaxe do JavaScript ou CSS em linha e, portanto, depende do desenvolvedor para especificar explicitamente o contexto exato a ser aplicado a essas expressões.

Não aplicar o escape correto resulta em vulnerabilidades XSS. Por isso, o HTL remove a saída de todas as expressões que estão em contextos de script e estilo, quando o contexto não foi declarado.

Este é um exemplo de como definir o contexto de expressões colocadas dentro de scripts e estilos:

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

Para obter mais detalhes sobre como controlar o escape, consulte a seção [Contexto de exibição da linguagem de expressão](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) das especificações do HTL. 

## Recursos gerais do HTL {#general-capabilities-of-htl}

Esta seção aborda rapidamente os recursos gerais da Linguagem de modelo HTML.

### API de uso para acessar a lógica {#use-api-for-accessing-logic}

A API de uso do Java da Linguagem de modelo HTML (HTL) permite que um arquivo de HTL acesse métodos de ajuda em uma classe de Java personalizada `data-sly-use`. Isso permite que toda lógica de negócios complexa seja encapsulada no código Java, enquanto o código HTL lida somente com a produção de marcação direta.

Consulte o documento [API de uso Java do HTL](java-use-api.md) para obter mais detalhes.

### Escape automático sensível ao contexto {#automatic-context-aware-escaping}

Considere o exemplo a seguir:

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

Na maioria das linguagens de modelo, esse exemplo poderia ocasionar uma vulnerabilidade de criação de script entre sites (XSS), pois mesmo quando todas as variáveis são automaticamente escapadas por HTML, o atributo `href` ainda deve ter um escape de URL específico. Essa omissão é um dos erros mais comuns, pois pode ser facilmente esquecida e é difícil de detectar de forma automatizada.

Para ajudar nisso, a Linguagem de modelo HTML escapa automaticamente cada variável de acordo com o contexto em que é colocada. Isso é possível devido ao fato de que o HTL entende a sintaxe do HTML.

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

Observe como os dois atributos foram escapados de forma diferente, porque o HTL sabe que os atributos `href` e `src` devem ser escapados para o contexto do URI. Além disso, se o URI começasse com `javascript:`, o atributo teria sido totalmente removido, a menos que o contexto fosse explicitamente alterado para algo diferente.

Para obter mais detalhes sobre como controlar o escape, consulte a seção [Contexto de exibição da linguagem de expressão](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) das especificações do HTL.

### Remoção automática de atributos vazios {#automatic-removal-of-empty-attributes}

Considere o exemplo a seguir:

```xml
<p class="${properties.class}">some text</p>
```

Se o valor da propriedade `class` estiver vazio, a Linguagem de modelo HTML removerá automaticamente todo o atributo `class` da saída.

Novamente, isso é possível porque o HTL entende a sintaxe HTML e, portanto, pode mostrar atributos com valores dinâmicos somente se o valor não estiver vazio. Isso é bastante conveniente, pois evita a adição de um bloco de condição ao redor dos atributos, o que tornaria a marcação inválida e ilegível.

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

Para definir atributos, a instrução `data-sly-attribute` também pode ser útil.

## Padrões comuns com HTL {#common-patterns-with-htl}

Esta seção apresenta alguns cenários comuns e como resolvê-los da melhor maneira com a Linguagem de modelo HTML.

### Carregamento de bibliotecas de clientes {#loading-client-libraries}

No HTL, as bibliotecas do cliente são carregadas por meio de um modelo auxiliar fornecido pelo AEM, que pode ser acessado por meio de `data-sly-use`. Três modelos estão disponíveis nesse arquivo, que pode ser chamado por meio de `data-sly-call`:

* **`css`** - carrega somente os arquivos CSS das bibliotecas de clientes referenciadas.
* **`js`** - carrega somente os arquivos JavaScript das bibliotecas de clientes referenciadas.
* **`all`** - carrega todos os arquivos das bibliotecas de clientes referenciadas (CSS e JavaScript).

Cada modelo auxiliar espera uma opção `categories` para fazer referência às bibliotecas de clientes desejadas. Essa opção pode ser uma matriz de valores de string ou uma string contendo uma lista de valores separados por vírgula.

Veja a seguir dois pequenos exemplos.

#### Carregamento de várias bibliotecas de clientes de uma só vez {#loading-multiple-client-libraries-fully-at-once}

```xml
<sly data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html"
     data-sly-call="${clientlib.all @ categories=['myCategory1', 'myCategory2']}"/>
```

#### Referência a uma biblioteca do cliente em diferentes seções de uma página {#referencing-a-client-library-in-different-sections-of-a-page}

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

Neste exemplo, caso os elementos HTML `head` e `body` sejam colocados em arquivos diferentes, o modelo `clientlib.html` deverá ser carregado em cada arquivo que precisar dele.

A seção sobre as declarações de modelo e chamada na [especificação do HTL](specification.md) fornece mais detalhes sobre como a declaração e a chamada desses modelos funcionam.

### Enviar dados para o cliente {#passing-data-to-the-client}

A melhor e mais eficiente maneira de enviar dados para o cliente em geral, principalmente no caso de HTL, é usar atributos `data`.

O exemplo a seguir mostra como a lógica (que também poderia ser escrita em Java) pode ser convenientemente usada para serializar em JSON o objeto a ser enviado ao cliente, que pode então ser facilmente colocado em um atributo `data`:

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

Um caso especial, em que a técnica explicada na seção [Supressão das limitações de contextos especiais](#lifting-limitations-of-special-contexts) pode ser usada legitimamente, é escrever modelos do lado do cliente (como Handlebars, por exemplo) que estão localizados em elementos de `scrip`. Essa técnica poder ser usada com segurança nesse caso, porque o elemento de `script` não contém JavaScript como pressuposto, mas outros elementos HTML. Veja um exemplo de como isso funcionaria:

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

Como mostrado acima, a marcação que será incluída no elemento `script` pode conter declarações de bloco HTL, e as expressões não precisam fornecer contextos explícitos, pois o conteúdo do modelo Handlebars foi isolado em seu próprio arquivo. Além disso, este exemplo mostra como o HTL executado do lado do servidor (como no elemento `h2`) pode ser misturado com uma linguagem de modelo executada do lado do cliente, como Handlebars (mostrado no elemento `h3`).

Uma técnica mais moderna seria, no entanto, usar o elemento HTML `template`, cujo benefício é não ser necessário isolar o conteúdo dos modelos em arquivos separados.

### Supressão das limitações de contextos especiais {#lifting-limitations-of-special-contexts}

Em casos especiais nos quais é necessário ignorar as restrições dos contextos de script, estilo e comentário, é possível isolar o conteúdo em um arquivo HTL separado. Tudo que estiver localizado nesse arquivo será interpretado pelo HTL como um fragmento HTML normal, esquecendo o contexto de limitação do qual ele pode ter sido incluído.

Mais abaixo, consulte a seção [Trabalhar com modelos do lado do cliente](#working-with-client-side-templates), para obter um exemplo.

>[!CAUTION]
>
>Essa técnica pode apresentar vulnerabilidades de criação de script entre sites (XSS), e os aspectos de segurança devem ser cuidadosamente estudados se isso for usado. Geralmente há melhores maneiras de implementar a mesma coisa do que confiar nessa prática.
