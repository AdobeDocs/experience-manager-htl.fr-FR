---
title: Historique de HTL
description: Pour les utilisateurs de longue date d’AEM, ce document fournit l’arrière-plan de HTL, comment il remplace JSP et le changement de nom de Sightly.
exl-id: 00985b35-2130-4946-959a-0a09a34a0f05
index: false
source-git-commit: 3a416e337337d31f7a7fdb2c78efac46ecf64096
workflow-type: tm+mt
source-wordcount: '530'
ht-degree: 100%

---


# Historique de HTL {#history-of-htl}

Pour les utilisateurs de longue date d’AEM, ce document fournit l’arrière-plan de HTL, comment il remplace JSP et le changement de nom de Sightly.

## Anciennement appelé Sightly {#sightly}

Le langage HTL (HTML Template Language) est le système de modèles côté serveur préféré et recommandé pour le HTML dans Adobe Experience Manager. Il remplace JSP (JavaServer Pages) qui était utilisé dans les versions précédentes d’AEM.

## HTL par rapport à JSP {#htl-over-jsp}

Adobe recommande d’utiliser le langage HTL pour les nouveaux projets AEM. La raison en est qu’il offre plusieurs avantages par rapport à JSP. Toutefois, pour les projets existants, une migration n’a de sens que si vous estimez qu’elle nécessite moins d’effort que la maintenance du JSP existant pour les années à venir.

Notons toutefois que le passage au langage HTL n’est pas nécessairement un choix de type « tout ou rien », car les composants écrits en HTL sont compatibles avec les composants écrits en JSP ou ESP. Cette approche signifie que les projets existants peuvent utiliser HTL pour de nouveaux composants sans problème, tout en conservant JSP pour les composants existants.

Même au sein d’un même composant, des fichiers HTL peuvent être utilisés en même temps que des fichiers JSP et ESP. L’exemple suivant montre sur la **ligne 1** comment inclure un fichier HTL à partir d’un fichier JSP et sur la **ligne 2** comment inclure un fichier JSP à partir d’un fichier HTL :

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

## Questions fréquentes {#frequently-asked-questions}

Les développeurs et développeuses AEM expérimentés qui découvrent HTL posent généralement les questions suivantes :

### HTL est-il plus limité que JSP ? {#limitations}

HTL n’est pas vraiment plus limité que JSP, dans la mesure où ce qui peut être réalisé avec JSP est également réalisable avec HTL. Cependant, la conception de HTL est plus stricte que celle de JSP sous plusieurs aspects. Ce qui peut être réalisé à partir d’un seul fichier JSP peut devoir être divisé en une classe Java ou un fichier JavaScript pour être réalisable en HTL. Mais cette approche est généralement souhaitable pour garantir une bonne séparation des problèmes entre la logique et le balisage.

### HTL prend-il en charge les bibliothèques de balises JSP ? {#tag-libraries}

Non. Toutefois, comme l’indique la section [Charger des bibliothèques clientes](getting-started.md#loading-client-libraries) du document de prise en main, les instructions de modèles et d’appels sont semblables.

### Les fonctionnalités HTL peuvent-elles être élargies pour un projet AEM ? {#extended}

Non. Le HTL dispose de puissants mécanismes d’extension pour la réutilisation de la logique (le [Use-API](#use-api-for-accessing-logic)) et du balisage (les instructions de modèles et d’appels), pouvant être utilisés pour modulariser le code de projets.

### Quels sont les principaux avantages de HTL par rapport à JSP ? {#benefits}

La sécurité et l’efficacité des projets, principaux avantages, sont détaillés dans la [Vue d’ensemble](overview.md).

### Les pages JavaServer (JSP) disparaissent-elles ? {#go-away}

Non. Il n’est pas prévu de mettre fin aux pages JSP.

## Que contient un nom ? {#what-is-in-a-name}

Dans les versions 6.0 et 6.1 d’AEM, HTL était nommé **Sightly**. Adobe l’a rebaptisé **« HTML Template Language »** ou **HTL** afin de clarifier l’objet de la spécification et de s’aligner sur les directives générales d’Adobe en matière de dénomination. Ce changement de nom est entré en vigueur en août 2016 et s’applique à AEM version 6.0 et versions ultérieures.

>[!NOTE]
>
>Ce changement de nom n’impacte pas le code ou l’API ; la compatibilité n’est donc pas affectée.

Pour en savoir plus sur HTL, consultez notre [Guide de prise en main du langage HTL (HTML Template Language)](overview.md).
