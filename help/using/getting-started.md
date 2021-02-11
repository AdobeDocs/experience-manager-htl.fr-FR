---
title: Prise en main d’HTL
description: HTL pris en charge par AEM remplace JSP en tant que système de modèle côté serveur favori et recommandé pour HTML dans AEM.
translation-type: tm+mt
source-git-commit: f7e46aaac2a4b51d7fa131ef46692ba6be58d878
workflow-type: tm+mt
source-wordcount: '2471'
ht-degree: 92%

---


# Prise en main d’HTL {#getting-started-with-htl}

Le langage de modèle HTML (HTML Template Language) pris en charge par Adobe Experience Manager (AEM) est le système de modèle côté serveur recommandé et préféré pour HTML en AEM. Il remplace JSP (JavaServer Pages) comme utilisé dans les versions précédentes d’AEM.

>[!NOTE]
>
>Pour exécuter la plupart des exemples fournis sur cette page, un environnement d’exécution en temps réel appelé [Read Eval Print Loop](https://github.com/Adobe-Marketing-Cloud/aem-htl-repl) peut être utilisé.

## HTL par rapport à JSP {#htl-over-jsp}

Pour les nouveaux projets AEM, il est recommandé d’utiliser le langage HTL, car il permet de bénéficier de plusieurs avantages par rapport à JSP. Toutefois, pour les projets existants, une migration n’a de sens que si vous estimez qu’elle nécessite moins d’effort que la maintenance du JSP existant pour les années à venir.

Mais le passage au HTL n’est pas nécessairement un choix de type « tout ou rien », parce que les composants écrits en HTL sont compatibles avec les composants écrits en JSP ou ESP. Cela signifie que les projets existants peuvent utiliser HTL sans problème pour les nouveaux composants, tout en conservant JSP pour les composants existants.

Même au sein d’un même composant, des fichiers HTL peuvent être utilisés en même temps que des fichiers JSP et ESP. L’exemple suivant montre sur **la ligne 1** comment inclure un fichier HTL à partir d’un fichier JSP et sur **la ligne 2**, comment un fichier JSP peut être inclus à partir d’un fichier HTL :

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

### Questions fréquentes{#frequently-asked-questions}

Avant de commencer à utiliser le langage HTL, nous vous conseillons de lire d’abord les réponses aux questions liées à la comparaison entre JSP et HTL.

**HTL est-il plus limité que JSP ?** - HTL n&#39;a pas vraiment de limites par rapport à JSP en ce sens que ce qui peut être fait avec JSP devrait également être possible avec HTL. Cependant, la conception de HTL est plus stricte que celle de JSP sur plusieurs aspects. C’est pourquoi ce qui peut être réalisé à partir d’un seul fichier JSP peut devoir être divisé dans une classe Java ou un fichier JavaScript pour être réalisable en HTL. Mais cela est généralement souhaitable pour garantir une bonne séparation des problèmes entre la logique et l’annotation.

**HTL prend-il en charge les bibliothèques de balises JSP ?** - Non, mais comme l’indique la section [Téléchargement des bibliothèques client](getting-started.md#loading-client-libraries), les instructions [template &amp; call](block-statements.md#template-call) offrent un schéma similaire.

**Les fonctionnalités HTL peuvent-elles être élargies pour un projet AEM ?** - Non, cela n’est pas possible. HTL dispose de mécanismes d’extension puissants pour la réutilisation de la logique ([Use-API](getting-started.md#use-api-for-accessing-logic)) et du balisage (les instructions [template &amp; call](block-statements.md#template-call)), qui peuvent être utilisés pour modulariser le code des projets.

**Quels sont les principaux avantages de HTL par rapport à JSP ?** - La sécurité et l&#39;efficacité des projets sont les principaux avantages, qui sont décrits en détail dans le  [Aperçu](overview.md).

**JSP finira-t-il par disparaître ?** - À l&#39;heure actuelle, il n&#39;y a pas de plans de ce genre.

## Concepts fondamentaux de HTL {#fundamental-concepts-of-htl}

Le langage HTL utilise un langage d’expression pour insérer des éléments de contenu dans l’annotation restituée et des attributs de données HTML5 pour définir des instructions sur des blocs d’annotation (comme des conditions ou des itérations). Tandis que HTL est compilé dans les servlets Java, les expressions et les attributs de données HTL sont entièrement évalué côté serveur et rien ne reste visible dans le code HTML résultant.

### Blocs et expressions   {#blocks-and-expressions}

Voici un premier exemple, qui peut être contenu tel quel dans un fichier **`template.html`** :

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

Nous pouvons distinguer deux différents types de syntaxe :

* **[Bloquer les instructions](block-statements.md)**  - Pour afficher de façon conditionnelle la variable  **&lt;h1>** , un attribut de données  [`data-sly-test`](block-statements.md#test) HTML5 est utilisé. HTL fournit des attributs de ce type, ce qui permet d’associer un comportement à n’importe quel élément HTML, et ils sont tous préfixés avec `data-sly`.

* **[Langue](expression-language.md)**  d’Expression - Les expressions HTML sont délimitées par des caractères  `${` et  `}`des caractères. Au moment de l’exécution, ces expressions sont évaluées et leur valeur est insérée dans le flux de sortie HTML.

Les deux pages liées ci-dessus fournissent une liste détaillée des fonctionnalités disponibles pour la syntaxe.

### Élément SLY    {#the-sly-element}

Un concept central de HTL est d’offrir la possibilité de réutiliser les éléments HTML existants pour définir des instructions de bloc, ce qui contourne le besoin d’insérer des délimiteurs supplémentaires pour définir le début et la fin de l’instruction. Cette annotation discrète de la balise pour transformer un HTML statique en modèle dynamique offre l’avantage de ne pas rompre la validité du code HTML. Par conséquent, l’affichage reste correct, même lorsque les fichiers sont statiques.

Toutefois, il se peut parfois qu’il n’existe pas d’élément à l’emplacement exact où une instruction de bloc doit être insérée. Dans ce cas, il est possible d’insérer un élément SLY spécial qui est automatiquement supprimé de la sortie lors de l’exécution des instructions du bloc joint et de l’affichage de son contenu qui en découle.

C’est pourquoi l’exemple suivant :

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

génère un résultat similaire au code HTML suivant, mais uniquement s’il existe les deux, une propriété **`jcr:title`** et une propriété **`jcr:description`** définies, et si aucune d’elles n’est vide :

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

Une chose à garder à l&#39;esprit est de n&#39;utiliser l&#39;élément SLY que lorsqu&#39;aucun élément existant n&#39;aurait pu être annoté avec l&#39;instruction de bloc, parce que les éléments SLY dissuadent la valeur offerte par la langue de ne pas modifier le code HTML statique lors de sa création dynamique.

Par exemple, si l’exemple précédent avait déjà été imbriqué dans un élément DIV, l’élément SLY supplémentaire aurait été problématique :

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

et l’élément DIV peut avoir été annoté avec la condition :

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

### Commentaires HTL    {#htl-comments}

L’exemple suivant montre un commentaire HTL sur **la ligne 1**, et un commentaire HTML sur **la ligne 2** :

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

Les commentaires HTL sont des commentaires HTML avec une syntaxe supplémentaire de type JavaScript. La totalité du commentaire HTL et tout ce qu’il contient sera entièrement ignorée par le processeur et supprimée de la sortie.

Le contenu des commentaires HTML standard sera toutefois transmis et les expressions dans le commentaire seront évaluées.

Les commentaires HTML ne peuvent pas contenir de commentaires HTL et vice versa.

### Contextes spéciaux    {#special-contexts}

Pour pouvoir tirer le meilleur parti de HTL, il est important de bien comprendre ce qu’implique le fait qu’il soit basé sur la syntaxe HTML.

### Noms des éléments et attributs    {#element-and-attribute-names}

Les expressions peuvent uniquement être placées dans du texte HTML ou des valeurs d’attribut, mais pas dans des noms d’éléments ou d’attributs, car le HTML ne serait alors plus valide. Pour définir dynamiquement des noms d’éléments, l’instruction [`data-sly-element`](block-statements.md#element) peut être utilisée sur les éléments souhaités. Pour définir dynamiquement des noms d’attributs, voire pour définir plusieurs attributs en même temps, l’instruction [`data-sly-attribute`](block-statements.md#attribute) peut être utilisée.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### Contextes sans instructions de bloc {#contexts-without-block-statements}

Comme HTL utilise des attributs de données pour définir les instructions de bloc, il n’est pas possible de définir ces instructions dans les contextes suivants, et seules les expressions peuvent être utilisées dans ce cas :

* Commentaires HTML
* Éléments de script
* Éléments de style

Cela s’explique par le fait que le contenu de ces contextes est textuel et non pas HTML. Les éléments HTML contenus seraient donc considérés en tant que simples données de caractères. Ainsi, sans véritables éléments HTML, les attributs **`data-sly`** ne peuvent pas être exécutés.

Cela peut sembler particulièrement restrictif. Pourtant, cela est souhaitable, car le langage HTL ne doit pas être utilisé abusivement pour générer une sortie qui n’est pas HTML. La section [Use-API pour accéder à la logique](getting-started.md#use-api-for-accessing-logic) ci-dessous explique comment une logique supplémentaire peut être appelée à partir du modèle, ce qui peut être employé si nécessaire afin de préparer une sortie complexe pour ces contextes. Par exemple, un moyen simple d’envoyer des données d’un script principal à un script frontal est d’utiliser la logique du composant pour générer une chaîne JSON, qui peut alors être placée dans un attribut de données avec une simple expression HTL.

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

### Contextes explicites requis    {#explicit-contexts-required}

Comme expliqué dans la section [Échappement automatique en fonction du contexte](getting-started.md#automatic-context-aware-escaping) ci-dessous, l’un des objectifs de HTL est de réduire les risques de vulnérabilité de type attaque multisite par scripts (XSS) en appliquant automatiquement l’échappement automatique à toutes les expressions en fonction du contexte. Bien que HTL puisse automatiquement détecter le contexte des expressions placées dans les balises HTML, il n’analyse pas la syntaxe JavaScript ni les styles CSS intégrés. C’est donc au développeur de spécifier explicitement un contexte précis qui doit être appliqué à de telles expressions.

Comme il n’applique pas les résultats d’échappement corrects dans des vulnérabilités XSS, HTL supprime donc la sortie de toutes les expressions qui se trouvent dans le script et les contextes de style lorsque le contexte n’a pas été déclaré.

Voici un exemple de la façon de définir le contexte des expressions placées dans des scripts et des styles :

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

Pour plus de détails sur la façon de contrôler l’échappement, référez-vous à la section [Contexte d’affichage du langage d’expression](expression-language.md#display-context).

### Suppression des limites liées aux contextes spéciaux {#lifting-limitations-of-special-contexts}

Dans les cas spéciaux où il est nécessaire de contourner les restrictions des contextes de script, de style et de commentaire, il est possible d’isoler leur contenu dans un fichier HTL distinct. Tout élément situé dans son propre fichier sera interprété par HTL sous forme de fragment HTML normal, sans prendre en compte le contexte de limite à partir duquel il peut avoir été inclus.

Voir la section [Utilisation des modèles côté client](getting-started.md#working-with-client-side-templates) plus bas pour obtenir un exemple.

>[!CAUTION]
>
>Cette technique peut présenter des vulnérabilités de type attaque multisite par scripts (XSS). C’est pourquoi les aspects liés à la sécurité doivent être soigneusement étudiés en cas d’utilisation. Il y a généralement de meilleures façons d’implémenter le même élément que de s’appuyer sur cette pratique.

## Capacités générales de HTL    {#general-capabilities-of-htl}

Cette section décrit brièvement les fonctionnalités générales du langage HTL.

### Use-API pour accéder à la logique    {#use-api-for-accessing-logic}

Examinez l’exemple suivant :

```xml
<p data-sly-use.logic="logic.js">${logic.title}</p>
```

Et le fichier JavaScript `logic.js` exécuté côté serveur situé à côté :

```javascript
use(function () {
    return {
        title: currentPage.getTitle().substring(0, 10) + "..."
    };
});
```

Comme le langage HTL ne permet pas de fusionner le code à l’intérieur des balises, il offre à la place le mécanisme d’extension Use-API pour exécuter facilement le code du modèle.

L’exemple ci-dessus utilise du JavaScript exécuté du côté serveur pour raccourcir le titre à 10 caractères, mais il aurait pu également utiliser du code Java pour faire de même en fournissant un nom de classe Java entièrement qualifié. Généralement, la logique métier doit être créée de préférence en Java, mais lorsque le composant nécessite quelques modifications spécifiques à la vue à partir de ce qui est fourni par l’API Java, il peut être commode de les effectuer à l’aide de JavaScript exécuté côté serveur.

Plus d’informations seront fournies à ce sujet dans les sections suivantes :

* La section sur [`data-sly-use`l’instruction ](block-statements.md#use) explique tout ce qui peut être réalisé avec cette instruction.
* La [page Use-API](use-api.md) offre des informations pour vous aider à choisir entre l’écriture d’une logique en Java ou en JavaScript.
* Les pages [Use-API JavaScript](use-api-javascript.md) et [Use-API Java](use-api-java.md) devraient vous aider à comprendre comment écrire la logique.

### Échappement automatique en fonction du contexte {#automatic-context-aware-escaping}

Examinez l’exemple suivant :

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

Dans la plupart des langages de modèle, cet exemple devrait créer une vulnérabilité de type attaque multisite par scripts (XSS), car même si toutes les variables sont automatiquement placées dans une séquence d’échappement en HTML, l’attribut `href` doit toujours être placé dans une séquence d’échappement spécifiquement par l’URL. Cette omission est l’une des erreurs les plus courantes, car cet élément peut facilement être oublié et il est difficile de le repérer de manière automatique.

Pour faciliter la tâche, le langage de modèle HTML échappe automatiquement chaque variable en fonction du contexte dans lequel elle se trouve. Cela est possible parce que HTL comprend la syntaxe du HTML.

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

Notez comment les deux attributs sont placées dans une séquence d’échappement différemment, parce que HTL sait que les attributs `href` et `src` doivent être placées dans une séquence d’échappement pour le contexte d’URI. En outre, si l’URI commençait par **`javascript:`**, l’attribut aurait été entièrement supprimé, à moins que le contexte n’ait été explicitement modifié.

Pour plus de détails sur la façon de contrôler l’échappement, référez-vous à la section [Contexte d’affichage du langage d’expression](expression-language.md#display-context).

### Suppression automatique des attributs vides {#automatic-removal-of-empty-attributes}

Examinez l’exemple suivant :

```xml
<p class="${properties.class}">some text</p>
```

Si la valeur de la propriété `class` est vide, le langage HTL supprime automatiquement l’ensemble de l’attribut `class` de la sortie.

Encore une fois, cela est possible parce que HTL comprend la syntaxe HTML et peut donc afficher de manière conditionnelle les attributs avec des valeurs dynamiques uniquement si la valeur n’est pas vide. Cela est très pratique, car cela évite d’ajouter un bloc de condition aux attributs, ce qui aurait rendu les balises invalides et illisibles.

En outre, le type de variable placé dans l’expression est important :

* **Chaîne:**
   * **non vide :** définit la chaîne comme valeur d’attribut.
   * **vide :** supprime l’attribut.

* **Nombre :** définit la valeur en tant que valeur d’attribut.

* **Booléen :**
   * **true :** affiche l’attribut sans valeur (en tant qu’attribut HTML booléen
   * **false :** supprime l’attribut.

Voici un exemple de la manière dont une expression booléenne peut contrôler un attribut HTML booléen :

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

Pour définir les attributs, l’instruction [`data-sly-attribute`](block-statements.md#attribute) peut également être utile.

## Cas courants avec HTL {#common-patterns-with-htl}

Cette section présente quelques scénarios courants et la manière de les résoudre au mieux avec le langage HTL.

### Téléchargement des bibliothèques client {#loading-client-libraries}

Dans HTL, les bibliothèques clientes sont chargées à l’aide d’un modèle d’assistance fourni par AEM, accessible via [`data-sly-use`](block-statements.md#use). Trois modèles sont disponibles dans ce fichier, qui peut être appelé via [`data-sly-call`](block-statements.md#template-call)  :

* **`css`** charge seulement les fichiers CSS des bibliothèques client référencées.
* **`js`** charge seulement les fichiers JavaScript des bibliothèques client référencées.
* **`all`** charge tous les fichiers des bibliothèques client référencées (CSS et JavaScript).

Chaque modèle d’assistance exige une option **`categories`** pour référencer les bibliothèques clientes souhaitées. Cette option peut être un tableau de valeurs de chaîne ou une chaîne contenant une liste de valeurs séparées par des virgules.

Voici deux exemples courts :

### Chargement de plusieurs bibliothèques client en même temps    {#loading-multiple-client-libraries-fully-at-once}

```xml
<sly data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html"
     data-sly-call="${clientlib.all @ categories=['myCategory1', 'myCategory2']}"/>
```

### Référencement d’une bibliothèque cliente dans différentes sections d’une page {#referencing-a-client-library-in-different-sections-of-a-page}

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

Dans le deuxième exemple ci-dessus, au cas où les éléments HTML **`head`** et **`body`** sont placés dans des fichiers différents, le modèle **`clientlib.html`** doit alors être chargé dans chaque fichier qui le requiert.

La section sur les instructions [template &amp; call](block-statements.md#template-call) fournit des détails supplémentaires sur la manière de déclarer et d’appeler de tels travaux de modèle.

### Transmission des données au client {#passing-data-to-the-client}

En général, la meilleure et la plus élégante des manières de transmettre des données au client, et d’autant plus avec HTL, est d’utiliser des attributs de données.

L’exemple suivant indique comment la logique (qui peut également être écrite de Java) peut être utilisée pour sérialiser de manière très pratique en JSON l’objet qui doit être transmis au client, qui peut ensuite être défini très facilement dans un attribut de données :

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

Ensuite, il est facile d’imaginer comment un JavaScript côté client peut accéder à cet attribut et analyser à nouveau le JSON. Ce code pourrait être un exemple de JavaScript correspondant à placer dans une bibliothèque cliente :

```javascript
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### Utilisation des modèles côté client    {#working-with-client-side-templates}

Il existe un cas spécial où la technique décrite dans la section [Suppression des limites liées aux contextes spéciaux](getting-started.md#lifting-limitations-of-special-contexts) peut légitimement être utilisée : lors de l’écriture de modèles côté client (comme des Guidons par exemple) qui sont situés dans des éléments de **script**. Cette technique peut être utilisée sans risque dans ce cas, parce que l’élément de **script** ne contient alors pas le JavaScript prévu, mais d’autres éléments HTML. Voici un exemple de la manière dont elle peut s’exécuter :

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

Comme montré ci-dessus, les balises qui seront incluses dans l’élément **`script`** peuvent contenir des instructions de bloc HTL, et les expressions ne doivent pas nécessairement fournir de contextes explicites, parce que le contenu du modèle de Guidons a été isolé dans son propre fichier. En outre, cet exemple montre comment le code HTL exécuté côté serveur (comme sur l’élément **`h2`**) peut être mélangé avec un langage de modèle exécuté côté client, comme les Guidons (affichés sur l’élément **`h3`**).

Cependant, une technique plus moderne serait d’utiliser l’élément **`template`** HTML à la place, qui a pour avantage de ne pas nécessiter l’isolement du contenu des modèles dans des fichiers séparés.

**À lire aussi :**

* [Langage d’expression](expression-language.md) : pour apprendre en détail ce qui peut être réalisé avec les expressions HTL.
* [Instructions de bloc](block-statements.md) : pour découvrir toutes les instructions de bloc disponibles en HTL et comment les employer.
