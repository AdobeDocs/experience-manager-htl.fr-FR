---
title: Java Use-API HTL
description: Le Java Use-API HTL permet à un fichier HTL d’accéder aux méthodes d’assistance de classe Java personnalisées.
exl-id: 9a9a2bf8-d178-4460-a3ec-cbefcfc09959
source-git-commit: ebeac25c38b81c92011c163c7860688f43547a7d
workflow-type: tm+mt
source-wordcount: '1137'
ht-degree: 69%

---


# Java Use-API HTL {#htl-java-use-api}

Le Java Use-API HTL permet à un fichier HTL d’accéder aux méthodes d’assistance de classe Java personnalisées.

## Cas d’utilisation {#use-case}

Le Java Use-API HTL permet à un fichier HTL d’accéder aux méthodes d’assistance dans une classe Java personnalisée via `data-sly-use`. Cette fonctionnalité permet à toute logique métier complexe d’être encapsulée dans le code Java, tandis que le code HTL traite uniquement de la production de balises directes.

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

## Un exemple simple {#a-simple-example}

Cet exemple illustre l’utilisation du Use-API.

>[!NOTE]
>
>Cet exemple est simplifié uniquement pour illustrer son utilisation. Dans un environnement de production, Adobe recommande d’utiliser les [modèles Sling](https://sling.apache.org/documentation/bundles/models.html).

Commencez par un composant HTL, appelé `info` qui n’a pas de classe d’utilisation. Il consiste en un seul fichier, `/apps/my-example/components/info.html`

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

Maintenant, ajoutez du contenu pour que ce composant soit rendu à `/content/my-example/` :

```xml
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

Lors de l’accès à ce contenu, le fichier HTL est exécuté. Dans le code HTL, l’objet de contexte `properties` est utilisé pour accéder aux `title` et `description` de la ressource active et les afficher. Le fichier de sortie `/content/my-example.html` est :

```html
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Ajouter une classe d’utilisation {#adding-a-use-class}

Le composant `info` en tant que tel n’a pas besoin d’une classe d’utilisation pour remplir sa simple fonction. Cependant, il existe des cas où vous devez effectuer des tâches qui ne peuvent pas être réalisées en HTL. Vous avez donc besoin d’une classe d’utilisation. Gardez toutefois à l’esprit les points suivants :

>[!NOTE]
>
>Une classe d’utilisation ne devrait être utilisée que lorsque quelque chose ne peut pas être fait dans le HTL seul.

Par exemple, supposons que vous souhaitiez que le composant `info` affiche les propriétés `title` et `description` de la ressource, mais toutes en minuscules. Comme HTL ne dispose pas d’une méthode pour mettre en minuscules les chaînes, vous avez besoin d’une classe d’utilisation en ajoutant une classe d’utilisation Java et en modifiant `/apps/my-example/component/info/info.html` comme suit :

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

De plus, `/apps/my-example/component/info/Info.java` est créé.

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

Pour plus d’informations, consultez la [documentation Java pour `com.adobe.cq.sightly.WCMUsePojo`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) .

Maintenant, examinons les différentes parties du code.

### Comparaison entre la classe Java locale et celle groupée {#local-vs-bundle-java-class}

La classe d’utilisation Java peut être installée de deux manières :

* **Local** - dans le cas d’une installation locale, le fichier source Java est placé à côté du fichier HTL, dans le même dossier du référentiel. La source est automatiquement compilée à la demande. Aucune étape de compilation ou de package distincte n’est requise.
* **Bundle** - dans le cas d’une installation groupée, la classe Java doit être compilée et déployée au sein d’un bundle OSGi par le biais du mécanisme de déploiement de bundle standard d’AEM (voir [Classe Java groupée](#bundled-java-class)).

Pour savoir quelle méthode utiliser et à quel moment, tenez compte des deux points suivants :

* Une **classe d’utilisation Java locale** est recommandée quand la classe d’utilisation est spécifique au composant en question.
* Une **classe d’utilisation Java groupée** est recommandée quand le code Java met en œuvre un service qui est utilisé par plusieurs composants HTL.

Cet exemple utilise une installation locale.

### Le package Java est le chemin d’accès du référentiel. {#java-package-is-repository-path}

Lors de l’utilisation d’une installation locale, le nom du package de la classe d’utilisation doit correspondre à l’emplacement du dossier du référentiel. Remplacez les tirets dans le chemin par des traits de soulignement dans le nom du module.

Dans ce cas, `Info.java` se trouve à l’adresse `/apps/my-example/components/info`. Par conséquent, le package est `apps.my_example.components.info` :

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {

   ...

}
```

>[!NOTE]
>
>L’utilisation de traits d’union dans les noms des éléments du référentiel est une pratique recommandée dans le développement AEM. Toutefois, les traits d’union sont des caractères interdits dans les noms de packages Java. C’est pourquoi **tous les tirets dans le chemin d’accès au référentiel doivent être convertis en traits de soulignement dans le nom du package**.

### Extension de `WCMUsePojo` {#extending-wcmusepojo}

Bien qu’il existe plusieurs façons d’intégrer une classe Java à HTL, la plus simple est d’étendre la classe `WCMUsePojo`. Pour cet exemple `/apps/my-example/component/info/Info.java` :

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo

    ...
}
```

### Initialiser la classe {#initializing-the-class}

Lorsque la classe d’utilisation est étendue depuis `WCMUsePojo`, l’initialisation est effectuée en remplaçant la méthode `activate`, dans ce cas dans `/apps/my-example/component/info/Info.java`.

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

La classe `WCMUsePojo` permet d’accéder au même ensemble d’objets de contexte qui est disponible dans un fichier HTL (voir le document [Objets globaux.](global-objects.md)).

Dans une classe étendue à `WCMUsePojo`, vous pouvez accéder aux objets de contexte à l’aide de leurs noms :

[`<T> T get(String name, Class<T> type)`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)

Vous pouvez également accéder directement aux objets de contexte fréquemment utilisés à l’aide de la méthode pratique appropriée répertoriée dans ce tableau.

| Objet | Méthode pratique |
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

Une fois la classe d’utilisation initialisée, le fichier HTL est exécuté. Au cours de cette étape, HTL extrait généralement l’état des différentes variables membres de la classe d’utilisation et les effectue pour présentation.

Pour permettre l’accès à ces valeurs à partir du fichier HTL, vous devez définir des méthodes getter personnalisées dans la classe d’utilisation, en respectant la convention de nommage suivante :

* Une méthode du formulaire `getXyz` expose dans le fichier HTL une propriété d’objet appelée `xyz`.

Dans l’exemple suivant `/apps/my-example/component/info/Info.java`, les méthodes `getTitle` et `getDescription` font en sorte que les propriétés d’objets `title` et `description` deviennent accessibles dans le contexte du fichier HTL.

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

### `data-sly-use` Attribut {#data-sly-use-attribute}

L’attribut `data-sly-use` est utilisé pour initialiser la classe d’utilisation dans votre code HTL. Dans cet exemple, l’attribut `data-sly-use` déclare que la classe `Info` est utilisée. Dans ce cas, vous pouvez utiliser uniquement le nom local de la classe, car vous utilisez une installation locale (après avoir placé le fichier source Java dans le même dossier que le fichier HTL). Si vous utilisez une installation par regroupement, vous devez spécifier le nom de classe complet.

Notez l’utilisation dans cet exemple : `/apps/my-example/component/info/info.html`.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Identifiant local {#local-identifier}

L’identificateur `info` (après le point dans `data-sly-use.info`) est utilisé dans le fichier HTL pour identifier la classe. La portée de cet identificateur est globale dans le fichier, une fois qu’il a été déclaré. Elle ne se limite pas à l’élément qui contient l’instruction `data-sly-use`. 

Notez l’utilisation dans cet exemple : `/apps/my-example/component/info/info.html`.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Obtenir des propriétés {#getting-properties}

L’identificateur `info` est alors utilisé pour accéder aux propriétés de l’objet `title` et `description`, qui ont été exposées par les méthodes getter `Info.getTitle` et `Info.getDescription`.

Notez l’utilisation dans cet exemple : `/apps/my-example/component/info/info.html`.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Sortie {#output}

Désormais, lors de l’accès à `/content/my-example.html`, il renvoie le fichier `/content/my-example.html` suivant.

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

>[!NOTE]
>
>Cet exemple a été simplifié pour illustrer son utilisation. Dans un environnement de production, Adobe vous recommande d’utiliser les [modèles Sling](https://sling.apache.org/documentation/bundles/models.html).

## Au-delà des concepts de base {#beyond-the-basics}

Cette section présente d’autres fonctionnalités qui vont au-delà de l’exemple simple décrit précédemment.

* Transmettre des paramètres à une classe d’utilisation
* Classe d’utilisation Java groupée

### Transmission des paramètres {#passing-parameters}

Les paramètres peuvent être transmis à un chemin de classe d’utilisation lors de l’initialisation.

Pour plus d’informations, veuillez vous reporter à la section [Documentation du moteur de script HTL Sling.](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#passing-parameters-to-java-use-objects)

### Classe Java groupée {#bundled-java-class}

Avec une classe d’utilisation groupée, la classe doit être compilée, mise en package et déployée dans AEM à l’aide du mécanisme de déploiement standard du bundle OSGi. Contrairement à une installation locale, la classe d’utilisation de déclaration du package doit normalement être nommée comme dans cet exemple : `/apps/my-example/component/info/Info.java`.

```java
package org.example.app.components;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    ...
}
```

Et l’instruction `data-sly-use` doit référencer le nom de classe entièrement qualifié, par opposition au simple nom de classe local comme dans cet exemple : `/apps/my-example/component/info/info.html`.

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```
