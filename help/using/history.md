---
title: Historique de HTL
description: Pour les utilisateurs de longue date d’AEM, ce document fournit l’arrière-plan de HTL, comment il remplace JSP et le changement de nom de Sightly.
exl-id: 00985b35-2130-4946-959a-0a09a34a0f05
source-git-commit: c6bb6f0954ada866cec574d480b6ea5ac0b51a3f
workflow-type: tm+mt
source-wordcount: '540'
ht-degree: 41%

---


# Historique de HTL {#history-of-htl}

Pour les utilisateurs de longue date d’AEM, ce document fournit l’arrière-plan de HTL, comment il remplace JSP et le changement de nom de Sightly.

## Anciennement appelé Sightly {#sightly}

Le langage HTL (HTML Template Language) est le système de modèles côté serveur préféré et recommandé pour le HTML dans Adobe Experience Manager. Il remplace JSP (JavaServer Pages) qui était utilisé dans les versions précédentes d’AEM.

## HTL par rapport à JSP {#htl-over-jsp}

Adobe recommande d’utiliser le langage de modèle d’HTML pour les nouveaux projets AEM. La raison en est qu’il offre plusieurs avantages par rapport à JSP. Toutefois, pour les projets existants, une migration n’a de sens que si vous estimez qu’elle nécessite moins d’effort que la maintenance du JSP existant pour les années à venir.

Le passage à HTL n’est pas nécessairement un choix de type &quot;tout ou rien&quot;, car les composants écrits en HTL sont compatibles avec les composants écrits en JSP ou ESP. Cette approche signifie que les projets existants peuvent utiliser HTL pour de nouveaux composants sans problème, tout en conservant JSP pour les composants existants.

Même au sein d’un même composant, des fichiers HTL peuvent être utilisés en même temps que des fichiers JSP et ESP. L’exemple suivant montre sur la **ligne 1** comment inclure un fichier HTL à partir d’un fichier JSP et sur la **ligne 2** comment un fichier JSP peut être inclus à partir d’un fichier HTL :

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

## Questions fréquentes {#frequently-asked-questions}

Les développeurs AEM expérimentés qui découvrent HTL posent généralement les questions suivantes :

### HTL est-il plus limité que JSP ? {#limitations}

HTL n’a pas de limites par rapport à JSP dans la mesure où ce qui peut être réalisé avec JSP doit également être faisable avec HTL. Cependant, HTL est par conception plus stricte que JSP sur plusieurs aspects. Pour qu’il soit possible d’obtenir ce qui peut être obtenu dans un seul fichier JSP, il faut peut-être le séparer dans une classe Java ou un fichier JavaScript pour pouvoir l’obtenir dans HTL. Mais cette approche est généralement souhaitée pour garantir une bonne séparation des préoccupations entre la logique et l’annotation.

### HTL prend-il en charge les bibliothèques de balises JSP ? {#tag-libraries}

Nombre Cependant, comme illustré dans la section [Chargement des bibliothèques clientes](getting-started.md#loading-client-libraries) du document Prise en main, les instructions template &amp; call offrent un modèle similaire.

### Les fonctionnalités HTL peuvent-elles être élargies pour un projet AEM ? {#extended}

Nombre Le HTL dispose de puissants mécanismes d’extension pour la réutilisation de la logique (le [Use-API](#use-api-for-accessing-logic)) et du balisage (les instructions de modèles et d’appels), pouvant être utilisés pour modulariser le code de projets.

### Quels sont les principaux avantages de HTL par rapport à JSP ? {#benefits}

La sécurité et l’efficacité des projets sont les principaux avantages, détaillés dans la [Présentation](overview.md).

### Les pages JavaServer (JSP) disparaissent-elles ? {#go-away}

Nombre Il n’est pas prévu d’interrompre JSP.

## Que contient un nom ? {#what-is-in-a-name}

Dans AEM 6.0 et 6.1, HTL était appelé **Sightly**. Adobe l’a renommé **HTML Template Language** ou **HTL** pour clarifier la spécification et s’aligner sur les directives d’attribution de noms d’Adobe en général. Ce changement de nom est entré en vigueur en août 2016 et s’applique à AEM version 6.0 et ultérieure.

>[!NOTE]
>
>Ce changement de nom n’a aucune incidence sur le code ou l’API. Par conséquent, la compatibilité n’est pas affectée. Pour plus d&#39;informations, [ reportez-vous à cette vidéo d&#39;annonce ](https://helpx.adobe.com/fr/experience-manager/how-to/announce-htl.html).

Pour en savoir plus sur HTL, consultez le [Guide de prise en main du langage de modèle d’HTML (HTL)](overview.md).
