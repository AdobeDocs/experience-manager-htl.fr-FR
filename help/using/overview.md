---
title: Présentation de HTL
description: Découvrez comment AEM prend en charge HTL (HTML Template Language) pour fournir un framework web productif au niveau de l’entreprise qui renforce la sécurité. Ce framework permet d’HTML les développeurs sans connaissance de Java à mieux participer à AEM Projets.
exl-id: 5d06ff25-d681-4b95-8375-c28a8364eb7e
source-git-commit: c6bb6f0954ada866cec574d480b6ea5ac0b51a3f
workflow-type: tm+mt
source-wordcount: '645'
ht-degree: 26%

---


# Vue d’ensemble {#overview}

Le langage de modèle d’HTML (HTL), pris en charge par Adobe Experience Manager (AEM), vise à fournir une structure web extrêmement productive au niveau de l’entreprise qui renforce la sécurité. Il permet également aux développeurs d’HTMLS sans connaissance de Java de participer de manière plus efficace à AEM Projets.

[Introduit dans AEM 6.0](history.md), le langage de modèle d’HTML est le système de modèle côté serveur préféré et recommandé pour l’HTML dans AEM. Pour les développeurs Web qui souhaitent créer des sites Web d’entreprise robustes, le langage HTL (HTML Template Language) permet d’améliorer la sécurité et l’efficacité du développement.

## Sécurité renforcée {#increased-security}

Le langage HTL (HTML Template Language) renforce la sécurité du site en appliquant automatiquement une séquence d’échappement adaptée au contexte à toutes les variables de sortie, ce qui le rend plus sécurisé que la plupart des autres systèmes de modèle. HTL rend cette approche possible, car il comprend la syntaxe de l’HTML et utilise ces connaissances pour ajuster l’échappement requis pour les expressions, en fonction de leur position dans le balisage. Cette méthode peut entraîner une séquence d’échappement différente des expressions placées dans les attributs `href` ou `src` par rapport aux expressions placées dans d’autres attributs ou ailleurs.

Bien que le même résultat puisse être obtenu avec des langages de modèle tels que JSP, le développement doit garantir que l’échappement manuel approprié est appliqué à chaque variable. Comme une seule omission ou erreur dans l’échappement appliqué est potentiellement suffisante pour provoquer une vulnérabilité de type attaque multisite par scripts (XSS), Adobe a décidé d’automatiser cette tâche avec HTL. Si nécessaire, les développeurs et développeuses peuvent toujours spécifier une séquence d’échappement différente sur les expressions, mais avec HTL, le comportement par défaut est beaucoup plus susceptible de correspondre au comportement souhaité, ce qui réduit la probabilité d’erreurs.

## Développement simplifié   {#simplified-development}

Le langage du modèle HTML est facile à apprendre et ses fonctionnalités sont volontairement limitées pour s’assurer qu’il reste simple et direct. Il dispose également de mécanismes puissants pour structurer les balises et appeler la logique, tout en appliquant toujours une séparation stricte des préoccupations entre les balises et la logique. HTL est un HTML5 standard, qui utilise des expressions et des attributs de données pour annoter le balisage avec le comportement dynamique. Cette approche permet de conserver la validité et la lisibilité des balises. L’évaluation des expressions et des attributs de données est effectuée entièrement côté serveur et n’est pas visible côté client, où toute structure JavaScript souhaitée peut être utilisée sans interférence.

Ces fonctionnalités permettent aux développeurs d’HTML sans connaissance de Java de modifier les modèles HTL, de s’intégrer à l’équipe de développement et de rationaliser la collaboration avec les développeurs Java en pile. Et vice versa, il permet aux développeurs Java de se concentrer sur le code principal sans se soucier de l’HTML.

## Réduction des coûts   {#reduced-costs}

Une sécurité renforcée, un développement simplifié et une meilleure collaboration des équipes se traduisent pour les projets AEM par des efforts réduits, un délai de mise sur le marché plus rapide et un coût total de propriété plus faible.

La réimplémentation du site Adobe.com avec le langage de modèle d’HTML a montré que les coûts et la durée du projet sont réduits jusqu’à environ 25 %.

![Augmentation de l’efficacité et diminution des coûts](assets/chlimage_1.png)

Le diagramme ci-dessus montre les améliorations d’efficacité suivantes rendues possibles par HTL :

* **HTML / CSS / JS :** Les développeurs d’HTMLS peuvent directement modifier les modèles HTL, ce qui permet de mettre en oeuvre directement les conceptions frontales sur les composants d’AEM, rendant ainsi inutile toute mise en oeuvre distincte. Cette approche réduit les itérations pénibles avec les développeurs Java en pile complète.
* **JSP / HTL :** Comme HTL lui-même ne nécessite aucune connaissance de Java et est simple à écrire, tout développeur ayant une expertise en HTML est autorisé à modifier les modèles.
* **Java :** grâce à l’API claire et simple d’utilisation fournie par HTL, l’interface avec la logique commerciale est clarifiée, ce qui bénéficie également au développement Java dans son ensemble.

## Présentation vidéo {#video}

La vidéo suivante d’une [session AEM Gems](https://experienceleague.adobe.com/en/docs/events/experience-manager-gems-recordings/gems2014/aem-introduction-to-htl), donne un aperçu de l’objectif de HTL ainsi que des exemples de mise en oeuvre.

>[!VIDEO](https://video.tv.adobe.com/v/19504/?quality=9)

Notez que la vidéo fait référence à HTL par [son ancien nom, Sightly](history.md).

## Étapes suivantes {#next-steps}

Maintenant que vous connaissez les objectifs et les avantages de HTL, vous pouvez commencer avec la langue. Voir [Prise en main du langage de modèle d’HTML](getting-started.md).
