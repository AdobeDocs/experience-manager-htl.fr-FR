---
title: Présentation de HTL
description: Découvrez comment AEM prend en charge le langage HTL (HTML Template Language) pour fournir un framework web productif au niveau de l’entreprise, ce qui renforce la sécurité et permet aux développeurs de HTML qui n’ont aucune connaissance sur Java de mieux participer aux projets AEM.
exl-id: 5d06ff25-d681-4b95-8375-c28a8364eb7e
source-git-commit: 88edbd2fd66de960460df5928a3b42846d32066b
workflow-type: ht
source-wordcount: '711'
ht-degree: 100%

---


# Présentation {#overview}

L’objectif du langage HTL (HTML Template Language), pris en charge par Adobe Experience Manager (AEM), est d’offrir un cadre web d’entreprise hautement productif qui renforce la sécurité et permet aux développeurs HTML sans connaissance de Java de participer de manière plus efficace aux projets AEM.

[Présenté dans AEM 6.0](history.md), le langage HTL (HTML Template Language) est le système de modèles côté serveur préféré et recommandé pour le HTML dans AEM. Pour les développeurs Web qui souhaitent créer des sites Web d’entreprise robustes, le langage HTL (HTML Template Language) permet d’améliorer la sécurité et l’efficacité du développement.

## Sécurité renforcée {#increased-security}

Par rapport aux autres systèmes de modèle, le langage HTL (HTML Template Language) renforce la sécurité des sites qui l’utilisent dans leur implémentation. HTL est en effet capable d’appliquer automatiquement l’échappement basé sur le contexte approprié à toutes les variables qui sont générées sur la couche présentation. HTL rend cela possible, car il comprend la syntaxe HTML et l’utilise pour ajuster l’échappement nécessaire aux expressions, en fonction de leur position dans les balises. Cela aura pour conséquence, par exemple, que des expressions placées dans les attributs `href` ou `src` seront placées dans une séquence d’échappement différemment des expressions placées dans d’autres attributs ou ailleurs.

Bien que le même résultat puisse être obtenu avec des langages de modèle tels que JSP, le développement doit garantir que l’échappement manuel approprié est appliqué à chaque variable. Comme une seule omission ou erreur sur l’échappement appliqué est potentiellement suffisante pour provoquer une vulnérabilité de type XSS (cross-site scripting), nous avons décidé d’automatiser cette tâche avec HTL. Si nécessaire, les développeurs et développeuses peuvent toujours spécifier une séquence d’échappement différente sur les expressions, mais avec HTL, le comportement par défaut est beaucoup plus susceptible de correspondre au comportement souhaité, ce qui réduit la probabilité d’erreurs.

## Développement simplifié   {#simplified-development}

Le langage du modèle HTML est facile à apprendre et ses fonctionnalités sont volontairement limitées pour s’assurer qu’il reste simple et direct. Il dispose également de mécanismes puissants pour structurer les balises et appeler la logique, tout en appliquant toujours une séparation stricte des préoccupations entre les balises et la logique. HTL est un HTML5 standard, car il utilise des expressions et des attributs de données pour annoter les balises avec le comportement dynamique souhaité, ce qui signifie qu’il n’enfreint pas la validité des balises et les rend lisibles. Notez que l’évaluation des expressions et des attributs de données est effectuée entièrement côté serveur et ne sera pas visible côté client, où tout framework JavaScript souhaité peut être utilisé sans interférence.

Ces fonctionnalités permettent aux développeurs et développeuses HTML qui ne connaissent pas Java et qui n’ont pas de connaissance approfondie d’un produit de pouvoir modifier des modèles HTL, ce qui leur permet de faire partie de l’équipe de développement et d’optimiser leur collaboration avec les développeurs et développeuses Java full-stack. Cela permet également aux développeurs et développeuses Java de se concentrer sur le code back-end sans se soucier du HTML.

## Réduction des coûts   {#reduced-costs}

Une sécurité renforcée, un développement simplifié et une meilleure collaboration des équipes se traduisent dans les projets AEM par des efforts réduits, une mise sur le marché plus rapide ainsi qu’un coût total de possession moins élevé.

Concrètement, d’après nos observations lors de la nouvelle mise en œuvre du site Adobe.com avec le langage HTL, le coût et la durée du projet peuvent être réduits d’environ 25 %.

![Augmentation de l’efficacité et diminution des coûts](assets/chlimage_1.png)

Le diagramme ci-dessus montre que HTL permet potentiellement d’améliorer l’efficacité des domaines suivants :

* **HTML/CSS/JS :** les développeurs et développeuses HTML étant en mesure de modifier directement des modèles HTL, les conceptions front-end ne doivent plus être mises en œuvre séparément du projet AEM, mais peuvent être mises en œuvre directement sur les composants AEM réels. Cela réduit les itérations pénibles avec les développeurs et développeuses Java full-stack.
* **JSP/HTL :** comme HTL ne nécessite aucune connaissance de Java et est simple à écrire, tout développeur et développeuse ayant une expertise HTML peut modifier les modèles.
* **Java :** grâce à l’API claire et simple d’utilisation fournie par HTL, l’interface avec la logique commerciale est clarifiée, ce qui bénéficie également au développement Java dans son ensemble.

## Présentation vidéo {#video}

La vidéo suivante, tirée d’une [session d’AEM Gems](https://experienceleague.adobe.com/docs/experience-manager-gems-events/gems/gems2014/aem-introduction-to-htl.html?lang=fr), donne un aperçu de l’objectif du HTL ainsi que des exemples d’implémentation.

>[!VIDEO](https://video.tv.adobe.com/v/19504/?quality=9)

Veuillez noter que la vidéo fait référence à HTL par [son ancien nom, Sightly](history.md).

## Étapes suivantes {#next-steps}

Maintenant que vous connaissez les objectifs et les avantages de HTL, commencez à utiliser le langage en consultant le document [Prise en main du langage de modèle HTML](getting-started.md).
