---
title: HTL Java Use-API
description: L’Use-API Java HTL permet à un fichier HTL d’accéder aux méthodes d’assistance dans une classe Java personnalisée.
exl-id: 9a9a2bf8-d178-4460-a3ec-cbefcfc09959
source-git-commit: da2375a2390f0254dba9745d9f4970e88788e5d5
workflow-type: tm+mt
source-wordcount: '0'
ht-degree: 0%

---


# Use-API Java HTL {#htl-java-use-api}

L’Use-API Java HTL permet à un fichier HTL d’accéder aux méthodes d’assistance dans une classe Java personnalisée.

## Cas d’utilisation {#use-case}

Use-API Java HTL permet à un fichier HTL d’accéder aux méthodes d’assistance dans une classe Java personnalisée via `data-sly-use`. Cela permet à l’ensemble de la logique commerciale complexe d’être encapsulée dans le code Java tandis que le code HTL traite uniquement la production directe des balises.

Un objet java Use-API peut être un POJO simple, instancié par une implémentation particulière à travers le constructeur par défaut du POJO.

Les POJO Use-API peuvent également exposer une méthode publique, appelée init, avec la signature suivante :

```java
    /**
     * Initializes the Use bean.
     *
     * @param bindings All bindings available to the HTL scripts.
     **/
    public void init(javax.script.Bindings bindings);
```

La carte `bindings` peut contenir des objets qui fournissent du contexte au script HTL actuellement exécuté que l’objet Use-API peut utiliser pour son traitement.

## Un exemple simple   {#a-simple-example}

Cet exemple illustre l’utilisation de Use-API.

>[!NOTE]
>
>Cet exemple est simplifié afin d’illustrer simplement son utilisation. Dans un environnement de production, il est recommandé d’utiliser [Modèles Sling.](https://sling.apache.org/documentation/bundles/models.html)

Nous commençons avec un composant HTL, appelé `info`, qui ne comporte pas de classe d’utilisation. Il consiste en un seul fichier, `/apps/my-example/components/info.html`

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

Nous ajoutons également du contenu pour que ce composant effectue le rendu à `/content/my-example/` :

```xml
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

Lorsque l’on accède à ce contenu, le fichier HTL est exécuté. Dans le code HTL, nous utilisons l’objet de contexte `properties` pour accéder au `title` et `description` et les afficher. Le fichier de sortie `/content/my-example.html` sera :

```html
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Ajout d’une classe d’utilisation {#adding-a-use-class}

Le `info` en l’état, n’a pas besoin d’une classe d’utilisation pour exécuter sa fonction simple. Cependant, dans certains cas, vous devez effectuer des opérations qui ne peuvent pas être effectuées dans HTL, et vous avez donc besoin d’une classe d’utilisation. Cependant, gardez à l’esprit les considérations suivantes :

>[!NOTE]
>
>Une classe d’utilisation ne devrait être utilisée que lorsque quelque chose ne peut pas être fait dans le HTL seul.

Par exemple, supposons que vous souhaitiez que le composant `info` affiche les propriétés `title` et `description` de la ressource, mais toutes en minuscules. Comme HTL ne dispose pas d’une méthode pour mettre en minuscules les chaînes, vous aurez besoin d’une classe d’utilisation. Pour ce faire, ajoutez une classe d’utilisation Java et modifiez la variable `/apps/my-example/component/info/info.html` comme suit :

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

En outre, nous créons `/apps/my-example/component/info/Info.java`.

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    private String lowerCaseTitle;
    private String lowerCaseDescription;

    @Override
    public void activate() throws Exception {
        lowerCaseTitle = getProperties().get("title", "").toLowerCase();
        lowerCaseDescription = getProperties().get("description", "").toLowerCase();
    }

    public String getLowerCaseTitle() {
        return lowerCaseTitle;
    }

    public String getLowerCaseDescription() {
        return lowerCaseDescription;
    }
}
```

Veuillez consulter la [Javadocs pour `com.adobe.cq.sightly.WCMUsePojo`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) pour plus d’informations.

Maintenant, examinons les différentes parties du code.

### Comparaison entre la classe Java locale et celle groupée {#local-vs-bundle-java-class}

La classe d’utilisation Java peut être installée de deux manières :

* **Local** - Dans une installation locale, le fichier source Java est placé à côté du fichier HTL, dans le même dossier de référentiel. Sur demande, la source est automatiquement compilée. Aucune étape de compilation ou d’assemblage distincte n’est requise.
* **Bundle** - Dans une installation groupée, la classe Java doit être compilée et déployée dans un lot OSGi à l’aide du mécanisme de déploiement groupé standard AEM (voir la section [Classe Java groupée](#bundled-java-class)).

Pour savoir quelle méthode utiliser lorsque, tenez compte des deux points suivants :

* Une **classe d’utilisation Java locale** est recommandée quand la classe d’utilisation est spécifique au composant en question.
* Une **classe d’utilisation Java groupée** est recommandée quand le code Java met en œuvre un service qui est utilisé par plusieurs composants HTL.

Cet exemple utilise une installation locale.

### Le package Java est le chemin du référentiel {#java-package-is-repository-path}

Lorsqu’une installation locale est utilisée, le nom du pack de la classe d’utilisation doit correspondre à l’emplacement du dossier du référentiel, les traits d’union du chemin étant remplacés par des traits de soulignement dans le nom du pack.

Dans ce cas, `Info.java` se trouve à l’adresse `/apps/my-example/components/info`. Par conséquent, le pack est `apps.my_example.components.info` :

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {

   ...

}
```

>[!NOTE]
>
>L’utilisation de traits d’union dans les noms des éléments du référentiel est une pratique recommandée dans le développement AEM. Toutefois, les traits d’union sont des caractères interdits dans les noms de packs Java. C’est pourquoi **tous les tirets dans le chemin d’accès au référentiel doivent être convertis en traits de soulignement dans le nom du package**.

### Extension   `WCMUsePojo` {#extending-wcmusepojo}

Bien qu’il existe plusieurs façons d’incorporer une classe Java avec HTL (voir la section [Alternatives à `WCMUsePojo`](#alternatives-to-wcmusepojo)), le plus simple consiste à étendre la variable `WCMUsePojo` classe . Pour notre exemple `/apps/my-example/component/info/Info.java`:

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo

    ...
}
```

### Initialisation de la classe {#initializing-the-class}

Lorsque la classe d’utilisation est étendue à partir de `WCMUsePojo`, l’initialisation s’effectue en remplaçant la variable `activate` , dans ce cas, dans `/apps/my-example/component/info/Info.java`

```java
...

public class Info extends WCMUsePojo {
    private String lowerCaseTitle;
    private String lowerCaseDescription;

    @Override
    public void activate() throws Exception {
        lowerCaseTitle = getProperties().get("title", "").toLowerCase();
        lowerCaseDescription = getProperties().get("description", "").toLowerCase();
    }

...

}
```

### Contexte {#context}

En règle générale, la méthode [activate](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) est utilisé pour précalculer et stocker (dans des variables membres) les valeurs nécessaires dans votre code HTL, en fonction du contexte actif (la requête et la ressource actives, par exemple).

Le `WCMUsePojo` permet d’accéder au même ensemble d’objets de contexte que celui disponible dans un fichier HTL (voir le document . [Objets globaux.](global-objects.md))

Dans une classe qui étend `WCMUsePojo`, on peut accéder aux objets de contexte par leur nom en utilisant

[`<T> T get(String name, Class<T> type)`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)

Vous pouvez également accéder directement aux objets de contexte fréquemment utilisés par la méthode pratique appropriée, comme indiqué dans ce tableau.

| objet | Méthode de commodité |
|---|---|
| [`PageManager`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/PageManager.html) | [`getPageManager()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getPageManager--) |
| [`Page`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/Page.html) | [`getCurrentPage()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getCurrentPage--) |
| [`Page`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/Page.html) | [`getResourcePage()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResourcePage--) |
| [`ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) | [`getPageProperties()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getPageProperties--) |
| [`ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) | [`getProperties()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getProperties--) |
| [`Designer`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [`getDesigner()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getDesigner--) |
| [`Design`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/designer/Design.html) | [`getCurrentDesign()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getCurrentDesign--) |
| [`Style`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/designer/Style.html) | [`getCurrentStyle()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getCurrentStyle--) |
| [`Component`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/components/Component.html) | [`getComponent()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getComponent--) |
| [`ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) | [`getInheritedProperties()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getInheritedPageProperties--) |
| [`Resource`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/Resource.html) | [`getResource()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResource--) |
| [`ResourceResolver`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [`getResourceResolver()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResourceResolver--) |
| [`SlingHttpServletRequest`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [`getRequest()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getRequest--) |
| [`SlingHttpServletResponse`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [`getResponse()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResponse--) |
| [`SlingScriptHelper`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [`getSlingScriptHelper()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getSlingScriptHelper--) |

### Méthodes getter {#getter-methods}

Une fois que la classe d’utilisation est initialisée, le fichier HTL est exécuté. Au cours de cette étape, HTL récupère généralement l’état de plusieurs variables de membres de la classe de l’utilisation et les restitue pour la présentation.

Pour permettre l’accès à ces valeurs à partir du fichier HTL, vous devez définir des méthodes getter personnalisées dans la classe d’utilisation, conformément à la convention d’affectation des noms suivante :

* Une méthode de formulaire `getXyz` exposera dans le fichier HTL une propriété d’objet appelée `xyz`.

Dans l’exemple de fichier suivant `/apps/my-example/component/info/Info.java`, les méthodes `getTitle` et `getDescription` Résultat : les propriétés de l’objet `title` et `description` accessible dans le contexte du fichier HTL.

```java
...

public class Info extends WCMUsePojo {

    ...

    public String getLowerCaseTitle() {
        return lowerCaseTitle;
    }

    public String getLowerCaseDescription() {
        return lowerCaseDescription;
    }
}
```

### Attribut data-sly-use {#data-sly-use-attribute}

L’attribut `data-sly-use` est utilisé pour initialiser la classe d’utilisation dans votre code HTL. Dans notre exemple, l’attribut `data-sly-use` indique que nous voulons utiliser la classe `Info`. Nous pouvons utiliser uniquement le nom local de la classe, car nous utilisons une installation locale (le fichier source Java ayant été placé dans le même dossier que le fichier HTL). Si nous utilisions une installation groupée, nous devrions spécifier le nom de classe entièrement qualifié.

Notez l’utilisation de cette `/apps/my-example/component/info/info.html` par exemple.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Identifiant local {#local-identifier}

L’identificateur `info` (après le point dans `data-sly-use.info`) est utilisé dans le fichier HTL pour identifier la classe. La portée de cet identificateur est globale dans le fichier, une fois qu’il a été déclaré. Elle ne se limite pas à l’élément qui contient l’instruction `data-sly-use`. 

Notez l’utilisation de cette `/apps/my-example/component/info/info.html` par exemple.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Obtention des propriétés {#getting-properties}

L’identificateur `info` est alors utilisé pour accéder aux propriétés de l’objet `title` et `description`, qui ont été exposées par les méthodes getter `Info.getTitle` et `Info.getDescription`.

Notez l’utilisation de cette `/apps/my-example/component/info/info.html` par exemple.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Sortie {#output}

Maintenant, lorsque nous accédons à `/content/my-example.html` il renverra ce qui suit : `/content/my-example.html` fichier .

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

>[!NOTE]
>
>Cet exemple a été simplifié pour illustrer simplement son utilisation. Dans un environnement de production, il est recommandé d’utiliser [Modèles Sling.](https://sling.apache.org/documentation/bundles/models.html)

## Au-delà des concepts de base {#beyond-the-basics}

Cette section présente d’autres fonctionnalités qui vont au-delà de l’exemple simple décrit précédemment.

* Transfert de paramètres à une classe d’utilisation
* Classe d’utilisation Java groupée

### Transmission des paramètres {#passing-parameters}

Les paramètres peuvent être transmis à un chemin de classe d’utilisation lors de l’initialisation. Par exemple, nous pouvons effectuer ce qui suit :

Pour plus d’informations, reportez-vous à la section Sling [Documentation du moteur de script HTL.](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#passing-parameters-to-java-use-objects)

### Classe Java regroupée {#bundled-java-class}

Avec une classe d’utilisation groupée, la classe doit être compilée, empaquetée et déployée dans AEM à l’aide du mécanisme de déploiement groupé OSGi standard. Contrairement à une installation locale, la déclaration de package de classe d’utilisation doit être nommée normalement comme dans cette `/apps/my-example/component/info/Info.java` par exemple.

```java
package org.example.app.components;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    ...
}
```

Et le `data-sly-use` L’instruction doit référencer le nom de classe complet, par opposition au nom de classe local uniquement, comme dans cette instruction `/apps/my-example/component/info/info.html` par exemple.

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```
