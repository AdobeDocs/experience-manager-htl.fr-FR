---
solution: Experience Manager
type: Documentation
git-repo: https://github.com/AdobeDocs/experience-manager-htl.en
index: true
landing-page-name: experience-manager
landing-page-breadcrumb-title: AEM
recommendations: noDisplay
product_v2: id: fd1f54a9-f50c-467d-8956-cebbaf4f3eb8
usetq: true
source-git-commit: d9500886a302eafb90cb5ece6dec09849acd7ae2
workflow-type: tm+mt
source-wordcount: 86
ht-degree: 2%

---


# Métadonnées à usage interne

Le système de création GitHub définit les métadonnées de manière hiérarchique, avec des niveaux de précédent croissants, comme illustré ci-dessous :

1. metadata.md
1. ToC
1. Article

Les métadonnées définies dans le fichier metadata.md s’appliquent à l’ensemble du référentiel, mais elles peuvent être remplacées au niveau de la table des matières et de l’article. Tout remplacement des métadonnées doit être effectué au niveau le plus bas possible.

Les métadonnées du référentiel `experience-manager-core-components.en` sont le minimum requis.

metadata.md

* `product`
* `git-repo`
* `index: true`

N’est plus utilisé :

* `solution-title`
* `solution-hub-url`
* `getting-started-title`
* `getting-started-url`
* `tutorials-title`
* `tutorials-url`

ToCs

* `sub-product`
* `user-guide-title`

Article

* `title`
* `description`
* `index: false` (uniquement pour les versions précédentes des composants)

