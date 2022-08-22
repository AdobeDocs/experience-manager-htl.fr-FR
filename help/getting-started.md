---
title: Prise en main de HTL
description: Découvrez HTL, le système de modèle côté serveur préféré et recommandé pour le HTML dans AEM, et comprenez les concepts majeurs du langage et ses concepts fondamentaux.
exl-id: c95eb1b3-3b96-4727-8f4f-d54e7136a8f9
source-git-commit: 5ab1275c984135fe946f36905bbc979cf19edd80
workflow-type: tm+mt
source-wordcount: '2170'
ht-degree: 54%

---


# Prise en main de HTL {#getting-started-with-htl}

Le langage HTL (HTML Template Language) est le système de modèle côté serveur préféré et recommandé pour le HTML dans Adobe Experience Manager. Comme dans tous les systèmes de modèle HTML côté serveur, un fichier HTL définit la sortie envoyée au navigateur en spécifiant le HTML lui-même, une logique de présentation de base et des variables à évaluer au moment de l’exécution.

Ce document donne un aperçu de l’objectif de HTL ainsi qu’une introduction aux concepts et concepts fondamentaux du langage.

>[!TIP]
>
>Ce document présente l’objectif de HTL et un aperçu de sa structure et de ses concepts fondamentaux. Si vous avez des questions sur une syntaxe spécifique, reportez-vous à la section [Spécification HTL.](specification.md)

## Calques HTL {#layers}

HTL utilisé dans AEM peut être défini par plusieurs calques.

1. **[Spécification HTL](specification.md)** - HTL est une spécification open source indépendante des plateformes, que tout le monde est libre de mettre en oeuvre.
1. **[Moteur de script HTL Sling](specification.md)** - Le projet Sling a créé l’implémentation de référence de HTL, qui est utilisée par AEM.
1. **[Extensions d’AEM](specification.md)** - AEM s’appuie sur le moteur de script HTL Sling pour offrir aux développeurs des fonctionnalités pratiques spécifiques à AEM.

Cette documentation HTL se concentre sur l’utilisation de HTL pour développer des solutions AEM. Il touche donc les trois couches, liant les ressources externes selon les besoins.

## Concepts fondamentaux de HTL {#fundamental-concepts-of-htl}

Le langage HTL utilise un langage d’expression pour insérer des éléments de contenu dans l’annotation restituée et des attributs de données HTML5 pour définir des instructions sur des blocs d’annotation (comme des conditions ou des itérations). Tandis que HTL est compilé dans les servlets Java, les expressions et les attributs de données HTL sont entièrement évalué côté serveur et rien ne reste visible dans le code HTML résultant.

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

On peut distinguer deux types différents de syntaxes :

* **Instructions de bloc** - Pour afficher de manière conditionnelle la variable `<h1>` élément , un `data-sly-test` L’attribut de données HTML5 est utilisé. HTL fournit plusieurs attributs de ce type, ce qui permet d’associer un comportement à n’importe quel élément de HTML, et tous sont précédés du préfixe `data-sly`.
* **Langage d’expression** - Les expressions HTL sont délimitées par la variable `${` et `}` caractères. Au moment de l’exécution, ces expressions sont évaluées et leur valeur est insérée dans le flux de sortie HTML.

Voir [Spécification HTL](specification.md) pour plus d’informations sur les deux syntaxes.

### Élément SLY   {#the-sly-element}

Un concept central de HTL consiste à offrir la possibilité de réutiliser des éléments de HTML existants pour définir des instructions de bloc, ce qui évite d’avoir à insérer des délimiteurs supplémentaires pour définir l’endroit où l’instruction commence et se termine. Cette annotation discrète de la balise pour transformer un HTML statique en modèle dynamique offre l’avantage de ne pas rompre la validité du code HTML. Par conséquent, l’affichage reste correct, même lorsque les fichiers sont statiques.

Toutefois, il se peut parfois qu’il n’existe pas d’élément à l’emplacement exact où une instruction de bloc doit être insérée. Dans ce cas, il est possible d’insérer une `sly` élément qui sera automatiquement supprimé de la sortie, lors de l’exécution des instructions de bloc jointes et de l’affichage de son contenu en conséquence.

L’exemple suivant...

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

...produit quelque chose comme le HTML suivant, mais uniquement s’il existe les deux, une `jcr:title` et un `jcr:description` définie, et si aucun d’eux n’est vide :

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

Gardez à l’esprit que si vous utilisez le `sly` lorsqu’aucun élément existant n’aurait pu être annoté avec l’instruction block . Ceci est dû au fait que `sly` les éléments dissuadent la valeur proposée par la langue de ne pas altérer le HTML statique lors de sa mise en dynamique.

Par exemple, si l’exemple précédent aurait été encapsulé dans une balise `div` , puis l’élément ajouté `sly` serait abusif :

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

et le `div` aurait pu être annoté avec la condition :

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

### Commentaires HTL   {#htl-comments}

L’exemple suivant illustre un commentaire HTL sur la première ligne et un commentaire de HTML sur la deuxième ligne.

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

Les commentaires HTL sont des commentaires HTML avec une syntaxe supplémentaire de type JavaScript. La totalité du commentaire HTL et tout ce qu’il contient sera entièrement ignorée par le processeur et supprimée de la sortie.

Le contenu des commentaires HTML standard sera toutefois transmis et les expressions dans le commentaire seront évaluées.

Les commentaires HTML ne peuvent pas contenir de commentaires HTL et vice versa.

### Contextes spéciaux   {#special-contexts}

Pour pouvoir tirer le meilleur parti de HTL, il est important de bien comprendre ce qu’implique le fait qu’il soit basé sur la syntaxe HTML.

Reportez-vous à la section [Afficher le contexte](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#121-display-context) de la spécification HTL pour plus d’informations.

### Noms des éléments et attributs   {#element-and-attribute-names}

Les expressions peuvent uniquement être placées dans du texte de HTML ou des valeurs d’attribut, mais pas dans des noms d’élément ou d’attribut, ou cela ne serait plus un HTML valide. Pour définir dynamiquement des noms d’éléments, l’instruction `data-sly-element` peut être utilisée sur les éléments souhaités. Pour définir dynamiquement des noms d’attributs, et même pour définir plusieurs attributs en même temps, vous pouvez utiliser l’instruction `data-sly-attribute`.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### Contextes sans instructions de bloc {#contexts-without-block-statements}

Comme HTL utilise des attributs de données pour définir les instructions de bloc, il n’est pas possible de définir ces instructions dans les contextes suivants, et seules les expressions peuvent être utilisées dans ce cas :

* Commentaires HTML
* Éléments de script
* Éléments de style

Cela s’explique par le fait que le contenu de ces contextes est textuel et non pas HTML. Les éléments HTML contenus seraient donc considérés en tant que simples données de caractères. Ainsi, sans véritables éléments HTML, les attributs `data-sly` ne peuvent pas non plus être exécutés.

Cela peut sembler une restriction importante, mais il s’agit d’une restriction souhaitée, car le langage de modèle de HTML ne doit pas être utilisé à mauvais escient pour générer une sortie qui n’est pas HTML. La section [Use-API pour accéder à la logique](#use-api-for-accessing-logic) ci-dessous explique comment une logique supplémentaire peut être appelée à partir du modèle, ce qui peut être employé si nécessaire afin de préparer une sortie complexe pour ces contextes. Par exemple, un moyen simple d’envoyer des données d’un script principal à un script frontal est d’utiliser la logique du composant pour générer une chaîne JSON, qui peut alors être placée dans un attribut de données avec une simple expression HTL.

L’exemple suivant illustre le comportement des commentaires HTML. Mais le même comportement pourrait être observé dans un script ou des éléments de style :

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

produit quelque chose qui ressemble au HTML suivant :

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### Contextes explicites requis   {#explicit-contexts-required}

Comme expliqué dans la section [Échappement automatique en fonction du contexte](#automatic-context-aware-escaping) ci-dessous, l’un des objectifs de HTL est de réduire les risques de vulnérabilité de type attaque multisite par scripts (XSS) en appliquant automatiquement l’échappement automatique à toutes les expressions en fonction du contexte. Bien que HTL puisse automatiquement détecter le contexte des expressions placées dans les balises HTML, il n’analyse pas la syntaxe JavaScript ni les styles CSS intégrés. C’est donc au développeur de spécifier explicitement un contexte précis qui doit être appliqué à de telles expressions.

Comme il n’applique pas les résultats d’échappement corrects dans des vulnérabilités XSS, HTL supprime donc la sortie de toutes les expressions qui se trouvent dans le script et les contextes de style lorsque le contexte n’a pas été déclaré.

Voici un exemple de la façon de définir le contexte des expressions placées dans des scripts et des styles :

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

Pour plus d’informations sur le contrôle de l’échappement, reportez-vous à la section [Contexte d’affichage du langage d’expression](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) des spécifications HTL.

## Capacités générales de HTL   {#general-capabilities-of-htl}

Cette section décrit brièvement les fonctionnalités générales du langage HTL.

### Use-API pour accéder à la logique   {#use-api-for-accessing-logic}

Le HTML Template Language (HTL) Java Use-API permet à un fichier HTL d’accéder aux méthodes d’assistance en classe Java personnalisée via `data-sly-use`. Cela permet à l’ensemble de la logique commerciale complexe d’être encapsulée dans le code Java tandis que le code HTL traite uniquement la production directe des balises.

Voir le document [Use-API Java HTL](java-use-api.md) pour plus d’informations.

### Échappement automatique basé sur le contexte {#automatic-context-aware-escaping}

Examinez l’exemple suivant :

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

Dans la plupart des langages de modèle, cet exemple devrait créer une vulnérabilité de type attaque multisite par scripts (XSS), car même si toutes les variables sont automatiquement placées dans une séquence d’échappement en HTML, l’attribut `href` doit toujours être placé dans une séquence d’échappement spécifiquement par l’URL. Cette omission est l’une des erreurs les plus courantes, car elle peut facilement être oubliée et il est difficile de la repérer de manière automatisée.

Pour faciliter la tâche, le langage de modèle HTML échappe automatiquement chaque variable en fonction du contexte dans lequel elle se trouve. Cela est possible grâce au fait que HTL comprend la syntaxe du HTML.

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

L’exemple initial peut entraîner le résultat suivant :

```xml
<p>
    <a href="#my%20link%27s%20safe" title="my title&#39;s safe">
        my text&#39;s safe
    </a>
</p>
```

Notez comment les deux attributs ont été placées dans une séquence d’échappement différemment, car HTL sait que `href` et `src` Les attributs doivent être précédés d’une séquence d’échappement pour le contexte URI. En outre, si l’URI commençait par `javascript:`, l’attribut aurait été entièrement supprimé, à moins que le contexte ne soit explicitement changé en autre chose.

Pour plus d’informations sur le contrôle de l’échappement, reportez-vous à la section [Contexte d’affichage du langage d’expression](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) des spécifications HTL.

### Suppression automatique des attributs vides {#automatic-removal-of-empty-attributes}

Examinez l’exemple suivant :

```xml
<p class="${properties.class}">some text</p>
```

Si la valeur de la propriété `class` est vide, le langage HTL supprime automatiquement l’ensemble de l’attribut `class` de la sortie.

Encore une fois, cela est possible, car HTL comprend la syntaxe du HTML et peut donc afficher de manière conditionnelle les attributs avec des valeurs dynamiques uniquement si leur valeur n’est pas vide. Cela s’avère très pratique, car cela évite d’ajouter un bloc de condition autour des attributs, ce qui aurait rendu le balisage non valide et illisible.

En outre, le type de variable placé dans l’expression est important :

* **Chaîne :**
   * **non vide :** définit la chaîne comme valeur d’attribut.
   * **vide :** supprime l’attribut.

* **Nombre :** définit la valeur en tant que valeur d’attribut.

* **Booléen :**
   * **true :** affiche l’attribut sans valeur (en tant qu’attribut HTML booléen
   * **false :** supprime l’attribut.

Voici un exemple de la manière dont une expression booléenne permettrait le contrôle d’un attribut de HTML booléen :

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

Pour définir des attributs, l’instruction `data-sly-attribute` peut également être utile.

## Modèles courants avec HTL {#common-patterns-with-htl}

Cette section présente quelques scénarios courants et la meilleure façon de les résoudre avec le langage de modèles HTML.

### Chargement des bibliothèques clientes {#loading-client-libraries}

Dans le langage HTL, les bibliothèques clientes sont chargées par le biais d’un modèle d’assistance fourni par AEM, auquel on peut accéder via `data-sly-use`. Trois modèles sont disponibles dans ce fichier, qui peuvent être appelés via `data-sly-call` :

* **`css`** : charge uniquement les fichiers CSS des bibliothèques clientes référencées.
* **`js`** : charge uniquement les fichiers JavaScript des bibliothèques clientes référencées.
* **`all`** : charge tous les fichiers des bibliothèques clientes référencées (à la fois CSS et JavaScript).

Chaque modèle d’assistance exige une option `categories` pour référencer les bibliothèques clientes souhaitées. Cette option peut être un tableau de valeurs de chaîne ou une chaîne contenant une liste de valeurs séparées par des virgules.

Voici deux exemples courts.

#### Chargement de plusieurs bibliothèques client en même temps   {#loading-multiple-client-libraries-fully-at-once}

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

Dans cet exemple, si le HTML `head` et `body` sont placés dans des fichiers différents, les `clientlib.html` doit ensuite être chargé dans chaque fichier qui en a besoin.

La section sur les instructions template &amp; call dans la variable [Spécification HTL](specification.md) fournit des détails supplémentaires sur la manière de déclarer et d’appeler de tels modèles.

### Transmission des données au client {#passing-data-to-the-client}

La meilleure et la plus élégante manière de transmettre des données au client en général, mais plus encore avec HTL, consiste à utiliser `data` attributs.

L’exemple suivant montre comment la logique (qui peut également être écrite en Java) peut être utilisée pour sérialiser facilement en JSON l’objet à transmettre au client, qui peut ensuite facilement être placé dans une `data` attribute:

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

Ensuite, il est facile d’imaginer comment un JavaScript côté client peut accéder à cet attribut et analyser à nouveau le JSON. Il s’agit, par exemple, du code JavaScript correspondant à placer dans une bibliothèque cliente :

```javascript
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### Utilisation des modèles côté client   {#working-with-client-side-templates}

Il existe un cas spécial où la technique décrite dans la section [Suppression des limites liées aux contextes spéciaux](#lifting-limitations-of-special-contexts) peut légitimement être utilisée : lors de l’écriture de modèles côté client (comme des Guidons par exemple) qui sont situés dans des éléments de `scrip`. Cette technique peut être utilisée sans risque dans ce cas, parce que l’élément de `script` ne contient alors pas le JavaScript prévu, mais d’autres éléments HTML. Voici un exemple de la manière dont elle peut s’exécuter :

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

Comme illustré ci-dessus, les balises qui seront incluses dans la variable `script` peut contenir des instructions de bloc HTL et les expressions n’ont pas besoin de fournir de contextes explicites, car le contenu du modèle Handlebars a été isolé dans son propre fichier . En outre, cet exemple montre comment le HTL exécuté côté serveur (comme sur l’élément `h2`) peut être mélangé à un langage de modèle exécuté côté client, comme Handlebars (montré sur l’élément `h3`).

Une technique plus moderne consisterait toutefois à utiliser l’élément HTML `template` à la place, car l’avantage serait alors de ne pas avoir à isoler le contenu des modèles dans des fichiers séparés.

### Suppression des limites liées aux contextes spéciaux {#lifting-limitations-of-special-contexts}

Dans les cas spéciaux où il est nécessaire de contourner les restrictions des contextes de script, de style et de commentaire, il est possible d’isoler leur contenu dans un fichier HTL distinct. Tout élément situé dans son propre fichier sera interprété par HTL sous forme de fragment HTML normal, sans prendre en compte le contexte de limite à partir duquel il peut avoir été inclus.

Voir la section [Utilisation des modèles côté client](#working-with-client-side-templates) plus bas pour obtenir un exemple.

>[!CAUTION]
>
>Cette technique peut introduire des vulnérabilités de script de site à site, et les aspects liés à la sécurité doivent être soigneusement étudiés si cela doit être fait. Il y a généralement de meilleures façons d’implémenter le même élément que de s’appuyer sur cette pratique.
