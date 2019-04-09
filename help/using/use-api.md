---
title: Use-API HTL
seo-title: Use-API Adobe HTL
description: 'Deux API sont disponibles pour HTL : Use-API Java et Use-API JavaScript.'
seo-description: 'Deux API sont disponibles pour Adobe HTL : Use-API Java et Use-API JavaScript.'
uuid: ab44aa5c-ce7e-40b9-97fb-e86c6a28405c 
contentOwner: Utilisateur
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: référence
discoiquuid: 89004426-eb59-4b63-913f-51bf98662773
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 796c55d3d85e6b5a3efaa5c04a25be1b0b4e54dd

---


# Use-API HTL {#htl-use-api}

Le tableau suivant présente un aperçu des avantages et des inconvénients de chaque API.

|  | **Utilisation d’une API Java**  | **Utilisation d’une API JavaScript**  |
|--- |--- |--- |
| **Avantages** | <ul><li>plus rapide</li><li>peut être inspecté à l’aide d’un débogueur</li><li>facile à tester à l’unité</li></ul> | <ul><li>peut être modifié par les développeurs frontaux</li><li>est situé dans le composant, gardant la logique de vue d’un composant près de son modèle correspondant</li></ul> |
| **Inconvénients** | <ul><li>ne peut pas être modifié par les développeurs frontaux</li></ul> | <ul><li>plus lent</li><li>aucun débogueur (pour l’instant)</li><li>plus difficile à tester à l’unité</li></ul> |


Pour les composants de page, il est conseillé d’utiliser un modèle mixte, avec l’ensemble de la logique du modèle située dans Java. Cela permet d’offrir des API transparentes qui ne sont pas impactées par tout ce qui se passe dans la vue (c’est-à-dire dans les composants). AEM est livré avec de grands modèles par défaut, comme l’API Page ou Ressource, qui devraient être en mesure de couvrir la plupart des cas.

L’ensemble de la logique de vue qui est spécifique à un composant doit être placée dans ce composant en tant que JavaScript, car elle appartient à ce composant.
