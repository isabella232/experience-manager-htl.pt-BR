---
title: Linguagem de expressão do HTL
description: A Linguagem de modelo HTML usa uma linguagem de expressão para acessar as estruturas de dados que fornecem os elementos dinâmicos da saída HTML.
exl-id: 57e3961b-8c84-4d56-a049-597c7b277448
source-git-commit: 89b9e89254f341e74f1a5a7b99735d2e69c8a91e
workflow-type: ht
source-wordcount: '1852'
ht-degree: 100%

---

# Linguagem de expressão do HTL {#htl-expression-language}

A Linguagem de modelo HTML usa uma linguagem de expressão para acessar as estruturas de dados que fornecem os elementos dinâmicos da saída HTML. Essas expressões são delimitadas pelos caracteres `${` e `}`. Para evitar HTML malformado, as expressões só podem ser usadas em valores de atributo, em conteúdo de elementos ou comentários.

```xml
<!-- ${component.path} -->
<h1 class="${component.name}">
    ${properties.jcr:title}
</h1>
```

As expressões podem ser em escape ao preceder um caractere `\`. Por exemplo, `\${test}` renderizará `${test}`.

>[!NOTE]
>
>Para experimentar os exemplos fornecidos nesta página, pode-se usar um ambiente de execução ativo chamado de [Read Eval Print Loop](https://github.com/Adobe-Marketing-Cloud/aem-sightly-repl).

A sintaxe de expressão inclui [variáveis](#variables), [literais](#literals), [operadores](#operators) e [opções](#options):

## Variáveis {#variables}

Variáveis são contêineres que armazenam valores ou objetos de dados. Os nomes das variáveis são chamados de identificadores.

Sem precisar especificar nada, o HTL fornece acesso a todos os objetos que normalmente estavam disponíveis em JSP após incluir `global.jsp`. A página [Objetos Globais](global-objects.md) fornece a lista de todos os objetos aos quais o HTL forneceu acesso.

### Acesso a propriedades {#property-access}

Há duas maneiras de acessar propriedades de variáveis: com uma notação de pontos ou com uma notação de colchetes:

```xml
${currentPage.title}  
${currentPage['title']} or ${currentPage["title"]}
```

A notação de pontos mais simples deve ser preferida para a maioria dos casos, e a notação de colchetes deve ser usada para acessar propriedades que contêm caracteres de identificador inválidos ou para acessar propriedades dinamicamente. Os dois capítulos seguintes fornecerão pormenores sobre esses dois casos.

As propriedades acessadas podem ser funções, no entanto, a transmissão de argumentos não é suportada. Portanto, somente as funções que não esperam argumentos podem ser acessadas, como getters. Essa é uma limitação desejada, que tem como objetivo reduzir a quantidade de lógica incorporada às expressões. Se necessário, a instrução [`data-sly-use`](block-statements.md#use) pode ser usada para transmitir parâmetros à lógica.

O exemplo acima também mostra que as funções getter do Java, como `getTitle()`, podem ser acessadas sem preceder o `get` e passando para minúscula o caractere seguinte.

### Caracteres de identificador válidos {#valid-identifier-characters}

Os nomes das variáveis, chamados de identificadores, estão em conformidade com determinadas regras. Eles devem começar com uma letra (`A`-`Z` e `a`-`z`) ou um sublinhado (`_`), e os caracteres subsequentes também podem ser dígitos (`0`-`9`) ou dois pontos (`:`). Letras Unicode como `å` e `ü` não podem ser usadas em identificadores.

Considerando que o caractere dois pontos (`:`) é comum em nomes de propriedades AEM, é necessário enfatizar que ele é convenientemente um caractere identificador válido:

`${properties.jcr:title}`

A notação de colchetes pode ser usada para acessar propriedades que contêm caracteres de identificador inválidos, como o caractere de espaço, no exemplo abaixo:

`${properties['my property']}`

### Acessar membros dinamicamente {#accessing-members-dynamically}

```xml
${properties[myVar]}
```

### Tratamento permissivo de valores nulos {#permissive-handling-of-null-values}

```xml
${currentPage.lastModified.time.toString}
```

## Literais {#literals}

Um literal é uma notação para representar um valor fixo.

### Booleano {#boolean}

Booleano representa uma entidade lógica e pode ter dois valores: `true` e `false`.

`${true} ${false}`

### Números {#numbers}

Há somente um tipo de número: números inteiros positivos. Embora outros formatos de número, como ponto flutuante, sejam suportados em variáveis, eles não podem ser expressos como literais.

`${42}`

### Cadeias de caracteres {#strings}

As cadeias de caracteres representam dados textuais e podem estar entre aspas simples ou duplas:

`${'foo'} ${"bar"}`

Além de caracteres comuns, os caracteres especiais a seguir podem ser usados:

* `\\` Caractere de barra invertida
* `\'` Aspas simples (ou apóstrofo)
* `\"` Aspas duplas
* `\t` Guia
* `\n` Nova linha
* `\r` Retorno de carro
* `\f` Avanço de formulário
* `\b` Backspace
* `\uXXXX` O caractere Unicode especificado pelos quatro dígitos hexadecimais XXXX.\
   Algumas sequências de escape Unicode úteis são:

   * `\u0022` para `"`
   * `\u0027` para `'`

Para caracteres não listados acima, preceder um caractere de barra invertida exibirá um erro.

Estes são alguns exemplos de como usar o escape de cadeia de caracteres:

```xml
<p>${'it\'s great, she said "yes!"'}</p>
<p title="${'it\'s great, she said \u0022yes!\u0022'}">...</p>
```

que resultará na seguinte saída, porque o HTL aplicará o escape específico do contexto:

```xml
<p>it&#39;s great, she said &#34;yes!&#34;</p>
<p title="it&#39;s great, she said &#34;yes!&#34;">...</p>
```

### Matrizes {#arrays}

Uma matriz é um conjunto ordenado de valores que pode ser referenciado por um nome e um índice. Os tipos de seus elementos podem ser misturados.

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

Normalmente, esses operadores são usados com valores booleanos. No entanto, como em JavaScript, eles retornam o valor de um dos operandos especificados, de modo que, quando usados com valores não booleanos, podem retornar um valor não booleano.

Se um valor puder ser convertido em `true`, o valor será chamado de truthy. Se um valor puder ser convertido em `false`, o valor será chamado de falsy. Os valores que podem ser convertidos em `false` são variáveis indefinidas, valores nulos, o número zero e cadeias de caracteres vazias.

#### NOT lógico {#logical-not}

`${!myVar}` retorna `false` se o seu único operando puder ser convertido em `true`; caso contrário, retornará `true`.

Por exemplo, isso pode ser usado para inverter uma condição de teste, como exibir um elemento somente se não houver páginas filhas:

```xml
<p data-sly-test="${!currentPage.hasChild}">current page has no children</p>
```

#### AND lógico {#logical-and}

`${varOne && varTwo}` retorna `varOne` se for falsy; caso contrário, retornará `varTwo`.

Esse operador pode ser usado para testar duas condições de uma só vez, como verificar a existência de duas propriedades:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

O operador AND lógico também pode ser usado para exibir condicionalmente atributos HTML, porque o HTL remove atributos com valores definidos dinamicamente que são avaliados como falso ou como uma cadeia de caracteres vazia. Portanto, no exemplo abaixo, o atributo `class` só é mostrado se `logic.showClass` for truthy e se `logic.className` existir e não estiver vazio:

```xml
<div class="${logic.showClass && logic.className}">...</div>
```

#### OR lógico {#logical-or}

`${varOne || varTwo}` retorna `varOne` se for truthy; caso contrário, retornará `varTwo`.

Esse operador pode ser usado para testar se uma de duas condições se aplicar, como verificar a existência de pelo menos uma propriedade:

```xml
<div data-sly-test="${properties.jcr:title || properties.jcr:description}">...</div>
```

Uma vez que o operador OR lógico retorna a primeira variável que seja truthy, ela também pode ser usada para fornecer valores de fallback.

Ele também pode ser usado para exibir condicionalmente atributos HTML, porque o HTL remove atributos com valores definidos por expressões que avaliam como falso ou como uma cadeia de caracteres vazia. Portanto, o exemplo abaixo exibirá **`properties.jcr:`** title se existir e não estiver vazio, senão, voltará a exibir **`properties.jcr:description`** se existir e não estiver vazio, ou então, exibirá a mensagem &quot;nenhum título ou descrição fornecida&quot;:

```xml
<p>${properties.jcr:title || properties.jcr:description || "no title or description provided"}</p>
```

### Operador condicional (ternário) {#conditional-ternary-operator}

`${varCondition ? varOne : varTwo}` retorna `varOne` se `varCondition` for truthy; caso contrário, retornará `varTwo`.

Normalmente, esse operador pode ser usado para definir condições em expressões, como exibir uma mensagem diferente com base no status da página:

```xml
<p>${currentPage.isLocked ? "page is locked" : "page can be edited"}</p>
```

>[!TIP]
>
>Uma vez que caracteres de dois pontos também são permitidos em identificadores, é melhor separar os operadores ternários com um espaço em branco para fornecer clareza ao analisador:

```xml
<p>${properties.showDescription ? properties.jcr:description : properties.jcr:title}</p>
```

### Operadores de comparação {#comparison-operators}

Os operadores de igualdade e desigualdade só oferecem suporte a operandos que sejam de tipos idênticos. Quando os tipos não correspondem, um erro é exibido.

* As cadeias de caracteres são iguais quando têm a mesma sequência de caracteres.
* Os números são iguais quando têm o mesmo valor
* Os booleanos são iguais se ambos forem `true` ou `false`.
* Variáveis nulas ou indefinidas são iguais a si mesmas e entre si.

`${varOne == varTwo}` retorna `true` se `varOne` e `varTwo` forem iguais.

`${varOne != varTwo}` retorna `true` se `varOne` e `varTwo` não forem iguais.

Os operadores relacionais suportam somente operandos que sejam números. Para todos os outros tipos, é exibido um erro.

`${varOne > varTwo}` retorna `true` se `varOne` for maior que `varTwo`.

`${varOne < varTwo}` retorna `true` se `varOne` for menor que `varTwo`.

`${varOne >= varTwo}` retorna `true` se `varOne` for maior ou igual a `varTwo`.

`${varOne <= varTwo}` retorna `true` se `varOne` for menor ou igual a `varTwo`.

### Parênteses de agrupamento {#grouping-parentheses}

O operador de agrupamento `()` controla a precedência da avaliação em expressões.

`${varOne && (varTwo || varThree)}`

## Opções {#options}

As opções de expressão podem atuar na expressão e modificá-la ou servir como parâmetros quando usadas junto a instruções em bloco.

Tudo depois de `@` é uma opção:

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

Expressões paramétricas contendo somente opções também são possíveis:

```xml
${@ optOne, optTwo=bar}
```

### Formatação de cadeias de caracteres {#string-formatting}

Opção que substitui os espaços reservados enumerados, {*n*}, pela variável correspondente:

```xml
${'Page {0} of {1}' @ format=[current, total]}
```

## Manipulação de URL {#url-manipulation}

Um novo conjunto de manipulações de URL está disponível.

Veja os seguintes exemplos de uso:

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

O `@extension` funciona em todos os cenários, verificando se a extensão deve ser adicionada ou não.

```xml
${ link @ extension = 'html' }
```

### Formatação de data/número {#number-date-formatting}

O HTL permite a formatação nativa de números e datas, sem gravar código personalizado. Isso também oferece suporte para fuso horário e localidade.

Os exemplos a seguir mostram que o formato é especificado primeiro, depois o valor que precisa de formatação:

```xml
<h2>${ 'dd-MMMM-yyyy hh:mm:ss' @
           format=currentPage.lastModified,
           timezone='PST',
           locale='fr'}</h2>

<h2>${ '#.00' @ format=300}</h2>
```

>[!NOTE]
>
>Para detalhes completos sobre o formato que pode ser usado, consulte [Especificação de HTL](https://github.com/Adobe-Marketing-Cloud/htl-spec/blob/master/SPECIFICATION.md).

### Internacionalização {#internationalization}

Traduz a cadeia de caracteres para o idioma da *origem* atual (veja abaixo), usando o [dicionário](https://experienceleague.adobe.com/docs/experience-manager-65/developing/components/internationalization/i18n-translator.html?lang=pt-BR) atual. Se nenhuma tradução for encontrada, a cadeia de caracteres original será usada.

```xml
${'Page' @ i18n}
```

A opção de dica pode ser usada para fornecer um comentário aos tradutores, especificando o contexto em que o texto é usado:

```xml
${'Page' @ i18n, hint='Translation Hint'}
```

A fonte padrão do idioma é `resource`, o que significa que o texto é traduzido para o mesmo idioma do conteúdo. Isso pode ser alterado para `user`, o que significa que o idioma é obtido do local do navegador ou do local do usuário conectado:

```xml
${'Page' @ i18n, source='user'}
```

Fornecer um local explícito substitui as configurações de origem:

```xml
${'Page' @ i18n, locale='en-US'}
```

Para incorporar variáveis em uma cadeia de caracteres traduzida, a opção de formato pode ser usada:

```xml
${'Page {0} of {1}' @ i18n, format=[current, total]}
```

### Junção de matrizes {#array-join}

Por padrão, ao exibir uma matriz como texto, o HTL exibirá valores separados por vírgula (sem espaçamento).

Use a opção de junção para especificar um separador diferente:

```xml
${['one', 'two'] @ join='; '}
```

### Exibir contexto {#display-context}

O contexto de exibição de uma expressão HTL refere-se ao seu local dentro da estrutura da página HTML. Por exemplo, se a expressão aparecer no lugar que produziria um nó de texto depois de renderizada, então ela estará em um contexto de `text`. Se for encontrado dentro do valor de um atributo, ele estará em um contexto de `attribute`, e assim por diante.

Com exceção dos contextos de script (JS) e estilo (CSS), o HTL detectará automaticamente o contexto das expressões e as escapará adequadamente, para evitar problemas de segurança XSS. No caso de scripts e CSS, o comportamento de contexto desejado deve ser explicitamente definido. Além disso, o comportamento de contexto também pode ser explicitamente definido em qualquer outro caso em que uma substituição do comportamento automático seja desejada.

Temos aqui três variáveis em três contextos diferentes:

* `properties.link` (contexto `uri` )
* `properties.title` (contexto `attribute` )
* `properties.text` (contexto `text` )

O HTL escapará de cada um deles de forma diferente, de acordo com os requisitos de segurança de seus respectivos contextos. Nenhuma configuração de contexto explícita é necessária em casos normais, como este:

```xml
<a href="${properties.link}" title="${properties.title}">${properties.text}</a>
```

Para exportar a marcação com segurança (ou seja, onde a própria expressão avalia para HTML), o contexto `html` é usado:

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

A proteção de escape e XSS também pode ser desativada:

```xml
<div>${myScript @ context='unsafe'}</div>
```

### Configurações de contexto {#context-settings}

| Contexto | Quando usar | O que faz |
|--- |--- |--- |
| `text` | Padrão, para conteúdo dentro de elementos | Codifica todos os caracteres especiais HTML. |
| `html` | Para gerar marcação com segurança | Filtra o HTML para atender às regras de política do AntiSamy, removendo o que não corresponde às regras. |
| `attribute` | Padrão, para valores de atributo | Codifica todos os caracteres especiais HTML. |
| `uri` | Para exibir links e caminhos Padrão para valores de atributos href e src | Valida o URI para gravar como um valor de atributo href ou src, não gera nada se a validação falhar. |
| `number` | Para exibir números | Valida o URI para conter um inteiro, e gera zero se a validação falhar. |
| `attributeName` | Padrão, para atributo data-sly ao definir nomes de atributo | Valida o nome do atributo, não gera nada se a validação falhar. |
| `elementName` | Padrão, para data-sly-element | Valida o nome do elemento, não gera nada se a validação falhar. |
| `scriptToken` | Para identificadores JS, números literais ou cadeias de caracteres literais | Valida o token JavaScript, não gera nada se a validação falhar. |
| `scriptString` | Em cadeias de caracteres JS | Codifica caracteres que romperiam a cadeia de caracteres. |
| `scriptComment` | Em comentários JS | Valida o comentário do JavaScript, não gera nada se a validação falhar. |
| `styleToken` | Para identificadores CSS, números, dimensões, cadeias de caracteres, cores hexadecimais ou funções. | Valida o token CSS, não gera nada se a validação falhar. |
| `styleString` | Em cadeias de caracteres CSS | Codifica caracteres que romperiam a cadeia de caracteres. |
| `styleComment` | Em comentários CSS | Valida o comentário CSS, não gera nada se a validação falhar. |
| `unsafe` | Somente se nenhuma das opções acima executar a tarefa | Desativa completamente o escape e a proteção XSS. |
