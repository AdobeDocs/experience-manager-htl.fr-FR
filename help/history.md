---
title: Historique de HTL
description: Pendant longtemps, les utilisateurs d’AEM, ce document fournit l’arrière-plan de HTL, la façon dont il remplace JSP et le changement de nom de Sightly.
exl-id: 00985b35-2130-4946-959a-0a09a34a0f05
source-git-commit: 5ab1275c984135fe946f36905bbc979cf19edd80
workflow-type: tm+mt
source-wordcount: '542'
ht-degree: 57%

---


# Historique de HTL {#history-of-htl}

Pendant longtemps, les utilisateurs d’AEM, ce document fournit l’arrière-plan de HTL, la façon dont il remplace JSP et le changement de nom de Sightly.

## Anciennement appelé Sightly {#sightly}

Le langage HTL (HTML Template Language) est le système de modèle côté serveur préféré et recommandé pour le HTML dans Adobe Experience Manager. Il remplace JSP (JavaServer Pages) qui était utilisé dans les versions précédentes d’AEM.

## HTL par rapport à JSP {#htl-over-jsp}

Pour les nouveaux projets AEM, il est recommandé d’utiliser le langage HTL, car il permet de bénéficier de plusieurs avantages par rapport à JSP. Toutefois, pour les projets existants, une migration n’a de sens que si vous estimez qu’elle nécessite moins d’effort que la maintenance du JSP existant pour les années à venir.

Mais le passage au HTL n’est pas nécessairement un choix de type « tout ou rien », parce que les composants écrits en HTL sont compatibles avec les composants écrits en JSP ou ESP. Cela signifie que les projets existants peuvent utiliser HTL sans problème pour les nouveaux composants, tout en conservant JSP pour les composants existants.

Même au sein d’un même composant, des fichiers HTL peuvent être utilisés en même temps que des fichiers JSP et ESP. L’exemple suivant montre sur **la ligne 1** comment inclure un fichier HTL à partir d’un fichier JSP et sur **la ligne 2**, comment un fichier JSP peut être inclus à partir d’un fichier HTL :

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

## Foire aux questions {#frequently-asked-questions}

Voici quelques-unes des questions fréquemment posées par les développeurs AEM expérimentés qui découvrent HTL.

### HTL est-il plus limité que JSP ? {#limitations}

HTL n’est pas vraiment plus limité que JSP, dans la mesure où ce qui peut être réalisé avec JSP est également réalisable avec HTL. Cependant, HTL est par conception plus stricte que JSP sur plusieurs aspects. Ce qui peut être réalisé dans un seul fichier JSP peut devoir être divisé en une classe Java ou un fichier JavaScript pour être réalisable dans HTL. Mais cela est généralement souhaitable pour garantir une bonne séparation des problèmes entre la logique et l’annotation.

### HTL prend-il en charge les bibliothèques de balises JSP ? {#tag-libraries}

Non, mais comme illustré dans la [Chargement des bibliothèques clientes](getting-started.md#loading-client-libraries) dans le document Prise en main , les instructions template &amp; call offrent un modèle similaire.

### Les fonctionnalités HTL peuvent-elles être élargies pour un projet AEM ? {#extended}

Non, cela n’est pas possible. HTL dispose de mécanismes d’extension puissants pour la réutilisation de la logique (le [Use-API](#use-api-for-accessing-logic)) et du balisage (les instructions template &amp; call), qui peuvent être utilisés pour modulariser le code des projets.

### Quels sont les principaux avantages de HTL par rapport à JSP ? {#benefits}

La sécurité et l’efficacité des projets sont les principaux avantages et sont détaillés dans la [Présentation.](overview.md)

### JSP finira-t-il par disparaître ? {#go-away}

Il n&#39;y a pas de plan dans ce sens.

## Qu&#39;y a-t-il dans un nom ? {#what-is-in-a-name}

Dans AEM 6.0 et 6.1, HTL était appelé **Sightly**. Adobe renommé **Langage de modèle de HTML** ou **HTL** pour clarifier la spécification et s’aligner sur les directives d’attribution de noms d’Adobe en général. Ce changement de nom est entré en vigueur en août 2016 et s’applique à AEM version 6.0 et ultérieure.

>[!NOTE]
>
>Ce changement de nom n’impacte pas le code ou l’API ; la compatibilité n’est donc pas affectée. Pour plus d’informations, veuillez [reportez-vous à cette vidéo d’annonce.](https://helpx.adobe.com/fr/experience-manager/how-to/announce-htl.html)

Pour en savoir plus sur HTL et faire vos premiers pas, consultez notre [Guide de prise en main du langage HTL.](overview.md)
