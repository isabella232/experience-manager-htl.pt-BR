---
title: Linguagem de expressão do HTL
seo-title: Linguagem de expressão do HTL
description: A Linguagem de modelo HTML usa uma linguagem de expressão para acessar as estruturas de dados que fornecem os elementos dinâmicos da saída HTML.
seo-description: 'A Linguagem de modelo HTML usa uma linguagem de expressão para acessar as estruturas de dados que fornecem os elementos dinâmicos da saída HTML. '
uuid: 38b4a259-03b5-4847-91c6-e20377600070
contentOwner: Usuário
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: referência
discoiquuid: 9ba37ca0-f318-48b0-a791-a944a72502ed
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 6de5ed20e4463c0c2e804e24cb853336229a7c1f

---


# Linguagem de expressão do HTL {#htl-expression-language}

A Linguagem de modelo HTML usa uma linguagem de expressão para acessar as estruturas de dados que fornecem os elementos dinâmicos da saída HTML. Essas expressões são delimitadas por caracteres `${` e `}`. Para evitar HTML malformado, as expressões só podem ser usadas em valores de atributo, no conteúdo do elemento ou em comentários.

```xml
<!-- ${component.path} -->
<h1 class="${component.name}">
    ${properties.jcr:title}
</h1>
```

As expressões podem ser evitadas por um **`\`** caractere, por exemplo, **`\${test}`** serão renderizadas **`${test}`**.

>[!NOTE]
>
>Para testar os exemplos fornecidos nesta página, é possível usar um ambiente de execução ao vivo chamado Loop [de impressão](https://github.com/Adobe-Marketing-Cloud/aem-sightly-repl) Leitura Eval.

A sintaxe de expressão inclui [variáveis](#variables), [literais](#literals), [operadores](#operators) e [opções](#options):

## Variáveis {#variables}

As variáveis são contêineres que armazenam valores de dados ou objetos. Os nomes das variáveis são chamados de identificadores.

Sem precisar especificar nada, o HTL fornece acesso a todos os objetos que normalmente estavam disponíveis no JSP após a inclusão `global.jsp`. A página Objetos [](global-objects.md) globais fornece a lista de todos os objetos aos quais o HTL concedeu acesso.

### Acesso à propriedade {#property-access}

Há duas maneiras de acessar propriedades de variáveis, com uma notação de ponto ou com uma notação de colchete:

```
${currentPage.title}  
${currentPage['title']} or ${currentPage["title"]}
```

A notação de pontos mais simples deve ser preferida para a maioria dos casos, e a notação de colchetes deve ser usada para acessar propriedades que contêm caracteres de identificador inválidos ou para acessar propriedades dinamicamente. Os dois capítulos seguintes fornecerão pormenores sobre estes dois casos.

As propriedades acessadas podem ser funções, mas não há suporte para a transmissão de argumentos, portanto, somente as funções que não esperam argumentos podem ser acessadas, como getters. Essa é uma limitação desejada, que tem como objetivo reduzir a quantidade de lógica incorporada às expressões. Se necessário, a [`data-sly-use`](block-statements.md#use) instrução pode ser usada para passar parâmetros para a lógica.

Também mostrado no exemplo acima é que funções do Java getter, como `getTitle()`, podem ser acessadas sem antecipar o **`get`** e diminuindo a caixa do caractere a seguir.

### Caracteres de identificador válidos {#valid-indentifier-characters}

Os nomes de variáveis, chamados de identificadores, estão em conformidade com determinadas regras. Eles devem começar com uma letra (**`A`**-**`Z`** e **`a`**-**`z`**) ou um sublinhado (**`_`**), e os caracteres subsequentes também podem ser dígitos (**`0`**-**`9`**) ou dois pontos (**`:`**). Letras Unicode como **`å`** e **`ü`** não podem ser usadas em identificadores.

Como o caractere de dois pontos (**:**) é comum nos nomes de propriedades do AEM, é conveniente que ele seja um caractere identificador válido:

`${properties.jcr:title}`

A notação entre colchetes pode ser usada para acessar propriedades que contêm caracteres de identificador inválidos, como o caractere de espaço no exemplo abaixo:

`${properties['my property']}`

### Acessar membros dinamicamente {#accessing-members-dynamically}

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

```xml
${properties[myVar]}
```

### Tratamento Permissivo de Valores Nulos {#permissive-handling-of-null-values}

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

```xml
${currentPage.lastModified.time.toString}
```

## Literais {#literals}

Um literal é uma notação para representar um valor fixo.

### Booleano {#boolean}

Booliano representa uma entidade lógica e pode ter dois valores: **`true`**, e **`false`**.

`${true} ${false}`

### Números {#numbers}

Há apenas um tipo de número: números inteiros positivos. Enquanto outros formatos de números, como ponto flutuante, são suportados em variáveis, mas não podem ser expressos como literais.

`${42}`

### Strings {#strings}

Eles representam dados textuais e podem ser entre aspas simples ou duplas:

`${'foo'} ${"bar"}`

Além dos caracteres comuns, os seguintes caracteres especiais podem ser usados:

* **`\\`** Caractere de barra invertida
* **`\'`** Citação única (ou apóstrofe)
* **`\"`** Aspas duplas
* **`\t`** Tabulação
* **`\n`** Nova linha
* **`\r`** Retorno de carro
* **`\f`** Feed de formulário
* **`\b`** Backspace
* `\uXXXX` O caractere Unicode especificado pelos quatro dígitos hexadecimais XXXX.\
   Algumas sequências de escape unicode úteis são:

   * **\u0022** para **"**
   * **\u0027** para **'**

Para caracteres não listados acima, o anterior a um caractere de barra invertida exibirá um erro.

Estes são alguns exemplos de como usar o escape de sequência:

```xml
<p>${'it\'s great, she said "yes!"'}</p>
<p title="${'it\'s great, she said \u0022yes!\u0022'}">...</p>
```

que resultará na seguinte saída, pois HTL aplicará escape específico ao contexto:

```xml
<p>it&#39;s great, she said &#34;yes!&#34;</p>
<p title="it&#39;s great, she said &#34;yes!&#34;">...</p>
```

### Matrizes {#arrays}

Uma matriz é um conjunto ordenado de valores que podem ser referenciados com um nome e um índice. Os tipos de seus elementos podem ser misturados.

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

```xml
${[1,2,3,4]}
${myArray[2]}
```

As matrizes são úteis para fornecer uma lista de valores do modelo.

```xml
<ul data-sly-list="${[1,2,3,4]}">
  <li>${item}</li>
</ul>
```

## Operadores {#operators}

### Operadores lógicos {#logical-operators}

Normalmente, esses operadores são usados com valores booleanos, no entanto, como no JavaScript, eles retornam o valor de um dos operandos especificados, de modo que, quando usados com valores não booleanos, podem retornar um valor não booliano.

Se um valor puder ser convertido em **`true`**, ele será chamado de verdadeiro. Se um valor puder ser convertido em **`false`**, ele será chamado de falso. Os valores que podem ser convertidos **`false`** são: variáveis indefinidas, valores nulos, o número zero e sequências vazias.

#### NÃO lógico {#logical-not}

**`${!myVar}`** retorna **`false`** se seu único operando puder ser convertido em `true`; caso contrário, retorna **`true`**.

Por exemplo, isso pode ser usado para inverter uma condição de teste, como exibir um elemento somente se não houver páginas secundárias:

```xml
<p data-sly-test="${!currentPage.hasChild}">current page has no children</p>
```

#### AND lógica {#logical-and}

**`${varOne && varTwo}`** retorna `varOne` se for falsa; caso contrário, retorna **varTwo**.

Esse operador pode ser usado para testar duas condições de uma só vez, como verificar a existência de duas propriedades:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

O operador AND lógico também pode ser usado para exibir condicionalmente atributos HTML, pois HTL remove atributos com valores definidos dinamicamente que avaliam como falso ou como uma sequência vazia. Portanto, no exemplo abaixo, o **`class`** atributo só é mostrado se **`logic.showClass`** é verdadeiro e se **`logic.className`** existe e não está vazio:

```xml
<div class="${logic.showClass && logic.className}">...</div>
```

#### OR lógica {#logical-or}

**`${varOne || varTwo}`** retorna **varOne** se for verdadeiro; caso contrário, retorna **varTwo**.

Esse operador pode ser usado para testar se uma das duas condições se aplicam, como verificar a existência de pelo menos uma propriedade:

```xml
<div data-sly-test="${properties.jcr:title || properties.jcr:description}">...</div>
```

Como o operador OR lógico retorna a primeira variável verdadeira, ela também pode ser usada para fornecer valores de fallback.

exibir condicionalmente atributos HTML, pois HTL remove atributos com valores definidos por expressões que avaliam como falso ou como uma sequência vazia. Assim, o exemplo abaixo exibirá o **`properties.jcr:`** título se ele existir e não estiver vazio, caso contrário, ele voltará para a exibição **`properties.jcr:description`** se existir e não estiver vazio, caso contrário, exibirá a mensagem "nenhum título ou descrição fornecido":

```xml
<p>${properties.jcr:title || properties.jcr:description || "no title or description provided"}</p>
```

### Operador condicional (ternário) {#conditional-ternary-operator}

**`${varCondition ? varOne : varTwo}`** retorna **`varOne`** se **`varCondition`** for verdadeiro; caso contrário, retorna **`varTwo`**.

Normalmente, esse operador pode ser usado para definir condições em expressões, como exibir uma mensagem diferente com base no status da página:

```xml
<p>${currentPage.isLocked ? "page is locked" : "page can be edited"}</p>
```

Uma observação importante, uma vez que caracteres de dois pontos também são permitidos em identificadores, é melhor separar os operadores ternários com um espaço em branco para fornecer clareza ao analisador:

```xml
<p>${properties.showDescription ? properties.jcr:description : properties.jcr:title}</p>
```

### Operadores de comparação {#comparison-operators}

Os operadores de igualdade e desigualdade suportam apenas operandos de tipos idênticos. Quando os tipos não correspondem, um erro é exibido.

* As strings são iguais quando têm a mesma sequência de caracteres.
* Os números são iguais quando têm o mesmo valor
* Os booleanos são iguais se ambos forem **`true`** ou se ambos forem **`false`**.

* As variáveis nulas ou indefinidas são iguais a si mesmas e entre si.

**`${varOne == varTwo}`** retorna **`true`** se **`varOne`** e **`varTwo`** é igual.

**`${varOne != varTwo}`** retorna **`true`** se **`varOne`** e **`varTwo`** não são iguais.

Os operadores relacionais suportam apenas operandos que são números. Para todos os outros tipos, um erro é exibido.

**`${varOne > varTwo}`** retorna **`true`** se **`varOne`** for maior que **`varTwo`**.

**`${varOne < varTwo}`** retorna **`true`** se **`varOne`** for menor que **`varTwo`**.

**`${varOne >= varTwo}`** retorna **`true`** se **`varOne`** for maior ou igual a **`varTwo`**.

**`${varOne <= varTwo}`** retorna **`true`** se **`varOne`** for menor ou igual a **`varTwo`**.

### Parênteses de agrupamento {#grouping-parentheses}

O operador de agrupamento **`(`** **`)`** controla a precedência da avaliação em expressões.

`${varOne && (varTwo || varThree)}`

## Opções {#options}

<!-- 

Comment Type: draft

<p>TODO: review text below.</p>

 -->

As opções de expressão podem atuar na expressão e modificá-la, ou servir como parâmetros quando usadas em conjunto com instruções de bloqueio.

Tudo depois da **`@`** opção é uma opção:

```xml
${myVar @ optOne}
```

As opções podem ter um valor, que pode ser uma variável ou um literal:

```xml
${myVar @ optOne=someVar}
${myVar @ optOne='bar'}
${myVar @ optOne=10}
${myVar @ optOne=true}
```

Várias opções são separadas por vírgulas:

```xml
${myVar @ optOne, optTwo=bar}
```

Expressões paramétricas contendo apenas opções também são possíveis:

```xml
${@ optOne, optTwo=bar}
```

### Formatação de sequência {#string-formatting}

Opção que substitui os espaços reservados enumerados, {*n*}, pela variável correspondente:

```xml
${'Page {0} of {1}' @ format=[current, total]}
```

### internacionalização {#internationalization}

Traduz a string para o idioma da *fonte* atual (veja abaixo), usando o [dicionário](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/i18n-translator)atual. Se nenhuma tradução for encontrada, a string original será usada.

```xml
${'Page' @ i18n}
```

A opção de dica pode ser usada para fornecer um comentário aos tradutores, especificando o contexto em que o texto é usado:

```xml
${'Page' @ i18n, hint='Translation Hint'}
```

A origem padrão do idioma é "recurso", o que significa que o texto é traduzido para o mesmo idioma do conteúdo. Isso pode ser alterado para "usuário", o que significa que o idioma é retirado da localidade do navegador ou da localidade do usuário conectado:

```xml
${'Page' @ i18n, source='user'}
```

Fornecer uma localidade explícita substitui as configurações de origem:

```xml
${'Page' @ i18n, locale='en-US'}
```

Para incorporar variáveis em uma string traduzida, a opção de formato pode ser usada:

```xml
${'Page {0} of {1}' @ i18n, format=[current, total]}
```

### Junção de matriz {#array-join}

Por padrão, ao exibir uma matriz como texto, HTL exibirá valores separados por vírgula (sem espaçamento).

Use a opção de junção para especificar um separador diferente:

```xml
${['one', 'two'] @ join='; '}
```

### Exibir contexto {#display-context}

O contexto de exibição de uma expressão HTL refere-se à sua localização na estrutura da página HTML. Por exemplo, se a expressão aparecer no lugar que produziria um nó de texto depois de renderizada, então ela estará em um **`text`** contexto. Se for encontrado dentro do valor de um atributo, então diz-se que está em um **`attribute`** contexto e assim por diante.

Com exceção dos contextos de script (JS) e estilo (CSS), o HTL detectará automaticamente o contexto das expressões e as ignorará apropriadamente para evitar problemas de segurança XSS. No caso de scripts e CSS, o comportamento de contexto desejado deve ser definido explicitamente. Além disso, o comportamento de contexto também pode ser explicitamente definido em qualquer outro caso em que uma substituição do comportamento automático seja desejada.

Aqui temos três variáveis em três contextos diferentes: **`properties.link`** ( `uri` contexto), **`properties.title`** (**`attribute`** contexto) e **`properties.text`**(**`text`** contexto). Os HTL escapam a cada uma destas situações de forma diferente, de acordo com os requisitos de segurança dos respectivos contextos. Nenhuma configuração de contexto explícita é necessária em casos normais, como este:

```xml
<a href="${properties.link}" title="${properties.title}">${properties.text}</a>
```

Para exibir com segurança a marcação (isto é, onde a própria expressão avalia para HTML), o `html` contexto é usado:

```xml
<div>${properties.richText @ context='html'}</div>
```

O contexto explícito deve ser definido para contextos de estilo:

```xml
<span style="color: ${properties.color @ context='styleToken'};">...</span>
```

O contexto explícito deve ser definido para contextos de script:

```xml
<span onclick="${properties.function @ context='scriptToken'}();">...</span>
```

A proteção de Escaping e XSS também pode ser desativada:

```xml
<div>${myScript @ context='unsafe'}</div>
```

### Configurações de contexto {#context-settings}

| Contexto | Quando usar | O que ele faz |
|--- |--- |--- |
| texto | Padrão para conteúdo dentro de elementos | Codifica todos os caracteres especiais HTML. |
| html | Marcação de saída com segurança | Filtra HTML para atender às regras de política do AntiSamy, removendo o que não corresponde às regras. |
| attribute | Padrão para valores de atributo | Codifica todos os caracteres especiais HTML. |
| uri | Para exibir links e caminhos Padrão para valores de atributos href e src | Valida o URI para gravar como um valor de atributo href ou src, não resulta em nada se a validação falhar. |
| número | Para exibir números | Valida o URI para conter um número inteiro, resulta em zero se a validação falhar. |
| attributeName | Padrão para atributo de estilo de dados ao definir nomes de atributo | Valida o nome do atributo e não gera nada se a validação falhar. |
| elementName | Padrão para o elemento de estilo de dados | Valida o nome do elemento e não gera nada se a validação falhar. |
| scriptToken | Para identificadores JS, números literais ou strings literais | Valida o token JavaScript e não gera nada se a validação falhar. |
| scriptString | Em strings JS | Codifica caracteres que se dividiriam na string. |
| scriptComment | Em comentários JS | Valida o comentário do JavaScript, não resulta em nada se a validação falhar. |
| styleToken | Para identificadores CSS, números, dimensões, strings, cores hexadecimais ou funções. | Valida o token CSS e não gera nada se a validação falhar. |
| styleString | Em strings CSS | Codifica caracteres que se dividiriam na string. |
| styleComment | Em comentários CSS | Valida o comentário CSS, não resulta em nada se a validação falhar. |
| inseguro | Somente se nenhuma das opções anteriores fizer o serviço | Desativa a remoção e a proteção XSS completamente. |

