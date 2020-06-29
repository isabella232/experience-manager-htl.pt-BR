---
product: Adobe Experience Manager
git-repo: https://git.corp.adobe.com/AdobeDocs/experience-manager-htl.pt-BR
index: y
solution-title: Aprendizagem e suporte para HTL
solution-hub-url: https://docs.adobe.com/content/help/en/experience-manager-cloud-service/sites/home.html
getting-started-title: Introdução ao desenvolvimento para o AEM
getting-started-url: https://docs.adobe.com/content/help/en/experience-manager-cloud-service/core-concepts/home.html
tutorials-title: Tutoriais do AEM
tutorials-url: https://docs.adobe.com/content/help/br/experience-manager-learn/cloud-service/overview.html
translation-type: tm+mt
source-git-commit: d3426d87dce09ac34ff1aca431ff2bfad2f7134a
workflow-type: tm+mt
source-wordcount: '139'
ht-degree: 20%

---


# Metadados para uso interno

Os metadados no sistema de criação do GitHub são hierárquicos e são definidos como os seguintes níveis crescentes de precedência.

1. metadata.md
1. ToC
1. Artigo

Os metadados definidos no arquivo metadata.md se aplicam a todo o acordo de recompra, mas podem ser substituídos nos níveis ToC e artigo. Qualquer substituição dos metadados deve ser feita no nível mais baixo possível.

Os metadados no repositório experience-manager-core-components.en são o mínimo necessário.

metadata.md

* `product`
* `git-repo`
* `index: y`
* `solution-title`
* `solution-hub-url`
* `getting-started-title`
* `getting-started-url`
* `tutorials-title`
* `tutorials-url`

ToCs

* `sub-product`
* `user-guide-title`

Artigo

* `title`
* `description`
* `index: n` (somente para versões anteriores de componentes)

Informações adicionais sobre os metadados podem ser encontradas no guia de criação [interno.](https://docs.adobe.com/help/en/collaborative-doc-instructions/collaboration-guide/markdown/metadata.html#solution-metadata)
