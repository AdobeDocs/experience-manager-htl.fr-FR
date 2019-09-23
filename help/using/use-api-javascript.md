---
title: Use-API JavaScript HTL
seo-title: Use-API JavaScript HTL
description: Use-API JavaScript HTL permet à un fichier HTL d’accéder au code d’assistance écrit en JavaScript.
seo-description: Use-API JavaScript HTL permet à un fichier HTL d’accéder au code d’assistance écrit en JavaScript.
uuid: 7ab34b10-30ac-44d6-926b-0234f52e5541
contentOwner: Utilisateur
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: référence
discoiquuid: 18871af8-e44b-4eec-a483-fcc765dae58f
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: bd1962e25d152be4f1608d0a83d8d5b3e728b4aa

---


# Use-API JavaScript HTL {#htl-javascript-use-api}

Use-API JavaScript HTL permet à un fichier HTL d’accéder au code d’assistance écrit en JavaScript. Cela permet à l’ensemble de la logique métier complexe d’être encapsulée dans le code JavaScript, tandis que le code HTL traite uniquement la production directe des balises.

## Un exemple simple   {#a-simple-example}

Nous définissons un composant, `info`, situé à l’adresse

**`/apps/my-example/components/info`**

Il contient deux fichiers :

* **`info.js`** : un fichier JavaScript qui définit la classe de l’utilisation.
* `info.html` : un fichier HTL qui définit le composant `info`. Ce code utilisera la fonctionnalité `info.js` via Use-API.

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

Nous créons également un nœud de contenu qui utilise le composant **`info`** à l’adresse

**`/content/my-example`**, avec les propriétés :

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

La logique correspondante peut être écrite à l’aide du code JavaScript ***côté serveur*** suivant, situé dans un fichier `component.js` à côté du modèle :

```
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

```
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

## Extension   {#extending}

Le motif de dépendance peut également être utilisé pour améliorer la logique d’un autre composant (en général, le `sling:resourceSuperType` du composant actif).

Imaginez que le composant parent fournisse déjà le `title` et que nous souhaitions également ajouter une **`description`** :

```
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

Dans le cas des instructions **`data-sly-template`** qui peuvent être indépendantes des composants, il peut être utile de transmettre des paramètres à l’Use-API associée.

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

La logique correspondante peut être écrite à l’aide du JavaScript ***côté serveur*** suivant, situé dans le fichier `template.js` à côté du fichier de modèle :

```
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
