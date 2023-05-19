---
title: Histórico do HTL
description: Para usuários antigos do AEM, esse documento fornece informações sobre o HTL, sua mudança de nome (ele era anteriormente conhecido como Sightly) e como ele substitui o JSP.
exl-id: 00985b35-2130-4946-959a-0a09a34a0f05
source-git-commit: 5ab1275c984135fe946f36905bbc979cf19edd80
workflow-type: tm+mt
source-wordcount: '542'
ht-degree: 100%

---


# Histórico do HTL {#history-of-htl}

Para usuários antigos do AEM, esse documento fornece informações sobre o HTL, sua mudança de nome (ele era anteriormente conhecido como Sightly) e como ele substitui o JSP.

## Anteriormente conhecido como Sightly {#sightly}

O HTL (Linguagem de modelo HTML) é o sistema de modelo do lado do servidor preferencial e recomendado para HTML no Adobe Experience Manager. Substitui as páginas JSP (JavaServer Pages), conforme usadas em versões anteriores do AEM.

## HTL em vez de JSP {#htl-over-jsp}

Recomenda-se que novos projetos AEM usem a Linguagem de modelo HTML, pois ela oferece vários benefícios em comparação ao JSP. No entanto, para projetos existentes, a migração só faz sentido se for estimado que o esforço será menor do que manter os JSPs existentes para os próximos anos.

Mas mudar para HTL não é, necessariamente, uma opção radical, pois os componentes gravados em HTL são compatíveis com componentes gravados em JSP ou ESP. Isso significa que os projetos existentes podem usar HTL para novos componentes, mantendo o JSP para os componentes existentes.

Inclusive no mesmo componente, os arquivos HTL podem ser usados junto a JSPs e ESPs. O exemplo a seguir mostra, na **linha 1**, como incluir um arquivo HTL de um arquivo JSP; e na **linha 2**, como um arquivo JSP pode ser incluído de um arquivo HTL:

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

## Perguntas frequentes  {#frequently-asked-questions}

Essas são algumas perguntas frequentes feitas por desenvolvedores experientes no AEM, mas novos no HTL.

### O HTL tem alguma limitação que o JSP não tem? {#limitations}

Na verdade, o HTL não tem limitações em comparação com o JSP. O que pode ser feito com o JSP também deve ser realizável com o HTL. No entanto, por design, o HTL é mais rigoroso que o JSP em vários aspectos. O que é possível obter com um único arquivo JSP pode precisar ser separado em uma classe Java ou em um arquivo JavaScript para ser realizável no HTL. Mas isso é geralmente recomendado para garantir uma boa separação de interesses entre a lógica e a marcação.

### O HTL é compatível com Bibliotecas de tags JSP? {#tag-libraries}

Não, mas conforme mostrado na seção [Carregamento de bibliotecas de clientes](getting-started.md#loading-client-libraries) do documento de introdução, as declarações de modelo e chamada oferecem um padrão semelhante.

### Os recursos do HTL podem ser estendidos em um projeto AEM? {#extended}

Não podem. O HTL tem mecanismos de extensão poderosos para reutilização de lógica (a [API de uso](#use-api-for-accessing-logic)) e de marcação (as declarações de modelo e chamada), que podem ser usados para modular o código dos projetos.

### Quais são os principais benefícios do HTL em relação ao JSP? {#benefits}

A segurança e a eficiência dos projetos são os principais benefícios, detalhados na [Visão geral.](overview.md)

### O JSP vai acabar? {#go-away}

Não existem planos para isso.

## Por que o nome foi alterado? {#what-is-in-a-name}

No AEM 6.0 e 6.1, o HTL era chamado de **Sightly**. A Adobe o renomeou para **Linguagem de modelo HTML** ou **HTL** para esclarecer a sua especificação e alinhar-se às diretrizes gerais de nomenclatura da Adobe. Essa alteração de nome entrou em vigor em agosto de 2016 e se aplica à versão AEM 6.0 e posteriores.

>[!NOTE]
>
>Essa alteração não afeta o código ou a API, portanto, a compatibilidade não foi afetada. Para obter mais informações, [assista este comunicado em vídeo.](https://helpx.adobe.com/br/experience-manager/how-to/announce-htl.html)

Para saber mais sobre o HTL, um ótimo local para começar é em nosso [Guia de introdução à Linguagem de Modelo HTML (HTL)](overview.md).
