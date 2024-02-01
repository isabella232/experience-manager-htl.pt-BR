---
solution: Experience Manager
type: Documentation
product: adobe experience manager
git-repo: https://github.com/AdobeDocs/experience-manager-htl.pt-BR
index: y
recommendations: noDisplay
source-git-commit: f891460cc7f247723c3e78031aba385faca6acd7
workflow-type: ht
source-wordcount: '96'
ht-degree: 100%

---


# Metadados para uso interno

Os metadados no sistema de autoria do GitHub são hierárquicos e definidos nos seguintes níveis crescentes de precedentes.

1. metadata.md
1. Índice
1. Artigo

Os metadados definidos no arquivo metadata.md se aplicam a todo o repositório, mas podem ser substituídos nos níveis de índice e artigo. Qualquer substituição dos metadados deve ser feita no nível mais baixo possível.

Os metadados no repositório experience-manager-core-components.en são o mínimo necessário.

metadata.md

* `product`
* `git-repo`
* `index: y`

Não está mais em uso:

* `solution-title`
* `solution-hub-url`
* `getting-started-title`
* `getting-started-url`
* `tutorials-title`
* `tutorials-url`

Índices

* `sub-product`
* `user-guide-title`

Artigo

* `title`
* `description`
* `index: n` (somente para versões anteriores de componentes)

Informações adicionais sobre os metadados podem ser encontradas no [guia de criação interno.](https://experienceleague.adobe.com/docs/authoring-guide-exl/using/authoring/features/metadata.html?lang=pt-BR#solution)
