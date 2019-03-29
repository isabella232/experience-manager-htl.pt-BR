---
title: Linguagem de expressão HTL
seo-title: Linguagem de expressão HTL
description: O Linguagem de modelo HTML usa um idioma de expressão para acessar as
  estruturas de dados que fornecem os elementos dinâmicos da saída HTML.
seo-description: O Linguagem de modelo HTML usa um idioma de expressão para acessar
  as estruturas de dados que fornecem os elementos dinâmicos da saída HTML.
uuid: 38 b 4 a 259-03 b 5-4847-91 c 6-e 20377600070
contentOwner: Usuário
products: SG_ EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: referência
discoiquuid: 9 ba 37 ca 0-f 318-48 b 0-a 791-a 944 a 72502 ed
mwpw-migration-script-version: 2017-10-12 T 21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 796c55d3d85e6b5a3efaa5c04a25be1b0b4e54dd

---


# Linguagem de expressão HTL {#htl-expression-language}

O Linguagem de modelo HTML usa um idioma de expressão para acessar as estruturas de dados que fornecem os elementos dinâmicos da saída HTML. Essas expressões são delimitadas por caracteres `${` e `}`. Para evitar HTML malformado, as expressões só podem ser usadas em valores de atributo, no conteúdo do elemento ou em comentários.

```xml
<!-- ${component.path} -->
<h1 class="${component.name}">
    ${properties.jcr:title}
</h1>
```

As expressões podem ser escapadas por um **`\`** caractere, por exemplo, se a instância **`\${test}`****`${test}`**for renderizada.

>[!NOTE]
>
>Para testar os exemplos fornecidos nessa página, é possível usar um ambiente de execução ao vivo chamado [Loop](https://github.com/Adobe-Marketing-Cloud/aem-sightly-repl) de impressão eval.

A sintaxe de expressão inclui [variáveis](#variables), [literais](#literals), [operadores](#operators) e [opções](#options):

## Variáveis {#variables}

As variáveis são contêineres que armazenam valores ou objetos de dados. Os nomes das variáveis são chamados de identificadores.

Sem precisar especificar qualquer coisa, o HTL fornece acesso a todos os objetos comumente disponíveis no JSP depois de incluir `global.jsp`. A [página Objetos](global-objects.md) globais fornece a lista de todos os objetos fornecidos por HTL.

### Acesso à propriedade {#property-access}

Há duas maneiras de acessar as propriedades de variáveis, com uma notação de pontos ou com uma notação de colchete:

`${currentPage.title}  
${currentPage['title']} or ${currentPage["title"]}`

A notação de pontos simples deve ser preferida na maioria dos casos, e a notação de colchetes deve ser usada para acessar propriedades que contenham caracteres de identificador inválidos ou para acessar as propriedades dinamicamente. Os dois capítulos a seguir fornecerão detalhes sobre esses dois casos.

As propriedades acessadas podem ser funções, no entanto, passar argumentos não é suportado, então apenas funções que não esperam argumentos podem ser acessadas, como getters. Essa é uma limitação desejada, que tem como objetivo reduzir a quantidade de lógica incorporada às expressões. Se necessário, a [`data-sly-use`](block-statements.md#use) declaração pode ser usada para passar parâmetros para a lógica.

Também mostrado no exemplo acima é que as funções getter Java, como `getTitle()`, podem ser acessadas sem pré-pendente e **`get`**reduzindo o caso do caractere a seguir.

### Caracteres de Recuo Válidos Válidos {#valid-indentifier-characters}

Os nomes das variáveis, chamados identificadores, estão em conformidade com determinadas regras. Eles devem começar com uma letra (**`A`**-**`Z`** e **`a`**-)**`z`**ou um sublinhado (**`_`**), e os caracteres subsequentes também podem ser dígitos (**`0`**-**`9`**) ou dois pontos (**`:`**). Letras Unicode, como **`å`** e **`ü`** não podem ser usadas em identificadores.

Considerando que o caractere dois pontos (**:**) é comum em nomes de propriedade AEM, é conveniente que seja um caractere de identificador válido:

`${properties.jcr:title}`

A notação de colchete pode ser usada para acessar propriedades que contenham caracteres de identificador inválidos, como o caractere de espaço no exemplo abaixo:

`${properties['my property']}`

### Acessar membros dinamicamente {#accessing-members-dynamically}

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

```xml
${properties[myVar]}
```

### Manipulação permissiva de valores nulos {#permissive-handling-of-null-values}

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

Booliano representa uma entidade lógica e pode ter dois valores: **`true`**e **`false`**.

`${true} ${false}`

### Números {#numbers}

Há apenas um tipo de número: números inteiros positivos. Embora outros formatos de número, como ponto flutuante, sejam suportados em variáveis, mas não podem ser expressos como literais.

`${42}`

### Strings {#strings}

Representam dados textuais e podem ser únicos ou duplos:

`${'foo'} ${"bar"}`

Além dos caracteres comuns, os seguintes caracteres especiais podem ser usados:

* **`\\`** Caractere de barra invertida
* **`\'`** Aspas simples (ou apóstrofe)
* **`\"`** Aspas duplas
* **`\t`** Tabulação
* **`\n`** Nova linha
* **`\r`** Retorno de carro
* **`\f`** Feed de formulários
* **`\b`** Backspace
* `\uXXXX` O caractere Unicode especificado pelos quatro dígitos hexadecimais XXXX.\
   Algumas sequências de escape unicode úteis são:

   * **\ u 0022** para **"**
   * **\ u 0027** para **'**

Para caracteres não listados acima, um carrossel de barra invertida exibirá um erro.

Estes são alguns exemplos de como usar o escape da string:

```xml
<p>${'it\'s great, she said "yes!"'}</p>
<p title="${'it\'s great, she said \u0022yes!\u0022'}">...</p>
```

o que resultará na saída seguinte, pois o HTL aplicará escape específico ao contexto:

```xml
<p>it&#39;s great, she said &#34;yes!&#34;</p>
<p title="it&#39;s great, she said &#34;yes!&#34;">...</p>
```

### Matrizes {#arrays}

Uma matriz é um conjunto ordenado de valores que podem ser referenciados com um nome e um índice. Os tipos de seus elementos podem ser combinados.

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

```xml
${[1,2,3,4]}
${myArray[2]}
```

Matrizes são úteis para fornecer uma lista de valores do modelo.

```xml
<ul data-sly-list="${[1,2,3,4]}">
  <li>${item}</li>
</ul>
```

## Operadores {#operators}

### Operadores lógicos {#logical-operators}

Esses operadores geralmente são usados com valores booleanos, no entanto, como no javascript, retornam o valor de um dos operandos especificados, portanto, quando usados com valores não booleanos, podem retornar um valor não booliano.

Se um valor puder ser convertido, **`true`**o valor será chamado de truthy. Se um valor puder ser convertido **`false`**, o valor será chamado de falsy. Os valores que podem ser convertidos **`false`** são: variáveis não definidas, valores nulos, número zero e strings vazias.

#### NOT lógico {#logical-not}

**`${!myVar}`** retorna **`false`** se seu operando único puder ser convertido `true`para; caso contrário, retorna **`true`**.

Isso pode ser usado para inverter uma condição de teste, como exibir um elemento somente se não houver páginas secundárias:

```xml
<p data-sly-test="${!currentPage.hasChild}">current page has no children</p>
```

#### AND lógica {#logical-and}

**`${varOne && varTwo}`** retorna `varOne` se for falsificado; caso contrário, retorna **vartwo**.

Esse operador pode ser usado para testar duas condições de uma vez, como verificar a existência de duas propriedades:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

O operador AND lógico também pode ser usado para exibir condicionalmente atributos HTML, pois a HTL remove atributos com valores definidos dinamicamente que avaliam como false ou a uma sequência vazia. Assim, no exemplo abaixo, o **`class`** atributo é mostrado somente se **`logic.showClass`** for truthy e se **`logic.className`** existir e não estiver vazio:

```xml
<div class="${logic.showClass && logic.className}">...</div>
```

#### OR lógica {#logical-or}

**`${varOne || varTwo}`** retorna **varone** se for truthy; caso contrário, retorna **vartwo**.

Esse operador pode ser usado para testar se uma de duas condições se aplica, como verificar a existência de pelo menos uma propriedade:

```xml
<div data-sly-test="${properties.jcr:title || properties.jcr:description}">...</div>
```

Como o operador OR lógico retorna a primeira variável que é truia, também pode ser usada muito conveniente para fornecer valores de fallback.

exibem condicionalmente atributos HTML, pois a HTL remove atributos com valores definidos por expressões que avaliam como falso ou a uma sequência vazia. Assim, o exemplo abaixo exibirá****`properties.jcr:`título se ele existir e não estiver vazio, caso contrário, retornará para descartar **`properties.jcr:description`** se existir e não estiver vazio, caso contrário, a mensagem será exibida "nenhum título ou descrição fornecida":

```xml
<p>${properties.jcr:title || properties.jcr:description || "no title or description provided"}</p>
```

### Operador condicional (ternário) {#conditional-ternary-operator}

**`${varCondition ? varOne : varTwo}`** retorna **`varOne`** se **`varCondition`** for truthy; do contrário, retorne **`varTwo`**.

Normalmente, esse operador pode ser usado para definir condições dentro de expressões, como exibir uma mensagem diferente com base no status da página:

```xml
<p>${currentPage.isLocked ? "page is locked" : "page can be edited"}</p>
```

Uma observação importante, visto que os caracteres dois pontos também são permitidos em identificadores, é melhor separar os operadores ternários com um espaço em branco para fornecer clareza ao analisador:

```xml
<p>${properties.showDescription ? properties.jcr:description : properties.jcr:title}</p>
```

### Operadores de comparação {#comparison-operators}

Os operadores de igualdade e de desigualdade suportam operandos de tipos idênticos. Quando os tipos não correspondem, um erro é exibido.

* As strings são iguais quando têm a mesma sequência de caracteres.
* Os números são iguais quando têm o mesmo valor
* Os booleanos são iguais se ambos forem **`true`** ou ambos **`false`**.

* Variáveis nulas ou não definidas são iguais a si mesmas e entre si.

**`${varOne == varTwo}`** retorna **`true`** se **`varOne`** e **`varTwo`** forem iguais.

**`${varOne != varTwo}`** retorna **`true`** se **`varOne`** e não **`varTwo`** são iguais.

Os operadores relacionais suportam operandos que são números. Para todos os outros tipos, um erro é exibido.

**`${varOne > varTwo}`** retorna **`true`** se **`varOne`** for maior do que **`varTwo`**.

**`${varOne < varTwo}`** retorna **`true`** se **`varOne`** for menor do que **`varTwo`**.

**`${varOne >= varTwo}`** retorna **`true`** se **`varOne`** for maior ou igual **`varTwo`**a.

**`${varOne <= varTwo}`** retorna **`true`** se **`varOne`** for menor ou igual **`varTwo`**a.

### Parênteses de agrupamento {#grouping-parentheses}

O operador de agrupamento **`(`****`)`** controla a precedência da avaliação em expressões.

`${varOne && (varTwo || varThree)}`

## Opções {#options}

<!-- 

Comment Type: draft

<p>TODO: review text below.</p>

 -->

As opções de expressão podem atuar na expressão e modificá-la, ou servir como parâmetros quando usadas em conjunto com declarações bloqueadas.

Tudo depois de **`@`** uma opção:

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

### Formatação de string {#string-formatting}

Opção que substitui os espaços reservados enumerados, {*n*}, com a variável correspondente:

```xml
${'Page {0} of {1}' @ format=[current, total]}
```

### Internacionalização {#internationalization}

Converte a string para o idioma da *fonte atual* (veja abaixo), usando o [dicionário atual](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/i18n-translator). Se nenhuma conversão for encontrada, a string original será usada.

```xml
${'Page' @ i18n}
```

A opção de dica pode ser usada para fornecer um comentário para os tradutores, especificando o contexto no qual o texto é usado:

```xml
${'Page' @ i18n, hint='Translation Hint'}
```

A fonte padrão do idioma é "resource", o que significa que o texto é traduzido para o mesmo idioma que o conteúdo. Isso pode ser alterado para «usuário», o que significa que o idioma é retirado da localidade do navegador ou da localidade do usuário conectado:

```xml
${'Page' @ i18n, source='user'}
```

Fornecer uma localidade explícita substitui as configurações de origem:

```xml
${'Page' @ i18n, locale='en-US'}
```

Para incorporar variáveis em uma sequência traduzida, a opção de formato pode ser usada:

```xml
${'Page {0} of {1}' @ i18n, format=[current, total]}
```

### Junção de matriz {#array-join}

Por padrão, ao exibir uma matriz como texto, o HTL exibirá valores separados por vírgulas (sem espaçamento).

Use a opção de junção para especificar um separador diferente:

```xml
${['one', 'two'] @ join='; '}
```

### Exibir contexto {#display-context}

O contexto de exibição de uma expressão HTL se refere ao seu local na estrutura da página HTML. Por exemplo, se a expressão aparecer no lugar que geraria um nó de texto depois de renderizado, diz-se que está em **`text`** um contexto. Se for encontrada no valor de um atributo, então diz-se que está em **`attribute`** um contexto e assim por diante.

Com exceção do contexto de script (JS) e de estilo (CSS), o HTL detecta automaticamente o contexto de expressões e as ignora adequadamente, para evitar problemas de segurança XSS. No caso de scripts e CSS, o comportamento de contexto desejado deve ser definido explicitamente. Além disso, o comportamento de contexto também pode ser definido explicitamente em qualquer outro caso em que uma substituição do comportamento automático é necessária.

Aqui temos três variáveis em três contextos diferentes: **`properties.link`** ( `uri` contexto), **`properties.title`** (**`attribute`** contexto) e **`properties.text`**(**`text`** contexto). O HTL escapará cada uma dessas maneiras de acordo com os requisitos de segurança de seus respectivos contextos. Nenhuma configuração de contexto explícita é necessária em casos normais, como este:

```xml
<a href="${properties.link}" title="${properties.title}">${properties.text}</a>
```

Para produzir marcação com segurança (ou seja, onde a própria expressão resulta em HTML), o `html` contexto é usado:

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

O escape e a proteção XSS também podem ser desativados:

```xml
<div>${myScript @ context='unsafe'}</div>
```

### Configurações de contexto {#context-settings}

| Contexto | Quando usar | O que faz |
|--- |--- |--- |
| texto | Padrão para conteúdo dentro de elementos | Codifica todos os caracteres especiais HTML. |
| html | Para produzir marcação com segurança | Filtra HTML para atender às regras de política antisamy, removendo o que não corresponde às regras. |
| atributo | Padrão para valores de atributo | Codifica todos os caracteres especiais HTML. |
| uri | Para exibir links e caminhos Padrão para valores de atributo href e src | Valida o URI para escrever como um valor de atributo href ou src, não gera nada se a validação falhar. |
| número | Para exibir números | Valida o URI para conter um número inteiro, gera zero se a validação falhar. |
| Attributename | Padrão para o atributo data-sly ao definir nomes de atributos | Valida o nome do atributo, não gera nada se a validação falhar. |
| Elementname | Padrão para o elemento de dados | Valida o nome do elemento, não gera nada se a validação falhar. |
| Scripttoken | Para identificadores JS, números literais ou strings literais | Valida o token javascript, não gera nada se a validação falhar. |
| Scriptstring | Em strings JS | Codifica caracteres que quebrariam da string. |
| Scriptcomment | Nos comentários JS | Valida o comentário do javascript, não gera nada se a validação falhar. |
| Styletoken | Para identificadores de CSS, números, dimensões, strings, cores hexadecimais ou funções. | Valida o token de CSS, não gera nada se a validação falhar. |
| Stylestring | Em strings CSS | Codifica caracteres que quebrariam da string. |
| Stylecomment | Comentários em CSS | Valida o comentário de CSS, não gera nada se a validação falhar. |
| unsafe | Somente se nenhum dos itens acima fizer o trabalho | Desativa o escape e a proteção XSS completamente. |

