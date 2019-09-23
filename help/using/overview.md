---
title: Présentation d’AEM HTL
seo-title: Présentation de la documentation technique AEM HTL.
description: L’objectif de HTL, pris en charge par AEM, est d’offrir une structure web extrêmement productive au niveau de l’entreprise qui renforce la sécurité et permet aux développeurs HTML sans connaissance de Java de participer de manière plus efficace aux projets AEM.
seo-description: Ce document expose les principes et l’objectif du langage de modèle HTML - HTML - pris en charge par Adobe Experience Manager. HTL est une structure Web d’entreprise hautement productive qui améliore la sécurité et permet aux développeurs HTML dépourvus de connaissances Java de mieux participer aux projets AEM.
uuid: 8f486325-0a1b-4186-a998-96fc0034c44a
contentOwner: Utilisateur
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: introduction
content-type: référence
discoiquuid: 8f779e08-94c7-43bc-a6e5-d81a9f818c5c
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 1d4565a4cffa6e5d42d6a5242f7ce62203dc7c63

---


# Présentation {#overview}

L’objectif du langage HTL (HTML Template Language), pris en charge par Adobe Experience Manager (AEM), est d’offrir une structure web extrêmement productive au niveau de l’entreprise qui renforce la sécurité et permet aux développeurs HTML sans connaissance de Java de participer de manière plus efficace aux projets AEM.

Le langage HTL a été introduit avec AEM 6.0 et remplace JSP (JavaServer Pages) en tant que système de modèle côté serveur préféré et recommandé pour HTML. Pour les développeurs web qui souhaitent créer des sites web d’entreprise performants, le langage HTL contribue à améliorer l’efficacité au niveau de la sécurité et du développement.

## Sécurité renforcée {#increased-security}

Le langage HTL accroît la sécurité des sites qui l’utilisent dans leur mise en œuvre, par rapport à JSP et la plupart des autres systèmes de modèle, car HTL est capable d’appliquer automatiquement l’échappement approprié adapté au contexte à toutes les variables qui s’affichent sur la couche de présentation. HTL rend cela possible, car il comprend la syntaxe HTML et l’utilise pour ajuster l’échappement nécessaire aux expressions, en fonction de leur position dans les balises. Cela aura pour conséquence, par exemple, que des expressions placées dans les attributs `href` ou `src` seront placées dans une séquence d’échappement différemment des expressions placées dans d’autres attributs ou ailleurs.

Si le même résultat peut être obtenu avec des langages de modèle en JSP, le développeur doit s’assurer manuellement que l’échappement adéquat est appliqué à chaque variable. Comme une simple omission ou erreur sur un échappement appliqué suffit potentiellement à entraîner une vulnérabilité de type attaque multisite par scripts (XSS), nous avons décidé d’automatiser cette tâche avec HTL. Si nécessaire, les développeurs peuvent tout de même indiquer un échappement différent sur les expressions, mais avec HTL, le comportement par défaut est bien plus susceptible de corresponde au comportement souhaité, ce qui réduit la probabilité d’erreurs.

## Développement simplifié   {#simplified-development}

Il est facile d’apprendre le langage de modèle HTML. Ses fonctionnalités sont volontairement limitées pour assurer un apprentissage simple et direct. Il comporte également des mécanismes performants pour structurer les balises et appeler la logique, tout en appliquant toujours une séparation stricte des problèmes entre l’annotation et la logique. HTL lui-même est aux standards du HTML5, car il utilise des expressions et des attributs de données pour annoter les balises avec le comportement dynamique souhaité, ce qui signifie qu’il n’interrompt pas la disponibilité des balises, qui restent lisibles. Notez que l’évaluation des expressions et des attributs de données est effectuée entièrement du côté serveur et ne sera pas visible du côté client, où toute autre structure JavaScript souhaitée peut être utilisée sans interférence.

Ces fonctionnalités permettent aux développeurs HTML sans connaissance préalable de Java et avec peu de connaissances spécifiques du produit de modifier les modèles HTL. Ils peuvent ainsi faire partie de l’équipe de développement et cela simplifie la collaboration avec les développeurs Java de plein-pile. Réciproquement, cela permet aux développeurs Java de se concentrer sur le code principal sans se soucier du HTML.

## Réduction des coûts   {#reduced-costs}

Une sécurité renforcée, un développement simplifié et une meilleure collaboration des équipes se traduisent dans les projets AEM par des efforts réduits, une mise sur le marché plus rapide et un moindre coût total de propriété du parc.

Concrètement, d’après nos observations lors de la nouvelle mise en œuvre du site Adobe.com avec le langage HTL, le coût et la durée du projet peuvent être réduits d’environ 25 %.

![](assets/chlimage_1.png)

Le diagramme ci-dessus montre que HTL permet potentiellement d’améliorer l’efficacité des domaines suivants :

* **HTML/CSS/JS :** Comme les développeurs HTML peuvent modifier directement les modèles HTL, les conceptions frontales n’ont plus besoin d’être mises en œuvre séparément du projet AEM, mais peuvent être mises en œuvre directement sur les composants proprement dits d’AEM. Cela permet de réduire les itérations laborieuses pour les développeurs Java de plein-pile.
* **JSP/HTL :** Comme HTL ne nécessite en soi aucune connaissance de Java et est simple à écrire, tout développeur maîtrisant l’HTML peut modifier les modèles.
* **Java :** Grâce à l’API Use claire et simple d’utilisation proposée par HTL, l’interface de logique métier est clarifiée, ce qui profite également au développement Java dans son ensemble.

**À lire aussi :**

* [Prise en main du langage HTL](getting-started.md)

