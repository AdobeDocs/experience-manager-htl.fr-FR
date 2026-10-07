---
source-git-commit: addc69e4b4e56a9b1c5f91ce9af26fa2d326d981
workflow-type: tm+mt
source-wordcount: '740'
ht-degree: 3%
---
# Recommandations pour la contribution à la documentation de Adobe Experience Manager

## Philosophie de la documentation

Les utilisateurs de Adobe Experience Manager travaillent dans des environnements hautement compétitifs et s’efforcent de créer des expériences digitales qui les distinguent de leurs concurrents. Par conséquent, lorsque Adobe fournit des outils avancés dans AEM, ceux-ci sont complétés par une documentation précise et claire. Il permet aux clients d’utiliser immédiatement leur investissement dans AEM et d’optimiser leur retour sur investissement.

L’objectif de la documentation d’AEM est de la mettre entre les mains des utilisateurs d’AEM dès que possible. Par conséquent, Adobe donne la priorité à une documentation précise et utilisable et s’efforce de la mettre à jour et de l’améliorer en permanence.

## Contributions à la documentation

Dans le but d’améliorer continuellement la documentation d’AEM, l’ensemble de la communauté des utilisateurs d’AEM est invitée à contribuer à la documentation. Que ce soit par le biais de demandes ou de problèmes de tirage, les améliorations apportées à la documentation peuvent être des corrections, des clarifications, des extensions et d’autres exemples.

## Normes de documentation

Bien qu’Adobe accueille favorablement les contributions à sa documentation, toute contribution à la documentation d’AEM dans une requête d’extraction ou un problème doit être conforme aux normes de contribution et de documentation d’Adobe.

Les contributions qui ne répondent pas à ces normes peuvent être refusées.

### Les cas d’utilisation standard sont documentés dans Adobe.

La documentation d’AEM couvre les cas d’utilisation standard. Les cas d’utilisation ne relevant pas de l’installation et de l’utilisation standard du produit ne font pas partie de la documentation d’AEM.

### Adobe ne documente généralement pas les bogues ou leurs solutions.

La documentation d’AEM couvre les cas d’utilisation standard. Pour cette raison, les bogues, les effets causés par les bogues et les solutions de contournement des bogues ne sont pas documentés.

Les exceptions à cette règle s’appliquent aux notes de mise à jour dans lesquelles les problèmes connus peuvent être répertoriés avec les solutions possibles approuvées par la gestion des produits.

### Les contributions à la documentation ne sont pas destinées à répondre à des questions techniques.

Toute idée visant à améliorer la documentation d’AEM est la bienvenue sous forme de contributions. Toutefois, les commentaires, problèmes et demandes d’extraction sont uniquement destinés aux *contributions*. Ils ne sont pas destinés à répondre à vos questions sur l’utilisation d’AEM, la mise en œuvre de votre projet AEM ou la résolution de problèmes techniques.

Vous pouvez signaler toutes les questions relatives à l’utilisation d’AEM ou les erreurs techniques. Utilisez le processus d’assistance classique par le biais du portail d’assistance aux entreprises [Experience Cloud](https://experienceleague.adobe.com/fr?support-solution=General#support) ou discutez-en avec la [communauté Experience Manager](https://experienceleaguecommunities.adobe.com/t5/adobe-experience-manager/ct-p/adobe-experience-manager-community).

***Les contributions à la documentation d’AEM ne remplacent pas l’assistance clientèle d’Adobe*** et toute contribution de ce type visant à obtenir des réponses à des questions d’assistance est refusée.

### Les contributions doivent clairement faire référence aux pages de documentation concernées.

Si vous créez un problème afin de suggérer des améliorations à la documentation, vous devez inclure des liens vers les pages affectées. Si vous créez un problème à l’aide du lien **Modifier cette page** sur une page de documentation, le problème est automatiquement créé avec un lien vers la page.

Ce processus ne s’applique pas aux demandes d’extraction, car les demandes d’extraction, de par leur nature, font référence aux pages affectées.

## Instructions de documentation

Adobe demande que toute contribution à sa documentation respecte certaines directives de style.

Le respect de ces instructions facilite la révision de votre contribution et accélère par conséquent l’intégration à la documentation d’Adobe.

### Langue et style

#### Langue

* La documentation d’AEM est rédigée et conservée en anglais (États-Unis).
* Veillez à ce que les phrases soient aussi simples que possible.
* Gardez le langage clair et concis.

Souvenez-vous que les lecteurs de la documentation d’AEM sont internationaux et ne peuvent pas être des locuteurs anglais natifs ou bilingues. Évitez les termes familiers et veillez à ce qu&#39;ils soient aussi clairs et simples que possible.

#### Suivez le Manuel de style de ®

Le [Manuel de style de Microsoft®](https://learn.microsoft.com/en-us/style-guide/welcome/) est un guide de style de documentation disponible gratuitement qui se concentre sur la documentation logicielle et la documentation AEM suit ce guide dans la mesure du possible.

### Mise en forme

| Élément | Style |
|---|---|
| Élément ou option de l’interface utilisateur | **gras** |
| Nom de fichier, chemin, entrée utilisateur, valeurs de paramètre | `monospaced` |
| Code, ligne de commande | ```Code Block``` |

### Copies d’écran

Les captures d’écran doivent être utilisées de manière judicieuse et uniquement lorsqu’une description textuelle est insuffisante.

Les marques ou autres annotations dans les captures d’écran (cadres rouges, flèches ou texte) ne doivent pas être utilisées. Ainsi, les captures d’écran sont plus faciles à réutiliser ou à répliquer dans les versions localisées de la documentation.

### Références spécifiques à la version

Dans la mesure du possible, évitez toute référence directe à une version spécifique dans le contenu de la documentation. Cette recommandation rend la documentation plus flexible et extensible pour les versions ultérieures.

### Utilisation de Day, AEM, CQ, CRX

Dans un article, faites toujours référence au produit par son nom complet **&#x200B;**&#x200B;la première fois qu&#39;il est utilisé. Par la suite, il peut être appelé **&#x200B;**.

Day, Day Software, CQ et CRX ne doivent pas être utilisés, sauf lorsqu’ils sont inévitables, comme dans les noms de classe ou en référence à l’historique d’AEM.
