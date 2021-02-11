---
title: Use-API HTL
description: '''Deux API sont disponibles pour Adobe HTL : Use-API Java et Use-API Javascript'''
translation-type: tm+mt
source-git-commit: f7e46aaac2a4b51d7fa131ef46692ba6be58d878
workflow-type: tm+mt
source-wordcount: '183'
ht-degree: 55%

---


# Use-API HTL {#htl-use-api}

HTL favorise la séparation des préoccupations en ne permettant pas à la logique commerciale de se mélanger avec les balises. La logique métier peut être implémentée par le biais de l&#39;API d&#39;utilisation.

Le tableau suivant présente un aperçu des avantages et des inconvénients de chaque API.

|  | **Utilisation d’une API Java**  | **Utilisation d’une API JavaScript**  |
|--- |--- |--- |
| **Avantages** | <ul><li>Plus rapide</li><li>Peut être inspecté avec un débogueur</li><li>Test facile à unitaire</li></ul> | <ul><li>Peut être modifié par les développeurs frontaux</li><li>Se trouve dans le composant, en conservant la logique de vue d’un composant à proximité de son modèle correspondant.</li></ul> |
| **Inconvénients** | <ul><li>Impossible d&#39;être modifié par les développeurs frontaux</li></ul> | <ul><li>Plus lent</li><li>Aucun débogueur (pour le moment)</li><li>Plus difficile à tester en unité</li></ul> |

Pour les composants de page, il est conseillé d’utiliser un modèle mixte, avec l’ensemble de la logique du modèle située dans Java. Cela permet d’offrir des API transparentes qui ne sont pas impactées par tout ce qui se passe dans la vue (c’est-à-dire dans les composants). AEM est livré avec de grands modèles par défaut, comme l’API Page ou Ressource, qui devraient être en mesure de couvrir la plupart des cas.

L’ensemble de la logique de vue qui est spécifique à un composant doit être placée dans ce composant en tant que JavaScript, car elle appartient à ce composant.
