---
title: HTL Use-API
description: 'Deux API sont disponibles pour HTL : Java Use-API et Javascript Use-API.'
exl-id: 8f95707e-952c-4efe-a44e-9a1ad501605e
source-git-commit: 8e70ee4921a7ea071ab7e06947824c371f4013d8
workflow-type: ht
source-wordcount: '183'
ht-degree: 100%

---

# HTL Use-API {#htl-use-api}

HTL encourage la séparation des préoccupations en ne permettant pas à la logique commerciale de se mélanger au balisage. La logique commerciale peut être implémentée par l’intermédiaire de l’interface Use-API.

Le tableau suivant donne un aperçu des avantages et des inconvénients de chaque API.

|  | **Utilisation d’une API Java**  | **Utilisation d’une API JavaScript**  |
|--- |--- |--- |
| **Avantages** | <ul><li>Plus rapide</li><li>Peut être inspecté avec un débogueur</li><li>Facilité des tests unitaires</li></ul> | <ul><li>Peut être modifié par les développeurs front-end</li><li>Se trouve à l’intérieur du composant, ce qui permet de garder la logique d’affichage d’un composant à proximité de son modèle correspondant</li></ul> |
| **Inconvénients** | <ul><li>Ne peut pas être modifié par les développeurs front-end</li></ul> | <ul><li>Plus lent</li><li>Pas de débogueur (pour l’instant)</li><li>Plus difficile de faire des tests unitaires</li></ul> |

Pour les composants de page, il est conseillé d’utiliser un modèle mixte, avec l’ensemble de la logique du modèle située dans Java. Cela permet d’offrir des API transparentes qui ne sont pas impactées par tout ce qui se passe dans la vue (c’est-à-dire dans les composants). AEM est livré avec de grands modèles par défaut, comme l’API Page ou Ressource, qui devraient être en mesure de couvrir la plupart des cas.

L’ensemble de la logique de vue qui est spécifique à un composant doit être placée dans ce composant en tant que JavaScript, car elle appartient à ce composant.
