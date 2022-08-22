---
title: Histórico do HTL
description: Para usuários de AEM longos, esse documento fornece o plano de fundo sobre HTL, como substitui o JSP e a alteração de nome do Sightly.
exl-id: 00985b35-2130-4946-959a-0a09a34a0f05
source-git-commit: 5ab1275c984135fe946f36905bbc979cf19edd80
workflow-type: tm+mt
source-wordcount: '542'
ht-degree: 53%

---


# Histórico do HTL {#history-of-htl}

Para usuários de AEM longos, esse documento fornece o plano de fundo sobre HTL, como substitui o JSP e a alteração de nome do Sightly.

## Anteriormente conhecido como Sightly {#sightly}

Linguagem de modelo de HTML (HTL) é o sistema de modelo preferencial e recomendado do lado do servidor para o HTML no Adobe Experience Manager. Substitui as páginas JSP (JavaServer Pages), conforme usadas em versões anteriores do AEM.

## HTL em vez de JSP {#htl-over-jsp}

Recomenda-se que novos projetos AEM usem a Linguagem de modelo HTML, pois ela oferece vários benefícios em comparação ao JSP. No entanto, para projetos existentes, a migração só faz sentido se for estimado que o esforço será menor do que manter os JSPs existentes para os próximos anos.

Mas mudar para HTL não é, necessariamente, uma opção radical, pois os componentes gravados em HTL são compatíveis com componentes gravados em JSP ou ESP. Isso significa que os projetos existentes podem usar HTL para novos componentes, mantendo o JSP para os componentes existentes.

Inclusive no mesmo componente, os arquivos HTL podem ser usados junto a JSPs e ESPs. O exemplo a seguir mostra, na **linha 1**, como incluir um arquivo HTL de um arquivo JSP; e na **linha 2**, como um arquivo JSP pode ser incluído de um arquivo HTL:

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

## Perguntas frequentes  {#frequently-asked-questions}

Essas são algumas perguntas comumente feitas por desenvolvedores de AEM experientes, novos no HTL.

### O HTL tem alguma limitação que o JSP não tem? {#limitations}

Na verdade, o HTL não tem limitações em comparação com o JSP. O que pode ser feito com o JSP também deve ser realizável com o HTL. No entanto, HTL é por design mais rigoroso que JSP em vários aspectos. O que pode ser obtido em um único arquivo JSP, pode precisar ser separado em uma classe Java ou em um arquivo JavaScript para ser alcançável em HTL. Mas isso é geralmente desejado para garantir uma boa separação de interesses entre a lógica e a marcação.

### O HTL suporta Bibliotecas de tags JSP? {#tag-libraries}

Não, mas como mostrado no [Carregamento de bibliotecas de clientes](getting-started.md#loading-client-libraries) do documento Introdução, as instruções de modelo e chamada oferecem um padrão semelhante.

### Os recursos do HTL podem ser estendidos em um projeto AEM? {#extended}

Não podem. O HTL tem poderosos mecanismos de extensão para reutilização da lógica (a variável [Usar API](#use-api-for-accessing-logic)) e de marcação (as instruções template e call ), que podem ser usadas para modular o código dos projetos.

### Quais são os principais benefícios do HTL em relação ao JSP? {#benefits}

A segurança e a eficiência dos projetos são os principais benefícios, detalhados na [Visão geral.](overview.md)

### O JSP vai acabar? {#go-away}

Não existem planos nesse sentido.

## Qual é o nome? {#what-is-in-a-name}

Nas AEM 6.0 e 6.1, o HTL foi referido como **Sightly**. Adobe renomeou para **Linguagem de modelo de HTML** ou **HTL** para esclarecer para que serve a especificação e alinhar-se às diretrizes de nomenclatura do Adobe em geral. Essa alteração de nome entrou em vigor em agosto de 2016 e se aplica à versão AEM 6.0 e posteriores.

>[!NOTE]
>
>Essa alteração não afeta o código ou a API, portanto, a compatibilidade não foi afetada. Para obter mais informações, [consulte este vídeo de anúncio.](https://helpx.adobe.com/br/experience-manager/how-to/announce-htl.html)

Saber mais sobre HTL e um ótimo lugar para começar é nosso oficial [Guia de Introdução à Linguagem de modelo de HTML (HTL).](overview.md)
