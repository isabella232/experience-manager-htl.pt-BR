---
title: A especificação do HTL
description: Consulte a especificação do HTL para obter informações detalhadas sobre sintaxe.
exl-id: c0657476-4db6-4fad-ad87-9252b5003237
source-git-commit: 88edbd2fd66de960460df5928a3b42846d32066b
workflow-type: ht
source-wordcount: '152'
ht-degree: 100%

---


# A especificação do HTL {#htl-specification}

A Linguagem de modelo HTML (HTL) é o sistema de modelo do lado do servidor preferencial e recomendado para HTML.

## Camadas HTL {#layers}

O HTL, conforme utilizado no AEM, pode ser definido por várias camadas.

1. **[Especificação do HTL](https://github.com/adobe/htl-spec)** - o HTL é uma especificação de código aberto e independente de plataforma que pode ser implementada livremente por qualquer pessoa. Suas especificações são mantidas no repositório do GitHub.
1. **[Mecanismo de script HTL do Sling](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html)** - o projeto Sling criou a implementação de referência do HTL, que é usada pelo AEM. Sua documentação é mantida pelo projeto Sling.
1. **[Extensões do AEM](aem-extensions.md)** - o AEM se baseia no mecanismo de script HTL do Sling para oferecer aos desenvolvedores recursos convenientes e específicos ao AEM. Essas extensões são documentadas como parte desse conjunto de documentações.

Utilize os links acima para acessar a documentação dedicada de todas as camadas do HTL usadas pelo AEM.
