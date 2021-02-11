---
title: Use-API JavaScript HTL
description: Le langage de modèle HTML - HTL - JavaScript Use-API permet à un fichier HTML d’accéder au code d’assistance écrit dans JavaScript.
translation-type: tm+mt
source-git-commit: f7e46aaac2a4b51d7fa131ef46692ba6be58d878
workflow-type: tm+mt
source-wordcount: '324'
ht-degree: 87%

---


# Use-API JavaScript HTL {#htl-javascript-use-api}

L’API JavaScript Use-API HTML Template Language (HTML Template Language) permet à un fichier HTML d’accéder au code d’assistance écrit en JavaScript. Cela permet à l’ensemble de la logique métier complexe d’être encapsulée dans le code JavaScript, tandis que le code HTL traite uniquement la production directe des balises.

Les conventions suivantes sont utilisées.

```javascript
/**
 * In the following example '/libs/dep1.js' and 'dep2.js' are optional
 * dependencies needed for this script's execution. Dependencies can
 * be specified using an absolute path or a relative path to this
 * script's own path.
 *
 * If no dependencies are needed the dependencies array can be omitted.
 */
use(['dep1.js', 'dep2.js'], function (Dep1, Dep2) {
    // implement processing
  
    // define this Use object's behavior
    return {
        propertyName: propertyValue
        functionName: function () {}
    }
});
```

## Un exemple simple   {#a-simple-example}

Nous définissons un composant, `info`, situé à l’adresse

`/apps/my-example/components/info`

Il contient deux fichiers :

* **`info.js`** : un fichier JavaScript qui définit la classe de l’utilisation.
* **`info.html`** : un fichier HTL qui définit le composant `info`. Ce code utilisera la fonctionnalité `info.js` via Use-API.

### /apps/my-example/component/info/info.js {#apps-my-example-component-info-info-js}

```java
"use strict";
use(function () {
    var info = {};
    info.title = resource.properties["title"];
    info.description = resource.properties["description"];
    return info;
});
```

### /apps/my-example/component/info/info.html {#apps-my-example-component-info-info-html}

```xml
<div data-sly-use.info="info.js">
    <h1>${info.title}</h1>
    <p>${info.description}</p>
</div>
```

Nous créons également un nœud de contenu qui utilise le composant `info` à l’adresse

`/content/my-example`, avec les propriétés :

* `sling:resourceType = "my-example/component/info"`
* `title = "My Example"`
* `description = "This is some example content."`

Voici la structure de référentiel obtenue :

### Structure du référentiel {#repository-structure}

```java
{
  "apps": {
    "my-example": {
      "components": {
        "info": {
          "info.html": {
            ...
          },
          "info.js": {
            ...
          }
        }
      }
    }
 },
 "content": {
    "my-example": {
      "sling:resourceType": "my-example/component/info",
      "title": "My Example",
      "description": "This is some example content."
    }
  }
}
```

Envisagez le modèle de composant suivant :

```xml
<section class="component-name" data-sly-use.component="component.js">
    <h1>${component.title}</h1>
    <p>${component.description}</p>
</section>
```

La logique correspondante peut être écrite à l’aide du code JavaScript côté serveur suivant, situé dans un fichier `component.js` à côté du modèle :

```javascript
use(function () {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };

    var title = currentPage.getNavigationTitle() || currentPage.getTitle() || currentPage.getName();
    var description = properties.get(Constants.DESCRIPTION_PROP, "").substr(0, Constants.DESCRIPTION_LENGTH);

    return {
        title: title,
        description: description
    };
});
```

Cette option tente de récupérer `title` à partir de différentes sources et coupe la description à 50 caractères.

## Dépendances {#dependencies}

Imaginons que nous ayons une classe d’utilitaires qui est déjà équipée de fonctionnalités intelligentes, comme la logique par défaut pour le titre de navigation ou la restriction d’une chaîne à une certaine longueur :

```javascript
use(['../utils/MyUtils.js'], function (utils) {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };

    var title = utils.getNavigationTitle(currentPage);
    var description = properties.get(Constants.DESCRIPTION_PROP, "").substr(0, Constants.DESCRIPTION_LENGTH);

    return {
        title: title,
        description: description
    };
});
```

## Extension    {#extending}

Le motif de dépendance peut également être utilisé pour améliorer la logique d’un autre composant (en général, le `sling:resourceSuperType` du composant actif).

Imaginez que le composant parent fournisse déjà le `title` et que nous souhaitions également ajouter une `description` :

```javascript
use(['../parent-component/parent-component.js'], function (component) {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };

    component.description = utils.shortenString(properties.get(Constants.DESCRIPTION_PROP, ""), Constants.DESCRIPTION_LENGTH);

    return component;
});
```

## Transmission des paramètres à un modèle {#passing-parameters-to-a-template}

Dans le cas des instructions `data-sly-template` qui peuvent être indépendantes des composants, il peut être utile de transmettre des paramètres à l’Use-API associée.

Par conséquent, dans notre composant, appelons un modèle qui se trouve dans un autre fichier :

```xml
<section class="component-name" data-sly-use.tmpl="template.html" data-sly-call="${tmpl.templateName @ page=currentPage}"></section>
```

Il s’agit alors du modèle situé dans `template.html` :

```xml
<template data-sly-template.templateName="${@ page}" data-sly-use.tmpl="${'template.js' @ page=page, descriptionLength=50}">
    <h1>${tmpl.title}</h1>
    <p>${tmpl.description}</p>
</template>
```

La logique correspondante peut être écrite à l’aide du JavaScript côté serveur suivant, situé dans le fichier `template.js` à côté du fichier de modèle :

```javascript
use(function () {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description"
    };

    var title = this.page.getNavigationTitle() || this.page.getTitle() || this.page.getName();
    var description = this.page.getProperties().get(Constants.DESCRIPTION_PROP, "").substr(0, this.descriptionLength);

    return {
        title: title,
        description: description
    };
});
```

Les paramètres transmis sont définis sur le mot-clé `this`.
