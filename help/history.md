---
title: Historique de HTL
description: Pour les utilisateurs de longue date d’AEM, ce document fournit l’arrière-plan de HTL, comment il remplace JSP et le changement de nom de Sightly.
exl-id: 00985b35-2130-4946-959a-0a09a34a0f05
source-git-commit: 5ab1275c984135fe946f36905bbc979cf19edd80
workflow-type: ht
source-wordcount: '542'
ht-degree: 100%

---


# Historique de HTL {#history-of-htl}

Pour les utilisateurs de longue date d’AEM, ce document fournit l’arrière-plan de HTL, comment il remplace JSP et le changement de nom de Sightly.

## Anciennement appelé Sightly {#sightly}

Le langage HTL (HTML Template Language) est le système de modèles côté serveur préféré et recommandé pour le HTML dans Adobe Experience Manager. Il remplace JSP (JavaServer Pages) qui était utilisé dans les versions précédentes d’AEM.

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

HTL n’est pas vraiment plus limité que JSP, dans la mesure où ce qui peut être réalisé avec JSP est également réalisable avec HTL. Cependant, la conception de HTL est plus stricte que celle de JSP sous plusieurs aspects. Ce qui peut être réalisé à partir d’un seul fichier JSP peut devoir être divisé en une classe Java ou un fichier JavaScript pour être réalisable en HTL. Mais cela est généralement souhaitable pour garantir une bonne séparation des problèmes entre la logique et le balisage.

### HTL prend-il en charge les bibliothèques de balises JSP ? {#tag-libraries}

Non, mais comme l’indique la section [Téléchargement des bibliothèques clientes](getting-started.md#loading-client-libraries) du document de prise en main, les instructions de modèles et d’appels sont semblables.

### Les fonctionnalités HTL peuvent-elles être élargies pour un projet AEM ? {#extended}

Non, cela n’est pas possible. Le HTL dispose de puissants mécanismes d’extension pour la réutilisation de la logique (le [Use-API](#use-api-for-accessing-logic)) et du balisage (les instructions de modèles et d’appels), pouvant être utilisés pour modulariser le code de projets.

### Quels sont les principaux avantages de HTL par rapport à JSP ? {#benefits}

La sécurité et l’efficacité des projets sont les principaux avantages et sont détaillés dans la [Présentation.](overview.md)

### JSP finira-t-il par disparaître ? {#go-away}

Rien de tel n’est prévu.

## Que contient un nom ? {#what-is-in-a-name}

Dans les versions 6.0 et 6.1 d’AEM, HTL était connu sous le nom de **Sightly**. Adobe l’a rebaptisé **« HTML Template Language »** ou **HTL** afin de clarifier l’objet de la spécification et de s’aligner sur les directives générales d’Adobe en matière de dénomination. Ce changement de nom est entré en vigueur en août 2016 et s’applique à AEM version 6.0 et ultérieure.

>[!NOTE]
>
>Ce changement de nom n’impacte pas le code ou l’API ; la compatibilité n’est donc pas affectée. Pour plus d’informations, veuillez vous [référer à cette vidéo d’annonce.](https://helpx.adobe.com/fr/experience-manager/how-to/announce-htl.html)

Pour en savoir plus sur HTL et faire vos premiers pas, consultez notre [Guide officiel de prise en main du langage HTL.](overview.md)
