---
title: Présentation de HTL
description: Découvrez comment AEM prend en charge le langage HTL (HTML Template Language) pour fournir un framework web productif au niveau de l’entreprise, ce qui renforce la sécurité et permet aux développeurs de HTML qui n’ont aucune connaissance sur Java de mieux participer aux projets AEM.
exl-id: 5d06ff25-d681-4b95-8375-c28a8364eb7e
source-git-commit: 5ab1275c984135fe946f36905bbc979cf19edd80
workflow-type: ht
source-wordcount: '711'
ht-degree: 100%

---


# Présentation {#overview}

L’objectif du langage HTL (HTML Template Language), pris en charge par Adobe Experience Manager (AEM), est d’offrir un cadre web d’entreprise hautement productif qui renforce la sécurité et permet aux développeurs HTML sans connaissance de Java de participer de manière plus efficace aux projets AEM.

[Présenté dans AEM 6.0](history.md), le langage HTL (HTML Template Language) est le système de modèles côté serveur préféré et recommandé pour le HTML dans AEM. Pour les développeurs Web qui souhaitent créer des sites Web d’entreprise robustes, le langage HTL (HTML Template Language) permet d’améliorer la sécurité et l’efficacité du développement.

## Sécurité renforcée {#increased-security}

Par rapport aux autres systèmes de modèle, le langage HTL (HTML Template Language) renforce la sécurité des sites qui l’utilisent dans leur implémentation. HTL est en effet capable d’appliquer automatiquement l’échappement basé sur le contexte approprié à toutes les variables qui sont générées sur la couche présentation. HTL rend cela possible, car il comprend la syntaxe HTML et l’utilise pour ajuster l’échappement nécessaire aux expressions, en fonction de leur position dans les balises. Cela aura pour conséquence, par exemple, que des expressions placées dans les attributs `href` ou `src` seront placées dans une séquence d’échappement différemment des expressions placées dans d’autres attributs ou ailleurs.

Si le même résultat peut être obtenu avec des langages de modèle en JSP, le développeur doit s’assurer manuellement que l’échappement adéquat est appliqué à chaque variable. Comme une simple omission ou erreur sur un échappement appliqué suffit potentiellement à entraîner une vulnérabilité de type attaque multisite par scripts (XSS), nous avons décidé d’automatiser cette tâche avec HTL. Si nécessaire, les développeurs peuvent tout de même indiquer un échappement différent sur les expressions, mais avec HTL, le comportement par défaut est bien plus susceptible de corresponde au comportement souhaité, ce qui réduit la probabilité d’erreurs.

## Développement simplifié   {#simplified-development}

Il est facile d’apprendre le langage de modèle HTML. Ses fonctionnalités sont volontairement limitées pour assurer un apprentissage simple et direct. Il comporte également des mécanismes performants pour structurer les balises et appeler la logique, tout en appliquant toujours une séparation stricte des problèmes entre l’annotation et la logique. HTL lui-même est aux standards du HTML5, car il utilise des expressions et des attributs de données pour annoter les balises avec le comportement dynamique souhaité, ce qui signifie qu’il n’interrompt pas la disponibilité des balises, qui restent lisibles. Notez que l’évaluation des expressions et des attributs de données est effectuée entièrement du côté serveur et ne sera pas visible du côté client, où toute autre structure JavaScript souhaitée peut être utilisée sans interférence.

Ces fonctionnalités permettent aux développeurs HTML sans connaissance préalable de Java et avec peu de connaissances spécifiques du produit de modifier les modèles HTL. Ils peuvent ainsi faire partie de l’équipe de développement et cela simplifie la collaboration avec les développeurs Java de plein-pile. Réciproquement, cela permet aux développeurs Java de se concentrer sur le code principal sans se soucier du HTML.

## Réduction des coûts   {#reduced-costs}

Une sécurité renforcée, un développement simplifié et une meilleure collaboration des équipes se traduisent dans les projets AEM par des efforts réduits, une mise sur le marché plus rapide ainsi qu’un coût total de possession moins élevé.

Concrètement, d’après nos observations lors de la nouvelle mise en œuvre du site Adobe.com avec le langage HTL, le coût et la durée du projet peuvent être réduits d’environ 25 %.

![Augmentation de l’efficacité et diminution des coûts](assets/chlimage_1.png)

Le diagramme ci-dessus montre que HTL permet potentiellement d’améliorer l’efficacité des domaines suivants :

* **HTML/CSS/JS :** Comme les développeurs HTML peuvent modifier directement les modèles HTL, les conceptions frontales n’ont plus besoin d’être mises en œuvre séparément du projet AEM, mais peuvent être mises en œuvre directement sur les composants proprement dits d’AEM. Cela permet de réduire les itérations laborieuses pour les développeurs Java de plein-pile.
* **JSP/HTL :** Comme HTL ne nécessite en soi aucune connaissance de Java et est simple à écrire, tout développeur maîtrisant l’HTML peut modifier les modèles.
* **Java :** Grâce à l’API Use claire et simple d’utilisation proposée par HTL, l’interface de logique métier est clarifiée, ce qui profite également au développement Java dans son ensemble.

## Présentation vidéo {#video}

La vidéo suivante, tirée d’une [session d’AEM Gems](https://experienceleague.adobe.com/docs/experience-manager-gems-events/gems/gems2014/aem-introduction-to-htl.html?lang=fr), donne un aperçu de l’objectif du HTL ainsi que des exemples d’implémentation.

>[!VIDEO](https://video.tv.adobe.com/v/19504/?quality=9)

Veuillez noter que la vidéo fait référence à HTL par [son ancien nom, Sightly](history.md).

## Étapes suivantes {#next-steps}

Maintenant que vous connaissez les objectifs et les avantages de HTL, commencez à utiliser le langage en consultant le document [Prise en main du langage de modèle HTML](getting-started.md).
