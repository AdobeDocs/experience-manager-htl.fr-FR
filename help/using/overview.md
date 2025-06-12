---
title: Vue d’ensemble de HTL
description: Découvrez comment AEM prend en charge HTL (HTML Template Language) pour fournir un framework web productif au niveau de l’entreprise et renforcer ainsi la sécurité. Ce framework permet aux développeurs et développeuses HTML ne disposant pas de connaissance de Java de mieux prendre part aux projets AEM.
exl-id: 5d06ff25-d681-4b95-8375-c28a8364eb7e
source-git-commit: 9735ca6ae09ca899c69edd9b63d2e85ad5d6f904
workflow-type: tm+mt
source-wordcount: '677'
ht-degree: 95%

---


# Vue d’ensemble {#overview}

>[!TIP]
>
>**Avez-vous envisagé Edge Delivery Services pour AEM ?**
>
>Vous pouvez continuer à utiliser les méthodes décrites dans ce document pour les projets existants. Toutefois, pour les nouveaux projets, Adobe recommande d’utiliser [Edge Delivery Services.](https://experienceleague.adobe.com/fr/docs/experience-manager-cloud-service/content/edge-delivery/overview)

Le langage HTL, pris en charge par Adobe Experience Manager (AEM), vise à fournir un framework web extrêmement productif au niveau de l’entreprise et à renforcer ainsi la sécurité. Il permet aux développeurs et développeuses HTML ne disposant pas de connaissance de Java de mieux prendre part aux projets AEM.

[Introduit dans AEM 6.0](history.md), le langage HTL (HTML Template Language) est le système de modèles côté serveur préféré et recommandé pour HTML dans AEM. Pour les développeurs Web qui souhaitent créer des sites Web d’entreprise robustes, le langage HTL (HTML Template Language) permet d’améliorer la sécurité et l’efficacité du développement.

## Sécurité renforcée {#increased-security}

Le langage HTL (HTML Template Language) renforce la sécurité du site en appliquant automatiquement une séquence d’échappement adaptée au contexte à toutes les variables de sortie, ce qui le rend plus sécurisé que la plupart des autres systèmes de modèle. HTL rend cette approche possible, car il comprend la syntaxe HTML et l’utilise pour ajuster l’échappement nécessaire aux expressions, en fonction de leur position dans les balises. Cette méthode peut avoir pour conséquence que des expressions placées dans les attributs `href` ou `src` seront placées dans une séquence d’échappement différemment des expressions placées dans d’autres attributs ou ailleurs.

Bien que le même résultat puisse être obtenu avec des langages de modèle tels que JSP, le développement doit garantir que l’échappement manuel approprié est appliqué à chaque variable. Comme une seule omission ou erreur dans l’échappement appliqué est potentiellement suffisante pour provoquer une vulnérabilité de type XSS (cross-site scripting), Adobe a décidé d’automatiser cette tâche avec HTL. Si nécessaire, les développeurs et développeuses peuvent toujours spécifier une séquence d’échappement différente sur les expressions, mais avec HTL, le comportement par défaut est beaucoup plus susceptible de correspondre au comportement souhaité, ce qui réduit la probabilité d’erreurs.

## Développement simplifié   {#simplified-development}

Le langage du modèle HTML est facile à apprendre et ses fonctionnalités sont volontairement limitées pour s’assurer qu’il reste simple et direct. Il dispose également de mécanismes puissants pour structurer les balises et appeler la logique, tout en appliquant toujours une séparation stricte des préoccupations entre les balises et la logique. HTL est un HTML5 standard, qui utilise des expressions et des attributs de données pour annoter le balisage d’un comportement dynamique. Cette approche permet de conserver la validité et la lisibilité du balisage. L’évaluation des expressions et des attributs de données est effectuée entièrement côté serveur et ne sera pas visible côté client, où tout framework JavaScript souhaité peut être utilisé sans interférence.

Ces fonctionnalités permettent aux développeurs et développeuses HTML sans connaissance de Java de modifier les modèles HTL, de s’intégrer à l’équipe de développement et de rationaliser la collaboration avec les développeurs et développeuses Java full-stack. Cela permet également aux développeurs et développeuses Java de se concentrer sur le code back-end sans se soucier du HTML.

## Réduction des coûts {#reduced-costs}

Une sécurité renforcée, un développement simplifié et une meilleure collaboration des équipes se traduisent dans les projets AEM par des efforts réduits, une mise sur le marché plus rapide ainsi qu’un coût total de possession moins élevé.

La réimplémentation du site Adobe.com avec HTL a montré que les coûts et la durée du projet sont réduits jusqu’à environ 25 %.

![Augmentation de l’efficacité et diminution des coûts](assets/chlimage_1.png)

Le diagramme ci-dessus montre que HTL permet potentiellement d’améliorer l’efficacité des domaines suivants :

* **HTML/CSS/JS :** les développeurs d’HTML peuvent directement modifier les modèles HTL, pour une mise en œuvre directe des conceptions frontales sur les composants AEM, rendant ainsi inutile toute mise en œuvre distincte. Cette approche réduit les itérations pénibles avec les développeurs et développeuses Java full-stack.
* **JSP/HTL :** puisqu’HTL ne nécessite aucune connaissance de Java et est simple à écrire, tout développeur et toute développeuse ayant une expertise HTML peut modifier les modèles.
* **Java :** grâce à l’API claire et simple d’utilisation fournie par HTL, l’interface avec la logique commerciale est clarifiée, ce qui bénéficie également au développement Java dans son ensemble.

## Présentation vidéo {#video}

La vidéo suivante, tirée d’une [session d’AEM Gems](https://experienceleague.adobe.com/fr/docs/events/experience-manager-gems-recordings/gems2014/aem-introduction-to-htl), donne un aperçu de l’objectif du HTL ainsi que des exemples d’implémentation.

>[!VIDEO](https://video.tv.adobe.com/v/19504/?quality=9)

Veuillez noter que la vidéo fait référence à HTL par [son ancien nom, Sightly](history.md).

## Étapes suivantes {#next-steps}

Maintenant que vous connaissez les objectifs et les avantages de HTL, vous pouvez commencer à utiliser ce langage. Voir [Prise en main du langage HTL (HTML Template Language)](getting-started.md).
