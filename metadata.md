---
product: Adobe Experience Manager
git-repo: https://git.corp.adobe.com/AdobeDocs/experience-manager-htl.fr-FR
index: y
solution-title: Formation et support pour HTL
solution-hub-url: https://docs.adobe.com/content/help/en/experience-manager-cloud-service/sites/home.html
getting-started-title: Prise en main du développement pour AEM
getting-started-url: https://docs.adobe.com/content/help/en/experience-manager-cloud-service/core-concepts/home.html
tutorials-title: Tutoriels AEM
tutorials-url: https://docs.adobe.com/content/help/en/experience-manager-learn/cloud-service/overview.html
translation-type: tm+mt
source-git-commit: d3426d87dce09ac34ff1aca431ff2bfad2f7134a
workflow-type: tm+mt
source-wordcount: '139'
ht-degree: 97%

---


# Métadonnées pour utilisation interne

Les métadonnées figurant dans le système de création GitHub sont hiérarchisées et définies selon les niveaux de précédents croissants suivants.

1. metadata.md
1. Table des matières
1. Article

Les métadonnées définies dans le fichier metadata.md s’appliquent à l’intégralité du référentiel, mais peuvent être remplacées aux niveaux de la table des matières et de l’article. Tout remplacement des métadonnées doit être effectué au niveau le plus bas possible.

Les métadonnées figurant dans le référentiel experience-manager-core-components.en sont le minimum requis.

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

Tables des matières

* `sub-product`
* `user-guide-title`

Article

* `title`
* `description`
* `index: n` (uniquement pour les versions précédentes des composants)

Vous trouverez des informations supplémentaires sur les métadonnées dans le [guide de création interne.](https://docs.adobe.com/help/en/collaborative-doc-instructions/collaboration-guide/markdown/metadata.html#solution-metadata)
