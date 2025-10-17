---
solution: Experience Manager
type: Documentation
product: adobe experience manager
git-repo: https://github.com/AdobeDocs/experience-manager-htl.fr-FR
index: y
landing-page-name: experience-manager
landing-page-breadcrumb-title: Experience Manager
recommendations: noDisplay
source-git-commit: 50bd3425ac316adf8b4b2560d4782cd1d2efa9c2
workflow-type: tm+mt
source-wordcount: '81'
ht-degree: 40%

---


# Métadonnées pour utilisation interne

Le système de création GitHub définit les métadonnées de manière hiérarchique, avec des niveaux de précédent croissants, comme illustré ci-dessous :

1. metadata.md
1. Table des matières
1. Article

Les métadonnées définies dans le fichier metadata.md s’appliquent à l’ensemble du référentiel, mais elles peuvent être remplacées au niveau de la table des matières et de l’article. Tout remplacement des métadonnées doit être effectué au niveau le plus bas possible.

Les métadonnées du référentiel `experience-manager-core-components.en` sont le minimum requis.

metadata.md

* `product`
* `git-repo`
* `index: y`

N’est plus utilisé :

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

