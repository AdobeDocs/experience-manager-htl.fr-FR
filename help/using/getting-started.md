---
title: Prise en main de HTL
description: Découvrez HTL, le système de modèles côté serveur préféré et recommandé pour le HTML dans AEM, et comprenez les concepts majeurs du langage et ses éléments fondamentaux.
exl-id: c95eb1b3-3b96-4727-8f4f-d54e7136a8f9
source-git-commit: addc69e4b4e56a9b1c5f91ce9af26fa2d326d981
workflow-type: ht
source-wordcount: '2045'
ht-degree: 100%

---


# Prise en main de HTL {#getting-started-with-htl}

Le langage HTL (HTML Template Language) est le système de modèles côté serveur préféré et recommandé pour le HTML dans Adobe Experience Manager. Comme dans tous les systèmes de modèles HTML côté serveur, un fichier HTL définit la sortie envoyée au navigateur en spécifiant le code HTML lui-même, une logique de présentation de base et des variables à évaluer au moment de l’exécution.

Ce document donne un aperçu de l’objectif du HTL et offre une introduction aux concepts et éléments fondamentaux du langage.

>[!TIP]
>
>Ce document présente l’objectif du HTL et un aperçu de sa structure et de ses concepts fondamentaux. Si vous avez des questions sur une syntaxe spécifique, reportez-vous à la [Spécification HTL](specification.md).

## Calques HTL {#layers}

Dans AEM, plusieurs calques définissent HTL.

1. **[Spécification HTL](specification.md)** : HTL est une spécification open source indépendante de la plateforme, que tout le monde est libre de mettre en œuvre.
1. **[`Sling`Moteur de script HTL](specification.md)** : le projet `Sling` a créé l’implémentation de référence de HTL, qui est utilisée par AEM.
1. **[Extensions AEM](specification.md)** - AEM s’appuie sur le moteur de script HTL `Sling` pour offrir à l’équipe de développement des fonctionnalités pratiques spécifiques à AEM.

Cette documentation HTL se concentre sur l’utilisation de HTL pour développer des solutions AEM. Par conséquent, elle couvre les trois aspects, associant les ressources externes selon les besoins.

## Concepts fondamentaux de HTL {#fundamental-concepts-of-htl}

Le langage HTL utilise un langage d’expression pour insérer des éléments de contenu dans l’annotation restituée et des attributs de données HTML5 pour définir des instructions sur des blocs d’annotation (comme des conditions ou des itérations). Lorsque HTL est compilé dans des servlets Java, les expressions et les attributs de données HTL sont tous deux entièrement évalués côté serveur, et rien ne reste visible dans le HTML résultant.

>[!TIP]
>
>Pour exécuter la plupart des exemples fournis sur cette page, vous pouvez utiliser un environnement d’exécution en temps réel appelé [Read Eval Print Loop](https://github.com/adobe/aem-htl-repl).

### Blocs et expressions   {#blocks-and-expressions}

Voici un premier exemple qui peut être présenté tel quel dans un fichier `template.html` :

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

Nous pouvons distinguer deux différents types de syntaxe :

* **Instructions de bloc** - Si vous souhaitez afficher l’élément `<h1>` de manière conditionnelle, utilisez un attribut de données HTML5 `data-sly-test`. HTL fournit des attributs de ce type, ce qui permet d’associer un comportement à n’importe quel élément HTML, et tous ont le préfixe `data-sly`.
* **Langage d’expression** - Les caractères `${` et `}` délimitent les expressions HTL. Au moment de l’exécution, ces expressions sont évaluées et leur valeur est insérée dans le flux de sortie HTML.

Voir la [Spécification HTL](specification.md) pour plus d’informations sur les deux syntaxes.

### Élément SLY   {#the-sly-element}

Un concept central de HTL est d’offrir la possibilité de réutiliser des éléments HTML existants pour définir des instructions de bloc. Cette réutilisation évite d’insérer des délimiteurs supplémentaires pour définir l’endroit où l’instruction commence et se termine. L’annotation des balises transforme discrètement le HTML statique en modèle dynamique sans rompre la validité du HTML, assurant un affichage correct même sous la forme de fichiers statiques.

Cependant, il arrive parfois qu’il n’y ait pas d’élément existant à l’emplacement exact où une instruction de bloc doit être insérée. Dans ce cas, vous pouvez insérer un élément `sly` spécial. Cet élément est automatiquement supprimé de la sortie lors de l’exécution des instructions de bloc jointes et de l’affichage de son contenu en conséquence.

L’exemple suivant :

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

produit quelque chose comme le code HTML suivant, mais uniquement s’il existe à la fois un `jcr:title` et une propriété `jcr:description` définis, et si aucune d’eux n’est vide :

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

Pensez à n’utiliser l’élément `sly` que lorsqu’aucun élément existant n’aurait pu être annoté avec l’instruction de bloc. La raison en est que les éléments `sly` empêchent la valeur proposée par le langage de ne pas modifier le HTML statique lors du rendu dynamique.

Donc, si l’exemple précédent avait déjà été encapsulé dans un élément `div`, alors l’élément supplémentaire `sly` aurait été problématique :

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

Et l’élément `div` pourrait avoir été annoté avec la condition suivante :

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

### Commentaires HTL   {#htl-comments}

L’exemple suivant illustre un commentaire HTL sur la première ligne et un commentaire HTML sur la deuxième ligne.

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

Les commentaires HTL sont des commentaires HTML avec une syntaxe JavaScript supplémentaire. La personne responsable du traitement ignore entièrement le commentaire HTL entier et tout ce qui se trouve à l’intérieur, le supprimant de la sortie.

Le contenu des commentaires HTML standard est toutefois transmis et les expressions du commentaire sont évaluées.

Les commentaires HTML ne peuvent pas contenir de commentaires HTL et vice versa.

### Contextes spéciaux   {#special-contexts}

Pour pouvoir tirer le meilleur parti de HTL, il est important de bien comprendre ce qu’implique le fait qu’il soit basé sur la syntaxe HTML.

Reportez-vous à la [section Afficher le contexte](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#121-display-context) de la spécification HTL pour plus d’informations.

### Noms des éléments et attributs   {#element-and-attribute-names}

Des expressions peuvent uniquement être placées dans du texte HTML ou des valeurs d’attribut, mais pas dans des noms d’éléments ou d’attributs, car le HTML ne serait alors plus valide. Pour définir dynamiquement des noms d’éléments, l’instruction `data-sly-element` peut être utilisée sur les éléments souhaités. Pour définir dynamiquement des noms d’attributs, et même pour définir plusieurs attributs en même temps, vous pouvez utiliser l’instruction `data-sly-attribute`.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### Contextes sans instructions de bloc {#contexts-without-block-statements}

Comme HTL utilise des attributs de données pour définir des instructions de bloc, il n’est pas possible de définir de telles instructions de bloc dans les contextes suivants, et seules les expressions peuvent être utilisées dans ces cas :

* Commentaires HTML
* Éléments de script
* Éléments de style

Cela s’explique par le fait que le contenu de ces contextes est textuel et non pas HTML. Les éléments HTML contenus seraient donc considérés en tant que simples données de caractères. Ainsi, sans véritables éléments HTML, les attributs `data-sly` ne peuvent pas non plus être exécutés.

Cette approche peut sembler être une restriction significative. Toutefois, elle est préférable, car le langage de modèle HTML ne doit générer qu’une sortie HTML valide. La section [Use-API pour accéder à la logique](#use-api-for-accessing-logic) ci-dessous explique comment une logique supplémentaire peut être appelée à partir du modèle, ce qui peut être employé si nécessaire afin de préparer une sortie complexe pour ces contextes. Pour envoyer des données d’un script back-end à un script front-end, générez une chaîne JSON avec la logique du composant et placez-la dans un attribut de données à l’aide d’une simple expression HTL.

L’exemple suivant illustre le comportement des commentaires HTML, mais dans les éléments de script ou de style, le même comportement est observé :

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

Génère quelque chose semblable au HTML suivant :

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### Contextes explicites requis {#explicit-contexts-required}

Comme expliqué dans la section [Échappement automatique en fonction du contexte](#automatic-context-aware-escaping) ci-dessous, l’un des objectifs de HTL est de réduire les risques de vulnérabilité de type attaque cross-site scripting (XSS) en appliquant automatiquement l’échappement automatique à toutes les expressions en fonction du contexte. HTL détecte le contexte des expressions dans les balises HTML, mais n’analyse pas les balises JavaScript ou CSS intégrées. L’équipe de développement doit donc spécifier le contexte exact de ces expressions.

Comme il n’applique pas les résultats d’échappement corrects dans les vulnérabilités XSS, HTL supprime la sortie de toutes les expressions qui se trouvent dans les contextes de script et de style lorsque le contexte n’a pas été déclaré.

Voici un exemple de la façon de définir le contexte des expressions placées dans les scripts et les styles :

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

Pour plus de détails sur la façon de contrôler l’échappement, référez-vous à la section [Contexte d’affichage du langage d’expression](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) des spécifications HTL.

## Capacités générales du HTL   {#general-capabilities-of-htl}

Cette section décrit brièvement les fonctionnalités générales du langage HTL.

### Use-API pour accéder à la logique   {#use-api-for-accessing-logic}

L’API Use-API Java HTML Template Language (HTL) permet à un fichier HTL d’accéder aux méthodes d’assistance en classe Java personnalisée via `data-sly-use`. Ce processus permet à l’ensemble de la logique commerciale complexe d’être encapsulée dans le code Java tandis que le code HTL traite uniquement la production directe des balises.

Voir le document [Use-API Java HTL](java-use-api.md) pour plus d’informations.

### Échappement automatique basé sur le contexte {#automatic-context-aware-escaping}

Considérez l’exemple suivant :

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

Dans la plupart des langages de modèle, cet exemple devrait créer une vulnérabilité de type cross-site scripting (XSS), car même si toutes les variables sont automatiquement placées dans une séquence d’échappement en HTML, l’attribut `href` doit toujours être placé dans une séquence d’échappement spécifiquement par l’URL. Cette omission est l’une des erreurs les plus courantes, car cet élément peut facilement être oublié et il est difficile de le repérer de manière automatique.

Pour plus de facilité, le langage de modèle HTML présente automatiquement une séquence d’échappement pour chaque variable en fonction du contexte dans lequel elle se trouve. Cette approche est possible parce que le HTL comprend la syntaxe du HTML.

Considérons le fichier `logic.js` suivant :

```javascript
use(function () {
    return {
        link:  "#my link's safe",
        title: "my title's safe",
        text:  "my text's safe"
    };
});
```

L’exemple initial génère la sortie suivante :

```xml
<p>
    <a href="#my%20link%27s%20safe" title="my title&#39;s safe">
        my text&#39;s safe
    </a>
</p>
```

Notez comment les deux attributs sont placées dans une séquence d’échappement différemment, parce que le HTL sait que les attributs `href` et `src` doivent être placées dans une séquence d’échappement pour le contexte d’URI. En outre, si l’URI commençait par `javascript:`, l’attribut aurait été entièrement supprimé, à moins que le contexte ne soit explicitement changé en autre chose.

Pour plus de détails sur la façon de contrôler l’échappement, référez-vous à la section [Contexte d’affichage du langage d’expression](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) des spécifications HTL.

### Suppression automatique des attributs vides {#automatic-removal-of-empty-attributes}

Considérez l’exemple suivant :

```xml
<p class="${properties.class}">some text</p>
```

Si la valeur de la propriété `class` est vide, le langage de modèle HTL supprime automatiquement l’ensemble de l’attribut `class` de la sortie.

Encore une fois, ce processus est possible parce que le HTL comprend la syntaxe HTML et peut donc afficher de manière conditionnelle les attributs avec des valeurs dynamiques uniquement si leur valeur n’est pas vide. La raison est très pratique, car cela évite d’ajouter un bloc de condition aux attributs, ce qui aurait rendu les balises non valides et illisibles.

De plus, le type de variable placé dans l’expression est important :

* **Chaîne :**
   * **Non vide :** définit la chaîne comme valeur d’attribut.
   * **Vide :** supprime l’attribut.

* **Nombre :** définit la valeur en tant que valeur d’attribut.

* **Booléen :**
   * **true :** affiche l’attribut sans valeur (en tant qu’attribut HTML booléen
   * **false :** supprime l’attribut.

Voici un exemple de la manière dont une expression booléenne peut contrôler un attribut HTML booléen :

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

Pour définir des attributs, l’instruction `data-sly-attribute` peut également être utile.

## Modèles courants avec HTL {#common-patterns-with-htl}

Cette section présente quelques scénarios courants. Elle explique comment résoudre au mieux ces scénarios avec le langage de modèle HTML.

### Chargement des bibliothèques clientes {#loading-client-libraries}

Dans le langage HTL, les bibliothèques clientes sont chargées par le biais d’un modèle d’assistance fourni par AEM, auquel on peut accéder via `data-sly-use`. Trois modèles sont disponibles dans ce fichier, qui peuvent être appelés via `data-sly-call` :

* **`css`** : charge uniquement les fichiers CSS des bibliothèques clientes référencées.
* **`js`** : charge uniquement les fichiers JavaScript des bibliothèques clientes référencées.
* **`all`** : charge tous les fichiers des bibliothèques clientes référencées (à la fois CSS et JavaScript).

Chaque modèle d’assistance exige une option `categories` pour référencer les bibliothèques clientes souhaitées. Cette option peut être un tableau de valeurs de chaîne ou une chaîne contenant une liste de valeurs séparées par des virgules.

Voici deux courts exemples.

#### Chargement de plusieurs bibliothèques clientes en même temps {#loading-multiple-client-libraries-fully-at-once}

```xml
<sly data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html"
     data-sly-call="${clientlib.all @ categories=['myCategory1', 'myCategory2']}"/>
```

#### Référencement d’une bibliothèque cliente dans différentes sections d’une page {#referencing-a-client-library-in-different-sections-of-a-page}

```xml
<!doctype html>
<html data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html">
    <head>
        <!-- HTML meta-data -->
        <sly data-sly-call="${clientlib.css @ categories='myCategory'}"/>
    </head>
    <body>
        <!-- page content -->
        <sly data-sly-call="${clientlib.js @ categories='myCategory'}"/>
    </body>
</html>
```

Dans cet exemple, si les éléments HTML `head` et `body` sont placés dans des fichiers séparés, le modèle `clientlib.html` doit alors être chargé dans chaque fichier qui le requiert.

La section sur les instructions de modèle et d’appel de la [Spécification HTL](specification.md) fournit des détails supplémentaires sur la manière de déclarer et d’appeler de tels travaux de modèle.

### Transmission des données au client {#passing-data-to-the-client}

En général, la meilleure et la plus élégante des manières de transmettre des données au client, et d’autant plus avec HTL, est d’utiliser des attributs `data`.

L’exemple suivant montre comment sérialiser un objet au format JSON (également possible en Java) pour le transmettre au client ou à la cliente. Il peut ensuite être facilement placé dans un attribut `data` :

```xml
<!--/* template.html file: */-->
<div data-sly-use.logic="logic.js" data-json="${logic.json}">...</div>
```

```javascript
/* logic.js file: */
use(function () {
    var myData = {
        str: "foo",
        arr: [1, 2, 3]
    };

    return {
        json: JSON.stringify(myData)
    };
});
```

Ensuite, il est facile d’imaginer comment un JavaScript côté client peut accéder à cet attribut et analyser à nouveau le JSON. Cette approche correspondrait au JavaScript à placer dans une bibliothèque cliente, par exemple :

```javascript
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### Utilisation des modèles côté client {#working-with-client-side-templates}

Il existe un cas spécial où la technique décrite dans la section [Suppression des limites liées aux contextes spéciaux](#lifting-limitations-of-special-contexts) peut légitimement être utilisée : lors de l’écriture de modèles côté client (comme Handlebars par exemple) qui sont situés dans des éléments `scrip`. Cette technique peut être utilisée sans risque dans ce cas, parce que l’élément `script` ne contient alors pas le JavaScript prévu, mais d’autres éléments HTML. Voici un exemple de la manière dont elle peut s’exécuter :

```xml
<!--/* template.html file: */-->
<script id="entry-section" type="text/template"
    data-sly-include="entry-section.html"></script>

<!--/* entry-section.html file: */-->
<div class="entry-section">
    <h2 data-sly-test="${properties.entrySectionTitle}">
        ${properties.entrySectionTitle}
    </h2>
    {{#each entry}}<h3><a href="{{link}}">{{title}}</a></h3>{{/each}}
</div>
```

Le balisage de l’élément `script` peut inclure des instructions de bloc HTL sans contextes explicites, car le contenu du modèle Handlebars est isolé dans son propre fichier. En outre, cet exemple montre comment le HTL exécuté côté serveur (comme sur l’élément `h2`) peut être mélangé à un langage de modèle exécuté côté client, comme Handlebars (montré sur l’élément `h3`).

Une technique plus moderne consisterait toutefois à utiliser l’élément HTML `template` à la place, car l’avantage serait alors de ne pas avoir à isoler le contenu des modèles dans des fichiers séparés.

### Suppression des limites liées aux contextes spéciaux {#lifting-limitations-of-special-contexts}

Dans les cas spéciaux où il est nécessaire de contourner les restrictions des contextes de script, de style et de commentaire, il est possible d’isoler leur contenu dans un fichier HTL distinct. HTL interprète tout ce qui se trouve dans son propre fichier comme un fragment HTML standard, en ignorant tout contexte de limitation à partir duquel il a été inclus.

Voir la section [Utilisation des modèles côté client](#working-with-client-side-templates) plus bas pour obtenir un exemple.

>[!CAUTION]
>
>Cette technique peut introduire des vulnérabilités de type cross-site scripting (XSS). Si cette approche est utilisée, il convient d’étudier attentivement les aspects liés à la sécurité. Il y a généralement de meilleures façons d’implémenter le même élément que de s’appuyer sur cette pratique.
