---
title: Use-API Java HTL
description: 'Use-API Java HTL permet à un fichier HTL d’accéder aux méthodes d’assistance dans une classe Java personnalisée. '
translation-type: tm+mt
source-git-commit: ee712ef61018b5e05ea052484e2a9a6b12e6c5c8
workflow-type: tm+mt
source-wordcount: '2558'
ht-degree: 83%

---


# Use-API Java HTL {#htl-java-use-api}

The HTML Template Language (HTL) Java Use-API enables an HTL file to access helper methods in a custom Java class through `data-sly-use`. Cela permet à l’ensemble de la logique métier complexe d’être encapsulée dans le code Java, tandis que le code HTL traite uniquement la production directe des balises.

Un objet Java Use-API peut être un POJO simple, instancié par une implémentation particulière via le constructeur par défaut du POJO.

Les POJO Use-API peuvent également exposer une méthode publique, appelée init, avec la signature suivante :

```java
    /**
     * Initializes the Use bean.
     *
     * @param bindings All bindings available to the HTL scripts.
     **/
    public void init(javax.script.Bindings bindings);
```

Le `bindings` mappage peut contenir des objets qui fournissent un contexte au script HTML actuellement exécuté que l’objet Use-API peut utiliser pour son traitement.

## Un exemple simple   {#a-simple-example}

Nous allons commencer avec un composant HTL qui n’a pas de classe d’utilisation. Il se compose d’un seul fichier, `/apps/my-example/components/info.html`

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html}

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

Nous avons également ajouté du contenu pour que le rendu de ce composant s’effectue à l’adresse `/content/my-example/` :

### `http://<host>:<port>/content/my-example.json` {#http-localhost-content-my-example-json}

```java
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

Lorsque ce contenu est consulté, le fichier HTL est exécuté. Within the HTL code we use the context object `properties` to access the current resource&#39;s `title` and `description` and display them. Le code HTML de sortie sera :

### `view-source:http://<host>:<port>/content/my-example.html` {#view-source-http-localhost-content-my-example-html}

```xml
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Ajout d’une classe d’utilisation {#adding-a-use-class}

Le composant **info** en tant que tel n’a pas besoin d’une classe d’utilisation pour remplir sa fonction (très simple). Cependant, dans certains cas, vous devez effectuer des opérations qui ne peuvent pas être effectuées dans HTL, et vous avez donc besoin d’une classe d’utilisation. Cependant, gardez à l’esprit les considérations suivantes :

>[!NOTE]
>
>Une classe d’utilisation doit être utilisée uniquement lorsqu’une opération ne peut pas être effectuée uniquement dans HTL.

Par exemple, supposons que vous souhaitiez que le composant `info` affiche les propriétés `title` et `description` de la ressource, mais entièrement en minuscules. Comme HTL ne possède pas de méthode pour passer des chaînes en minuscules, vous aurez besoin d’une classe d’utilisation. Nous pouvons effectuer cela en ajoutant une classe d’utilisation Java et en modifiant le fichier `info.html` comme suit :

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-1}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java}

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

Dans les sections suivantes, nous examinons les différentes parties du code.

### Local vs. Bundle Java Class {#local-vs-bundle-java-class}

La classe d’utilisation Java peut être installée de deux manières : **en local** ou **groupée**.  Cet exemple utilise une installation locale.

Dans le cas d’une installation locale, le fichier source Java est placé à côté du fichier HTL, dans le même dossier de référentiel. Sur demande, la source est automatiquement compilée. Aucune étape de compilation ou d’assemblage distincte n’est requise.

Dans le cas d’une installation groupée, la classe Java doit être compilée et déployée au sein d’un groupe OSGi par le biais du mécanisme de déploiement groupé standard d’AEM (voir [Classe Java groupée](#bundled-java-class)).

>[!NOTE]
>
>Une **classe d’utilisation Java locale** est recommandée quand la classe d’utilisation est spécifique au composant en question.
>
>Une **classe d’utilisation Java groupée** est recommandée quand le code Java met en œuvre un service qui est utilisé par plusieurs composants HTL.

### Le package Java est le chemin du référentiel {#java-package-is-repository-path}

Lorsque vous utilisez une installation locale, le nom du package de la classe d’utilisation doit correspondre à l’emplacement du dossier de référentiel, avec tous les tirets dans le chemin remplacés par des traits de soulignement dans le nom du package.

Dans ce cas, `Info.java` se trouve à l’adresse `/apps/my-example/components/info`. Par conséquent, le package est `apps.my_example.components.info` :

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-1}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {

   ...

}
```

>[!NOTE]
>
>Il est recommandé d’utiliser des tirets dans les noms des éléments du référentiel lors du développement d’AEM. Toutefois, les tirets sont des caractères interdits dans les noms de package Java. C’est pourquoi **tous les tirets dans le chemin d’accès au référentiel doivent être convertis en traits de soulignement dans le nom du package**.

### Extension `WCMUsePojo` {#extending-wcmusepojo}

Même s’il existe plusieurs façons d’incorporer une classe Java avec HTL (voir les alternatives à `WCMUsePojo`), le plus simple est d’étendre la classe `WCMUsePojo` :

#### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-2}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo

    ...
}
```

### Initialisation de la classe {#initializing-the-class}

When the use-class is extended from `WCMUsePojo`, initialization is performed by overriding the `activate` method:

### /apps/my-example/component/info/Info.java {#apps-my-example-component-info-info-java-3}

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

En règle générale, la méthode [activate](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) est utilisé pour précalculer et stocker (dans les variables de membres) les valeurs nécessaires dans votre code HTL, en fonction du contexte actif (la requête et la ressource actives, par exemple).

La classe `WCMUsePojo` permet d’accéder au même ensemble d’objets de contexte qui est disponible dans un fichier HTL (voir [Objets globaux](global-objects.md)).

Dans une classe qui étend `WCMUsePojo`, les objets de contexte peuvent être consultés par nom en utilisant

[`<T> T get(String name, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)

Vous pouvez également accéder directement aux objets de contexte fréquemment utilisés par la **méthode pratique** appropriée :

|  |  |
|---|---|
| [PageManager](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/PageManager.html) | [getPageManager()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageManager()) |
| [Page](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getCurrentPage()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentPage()) |
| [Page](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getResourcePage()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourcePage()) |
| [ValueMap](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getPageProperties()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageProperties()) |
| [ValueMap](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getProperties()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getProperties()) |
| [Designer](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [getDesigner()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getDesigner()) |
| [Concevoir](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Design.html) | [getCurrentDesign()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentDesign()) |
| [Style](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Style.html) | [getCurrentStyle()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentStyle()) |
| [Composant](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/components/Component.html) | [getComponent()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getComponent()) |
| [ValueMap](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getInheritedProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getInheritedProperties) |
| [Ressource](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/Resource.html) | [getResource()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResource()) |
| [ResourceResolver](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [getResourceResolver()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourceResolver()) |
| [SlingHttpServletRequest](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [getRequest()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getRequest()) |
| [SlingHttpServletResponse](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [getResponse()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResponse()) |
| [SlingScriptHelper](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [getSlingScriptHelper()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getSlingScriptHelper()) |

### Méthodes getter {#getter-methods}

Une fois que la classe d’utilisation est initialisée, le fichier HTL est exécuté. Au cours de cette étape, HTL récupère généralement l’état de plusieurs variables de membres de la classe de l’utilisation et les restitue pour la présentation.

Pour permettre l’accès à ces valeurs à partir du fichier HTL, vous devez définir des méthodes getter personnalisées dans la classe d’utilisation, en respectant la convention de dénomination suivante :

* Une méthode de formulaire `getXyz` exposera une propriété d’objet appelée `xyz` dans le fichier HTL.

In the following example, the methods `getTitle` and `getDescription` result in the object properties `title` and `description` becoming accessible within the context of the HTL file:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-4}

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

### attribut data-sly-use {#data-sly-use-attribute}

L’attribut `data-sly-use` est utilisé pour initialiser la classe d’utilisation dans votre code HTL. Dans notre exemple, l’attribut `data-sly-use` indique que nous voulons utiliser la classe `Info`. Nous pouvons utiliser seulement le nom local de la classe, parce que nous utilisons une installation locale (le fichier source Java ayant été placé dans le même dossier que le fichier HTL). Si nous utilisions une installation groupée, nous devrions spécifier le nom de classe complet.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-2}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Identifiant local {#local-identifier}

The identifier `info` (after the dot in `data-sly-use.info`) is used within the HTL file to identify the class. La portée de cet identificateur est globale dans le fichier, une fois qu’il a été déclaré. Elle ne se limite pas à l’élément qui contient l’instruction `data-sly-use`. 

### `/apps/my-example/component/info/info.html`{#apps-my-example-component-info-info-html-3}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Obtention des propriétés {#getting-properties}

L’identificateur `info` est alors utilisé pour accéder aux propriétés de l’objet `title` et `description`, qui ont été exposées par les méthodes getter `Info.getTitle` et `Info.getDescription`.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-4}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Sortie {#output}

Désormais, lorsque nous accédons à `/content/my-example.html`, elle renvoie le code HTML suivant :

### `view-source:http://<host>:<port>/content/my-example.html` {#view-source-http-localhost-content-my-example-html-1}

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

## Au-delà des concepts de base {#beyond-the-basics}

Dans cette section, nous présenterons d&#39;autres fonctionnalités qui vont au-delà de l&#39;exemple simple ci-dessus :

* Transmission de paramètres à une classe d’utilisation
* Classe d’utilisation Java groupée
* Alternatives à `WCMUsePojo`

### Transmission des paramètres {#passing-parameters}

Les paramètres peuvent être transmis à un chemin de classe d’utilisation lors de l’initialisation. Par exemple, nous pouvons effectuer ce qui suit :

### `/content/my-example/component/info/info.html` {#content-my-example-component-info-info-html}

```xml
<div data-sly-use.info="${'Info' @ text='Some text'}">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
    <p>${info.upperCaseText}</p>
</div>
```

Dans ce cas, nous transmettons un paramètre appelé `text`. La classe d’utilisation met la chaîne que nous récupérons en majuscules et affiche le résultat avec `info.upperCaseText`. Voici la classe d’utilisation ajustée :

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-5}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {

    ...

    private String reverseText;

    @Override
    public void activate() throws Exception {

        ...

        String text = get("text", String.class);
        reverseText = new StringBuffer(text).reverse().toString();

    }

    public String getReverseText() {
        return reverseText;
    }

    ...
}
```

Le paramètre est accessible via la méthode `WCMUsePojo` [`<T> T get(String paramName, Class<T> type)`](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

Dans notre cas, l’instruction:

`get("text", String.class)`

La chaîne est alors inversée et exposée par le biais de la méthode:

`getReverseText()`

### Ne transmettez des paramètres que depuis data-sly-template {#only-pass-parameters-from-data-sly-template}

Bien que l’exemple ci-dessus soit techniquement correct, il n’est pas très judicieux de transmettre une valeur depuis HTL pour initialiser une classe d’utilisation, lorsque la valeur en question est disponible dans le contexte d’exécution du code HTL (ou, trivialement, la valeur est statique, comme ci-dessus).

C’est parce que la classe d’utilisation aura toujours accès au même contexte d’exécution que le code HTL. Cela entraîne une ’importation recommandée :

>[!NOTE]
>
>La transmission d’un paramètre à une classe d’utilisation ne doit être effectuée que lorsque cette dernière est utilisée dans un fichier `data-sly-template`, qui est lui-même appelé à partir d’un autre fichier HTL avec les paramètres qui doivent être transmis.

Par exemple, créons un fichier `data-sly-template` distinct avec notre exemple existant. Nous appellerons le nouveau fichier `extra.html`. Il contient un bloc `data-sly-template` appelé `extra` :

### `/apps/my-example/component/info/extra.html` {#apps-my-example-component-info-extra-html}

```xml
<template data-sly-template.extra="${@ text}"
          data-sly-use.extraHelper="${'ExtraHelper' @ text=text}">
  <p>${extraHelper.reversedText}</p>
</template>
```

Le modèle `extra` prend un seul paramètre, `text`. Il initialise ensuite la classe d’utilisation Java `ExtraHelper``extraHelper` avec le nom local  et lui transmet la valeur du paramètre de modèle `text` en tant que paramètre de classe d’utilisation `text`.

Le corps du modèle récupère la propriété `extraHelper.reversedText` (qui, de son côté, appelle en fait `ExtraHelper.getReversedText()`) et affiche cette valeur.

Nous adaptons également notre fichier `info.html` existant pour utiliser ce nouveau modèle :

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-5}

```xml
<div data-sly-use.info="Info"
     data-sly-use.extra="extra.html">

  <h1>${info.lowerCaseTitle}</h1>
  <p>${info.lowerCaseDescription}</p>

  <div data-sly-call="${extra.extra @ text=properties.description}"></div>

</div>
```

Le fichier `info.html` contient désormais deux instructions `data-sly-use`, celle d’origine qui importe la classe d’utilisation Java `Info`, et une nouvelle qui importe le fichier de modèle sous le nom local `extra`.

Notez que nous aurions pu placer le bloc de modèle à l’intérieur du fichier `info.html` pour éviter le deuxième `data-sly-use`, mais un fichier de modèle distinct est plus courant et plus facile à réutiliser.

La classe `Info` est utilisée comme avant, en appelant ses méthodes getter `getLowerCaseTitle()` et `getLowerCaseDescription()` via leurs propriétés HTL correspondantes `info.lowerCaseTitle` et `info.lowerCaseDescription`.

Nous effectuons ensuite un `data-sly-call` sur le modèle `extra` et lui transmettons la valeur `properties.description` en tant que paramètre `text`.

La classe d’utilisation Java `Info.java` est modifiée pour gérer le nouveau paramètre de texte :

### `/apps/my-example/component/info/ExtraHelper.java` {#apps-my-example-component-info-extrahelper-java}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class ExtraHelper extends WCMUsePojo {
    private String reversedText;
    ...

    @Override
    public void activate() throws Exception {
        String text = get("text", String.class);
        reversedText = new StringBuilder(text).reverse().toString();

        ...
    }

    public String getReversedText() {
        return reversedText;
    }
}
```

Le paramètre `text` est récupéré avec `get("text", String.class)`, et la valeur est inversée et rendue disponible en tant qu’objet HTL `reversedText` par le getter `getReversedText()`.

### Classe Java groupée {#bundled-java-class}

Avec une classe d’utilisation groupée, la classe doit être compilée, mise en package et déployée dans AEM à l’aide du mécanisme de déploiement standard de regroupement OSGi. Contrairement à installation locale, la classe d’utilisation **de déclaration du package** doit être appelé normalement :

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-6}

```java
package org.example.app.components;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    ...
}
```

et l’instruction `data-sly-use` doit référencer le nom de classe entièrement qualifié, et non pas simplement le nom de classe local :

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-6}

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```

### Alternatives à `WCMUsePojo` {#alternatives-to-wcmusepojo}

La méthode la plus fréquente pour créer une classe d’utilisation Java consiste à étendre `WCMUsePojo`. Cependant, il existe un certain nombre d’options supplémentaires. Pour comprendre ces variantes, il est utile de comprendre comment l’instruction HTL `data-sly-use` s’exécute en arrière-plan.

Supposons que vous ayez l’instruction `data-sly-use` suivante :

**`<div data-sly-use.`** `localName`**`="`** `UseClass`**`">`**

Le système traite l’instruction comme suit :

(1)

* Si un fichier local `UseClass.java` existe dans le même répertoire que le fichier HTL, essayez de compiler et de charger cette classe. En cas de réussite, accédez à la partie (2).
* Autrement, interprétez `UseClass` comme nom de classe entièrement qualifié et essayez de le charger à partir de l’environnement OSGi. En cas de réussite, accédez à la partie (2).
* Autrement, interprétez `UseClass` en tant que chemin d’accès vers un fichier HTL ou JavaScript et chargez ce fichier. En cas de réussite, accédez à la partie (4).

(2)

* Try to adapt the current `Resource` to `UseClass`. If successful, go to (3).
* Autrement, essayez d’adapter la `Request` active à `UseClass`. En cas de réussite, accédez à la partie (3).
* Autrement, essayez d’instancier `UseClass` avec un constructeur sans argument. En cas de réussite, accédez à la partie (3).

(3)

* Dans HTL, liez l’objet nouvellement adapté ou créé au nom `localName`.
* If `UseClass` implements [`io.sightly.java.api.Use`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html) then call the `init` method, passing the current execution context (in the form of a `javax.scripting.Bindings` object).

(4)

* Si `UseClass` est un chemin d’accès à un fichier HTL contenant un `data-sly-template`, préparez le modèle.
* Autrement, si `UseClass` est un chemin d’accès à une classe d’utilisation JavaScript, préparez la classe d’utilisation (voir [Use-API JavaScript](use-api-javascript.md)). 

Voici quelques points importants à propos de la description ci-dessus :

* Toute classe qui est adaptable à partir de `Resource` ou de `Request`, ou qui dispose d’un constructeur sans argument peut être une classe d’utilisation. La classe ne doit pas nécessairement étendre `WCMUsePojo` ni même mettre en œuvre `Use`.
* Cependant, si la classe d’utilisation *met vraiment en œuvre*`Use`, sa méthode `init` est automatiquement appelée avec le contexte actif, ce qui vous permet d’y placer le code d’initialisation qui dépend de ce contexte.
* Une classe d’utilisation qui étend `WCMUsePojo` est seulement un cas spécial de mise en œuvre de `Use`. Elle fournit les méthodes de contexte pratiques et sa méthode `activate` est appelée automatiquement à partir de `Use.init`.

### Mise en œuvre directe de l’utilisation de l’interface {#directly-implement-interface-use}

Bien que la méthode la plus simple pour créer une classe d’utilisation consiste à étendre `WCMUsePojo`, il est également possible de mettre en œuvre directement l’interface [`io.sightly.java.api.Use`elle même.](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html)

L’interface`Use` ne définit qu’une seule méthode :

[`public void init(javax.script.Bindings bindings)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use#init(javax.script.Bindings))

La méthode `init` sera appelée lors de l’initialisation de la classe avec un objet `Bindings` qui contient tous les objets de contexte et tous les paramètres transmis dans la classe d’utilisation.

All additional functionality (such as the equivalent of `WCMUsePojo.getProperties()`) must be implemented explicitly using the [`javax.script.Bindings`](http://docs.oracle.com/javase/7/docs/api/javax/script/Bindings.html) object. Par exemple :

### `Info.java` {#info-java}

```java
import io.sightly.java.api.Use;

public class MyComponent implements Use {
   ...
    @Override
    public void init(Bindings bindings) {

        // All standard objects/binding are available
        Resource resource = (Resource)bindings.get("resource");
        ValueMap properties = (ValueMap)bindings.get("properties");
        ...

        // Parameters passed to the use-class are also available
        String param1 = (String) bindings.get("param1");
    }
    ...
}
```

La principale raison pour laquelle vous devriez mettre en œuvre l’interface `Use` par vous-même au lieu d’étendre `WCMUsePojo` est si vous souhaitez utiliser une sous-classe d’une classe existante en tant que classe d’utilisation.

### Adaptable à partir de la ressource {#adaptable-from-resource}

Une autre option consiste à utiliser une classe d’assistance qui est adaptable à partir de `org.apache.sling.api.resource.Resource`.

Supposons que vous ayez besoin d’écrire un script HTL qui affiche le mimetype d’une ressource DAM. Dans ce cas, vous savez que quand votre script HTL est appelé, ce sera dans le contexte d’une `Resource` qui enveloppe un `Node` JCR de type `dam:Asset`.

Vous savez qu’un nœud `dam:Asset` possède le type de structure suivant :

### Structure du référentiel {#repository-structure}

```java
{
  "content": {
    "dam": {
      "geometrixx": {
        "portraits": {
          "jane_doe.jpg": {
            ...
          "jcr:content": {
            ...
            "metadata": {
              ...
            },
            "renditions": {
              ...
              "original": {
                ...
                "jcr:content": {
                  "jcr:primaryType": "nt:resource",
                  "jcr:lastModifiedBy": "admin",
                  "jcr:mimeType": "image/jpeg",
                  "jcr:lastModified": "Fri Jun 13 2014 15:27:39 GMT+0200",
                  "jcr:data": ...,
                  "jcr:uuid": "22e3c598-4fa8-4c5d-8b47-8aecfb5de399"
                }
              },
              "cq5dam.thumbnail.319.319.png": {
                  ...
              },
              "cq5dam.thumbnail.48.48.png": {
                  ...
              },
              "cq5dam.thumbnail.140.100.png": {
                  ...
              }
            }
          }  
        }
      }
    }
  }
}
```

Nous montrons ici la ressource (une image JPEG) qui est fournie avec une installation par défaut d’AEM en tant qu’exemple de projet geometrixx. La ressource est appelée `jane_doe.jpg` et son mimetype est `image/jpeg`.

To access the asset from within HTL, you can declare [`com.day.cq.dam.api.Asset`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/granite/asset/api/Asset.html) as the class in the `data-sly-use` statement and then use a get method of `Asset` to retrieve the desired information. Par exemple :

### `mimetype.html` {#mimetype-html}

```xml
<div data-sly-use.asset="com.day.cq.dam.api.Asset">
  <p>${asset.mimeType}</p>
</div>
```

L’instruction `data-sly-use` demande à HTL d’adapter la `Resource` active à un `Asset` et de lui donner le nom local `asset`. Il appelle ensuite la méthode `getMimeType` de `Asset` à l’aide du raccourci getter HTL : `asset.mimeType`.

### Adaptable à partir de la requête {#adaptable-from-request}

It is also possible to employ as a use-class any class that is adaptable from [`org.apache.sling.api.SlingHttpServletRequest`](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html)

As with the above case of a use-class adaptable from `Resource`, a use-class adaptable from [`SlingHttpServletRequest`](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) can be specified in the `data-sly-use` statement. Lors de l’exécution, la requête active sera adaptée à la classe donnée et l’objet produit sera rendu disponible dans HTL.
