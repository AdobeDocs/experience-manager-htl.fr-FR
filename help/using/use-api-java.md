---
title: Use-API Java HTL
description: Use-API Java HTL permet à un fichier HTL d’accéder aux méthodes d’assistance dans une classe Java personnalisée.
exl-id: 9a9a2bf8-d178-4460-a3ec-cbefcfc09959
source-git-commit: 8e70ee4921a7ea071ab7e06947824c371f4013d8
workflow-type: ht
source-wordcount: '2558'
ht-degree: 100%

---

# HTL Java Use-API {#htl-java-use-api}

Le HTML Template Language (HTL) Java Use-API permet à un fichier HTL d’accéder aux méthodes d’assistance en classe Java personnalisée via `data-sly-use`. Cela permet à l’ensemble de la logique commerciale complexe d’être encapsulée dans le code Java tandis que le code HTL traite uniquement la production directe des balises.

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

Nous allons commencer avec un composant HTL qui n’a pas de classe d’utilisation. Il consiste en un seul fichier, `/apps/my-example/components/info.html`

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html}

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

Nous ajoutons également du contenu pour que ce composant effectue le rendu à `/content/my-example/` :

### `http://<host>:<port>/content/my-example.json` {#http-localhost-content-my-example-json}

```java
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

Lorsque l’on accède à ce contenu, le fichier HTL est exécuté. Dans le code HTL, nous utilisons l’objet contextuel `properties` pour accéder aux ressources actives `title` et `description` et les afficher. Le code HTML de sortie sera :

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
>Une classe d’utilisation ne devrait être utilisée que lorsque quelque chose ne peut pas être fait dans le HTL seul.

Par exemple, supposons que vous souhaitiez que le composant `info` affiche les propriétés `title` et `description` de la ressource, mais toutes en minuscules. Comme HTL ne dispose pas d’une méthode pour mettre en minuscules les chaînes, vous aurez besoin d’une classe d’utilisation. Nous pouvons le faire en ajoutant une classe d’utilisation Java et en modifiant le fichier `info.html` comme suit :

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

### Comparaison entre la classe Java locale et celle groupée {#local-vs-bundle-java-class}

La classe d’utilisation Java peut être installée de deux manières : **en local** ou **groupée**. Cet exemple utilise une installation locale.

Dans le cas d’une installation locale, le fichier source Java est placé à côté du fichier HTL, dans le même dossier de référentiel. Sur demande, la source est automatiquement compilée. Aucune étape de compilation ou d’assemblage distincte n’est requise.

Dans le cas d’une installation groupée, la classe Java doit être compilée et déployée au sein d’un groupe OSGi par le biais du mécanisme de déploiement groupé standard d’AEM (voir [Classe Java groupée](#bundled-java-class)).

>[!NOTE]
>
>Une **classe d’utilisation Java locale** est recommandée quand la classe d’utilisation est spécifique au composant en question.
>
>Une **classe d’utilisation Java groupée** est recommandée quand le code Java met en œuvre un service qui est utilisé par plusieurs composants HTL.

### Le pack Java est le chemin du référentiel. {#java-package-is-repository-path}

Lorsqu’une installation locale est utilisée, le nom du pack de la classe d’utilisation doit correspondre à l’emplacement du dossier du référentiel, les traits d’union du chemin étant remplacés par des traits de soulignement dans le nom du pack.

Dans ce cas, `Info.java` se trouve à l’adresse `/apps/my-example/components/info`. Par conséquent, le pack est `apps.my_example.components.info` :

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
>L’utilisation de traits d’union dans les noms des éléments du référentiel est une pratique recommandée dans le développement AEM. Toutefois, les traits d’union sont des caractères interdits dans les noms de packs Java. C’est pourquoi **tous les tirets dans le chemin d’accès au référentiel doivent être convertis en traits de soulignement dans le nom du package**.

### Extension   `WCMUsePojo` {#extending-wcmusepojo}

Bien qu’il existe plusieurs façons d’incorporer une classe Java à HTL (voir Alternatives à `WCMUsePojo`), la plus simple est d’étendre la classe `WCMUsePojo` :

#### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-2}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo

    ...
}
```

### Initialisation de la classe {#initializing-the-class}

Lorsque la classe d’utilisation est étendue depuis `WCMUsePojo`, l’initialisation est effectuée en remplaçant la méthode `activate` :

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

En règle générale, la méthode [activate](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) est utilisé pour précalculer et stocker (dans des variables membres) les valeurs nécessaires dans votre code HTL, en fonction du contexte actif (la requête et la ressource actives, par exemple).

La classe `WCMUsePojo` donne accès au même ensemble d’objets de contexte que ceux disponibles dans un fichier HTL (voir [Objets globaux](global-objects.md)).

Dans une classe qui étend `WCMUsePojo`, on peut accéder aux objets de contexte par leur nom en utilisant

[`<T> T get(String name, Class<T> type)`](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)

Alternativement, il est possible d’accéder aux objets de contexte couramment utilisés directement par la **méthode pratique** appropriée :

|  |  |
|---|---|
| [PageManager](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/PageManager.html) | [getPageManager()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageManager()) |
| [Page](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getCurrentPage()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentPage()) |
| [Page](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getResourcePage()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourcePage()) |
| [ValueMap](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getPageProperties()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageProperties()) |
| [ValueMap](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getProperties()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getProperties()) |
| [Designer](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [getDesigner()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getDesigner()) |
| [Conception](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Design.html) | [getCurrentDesign()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentDesign()) |
| [Style](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Style.html) | [getCurrentStyle()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentStyle()) |
| [Composant](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/components/Component.html) | [getComponent()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getComponent()) |
| [ValueMap](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getInheritedProperties()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getInheritedProperties) |
| [Ressource](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/Resource.html) | [getResource()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResource()) |
| [ResourceResolver](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [getResourceResolver()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourceResolver()) |
| [SlingHttpServletRequest](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [getRequest()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getRequest()) |
| [SlingHttpServletResponse](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [getResponse()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResponse()) |
| [SlingScriptHelper](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [getSlingScriptHelper()](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getSlingScriptHelper()) |

### Méthodes getter {#getter-methods}

Une fois que la classe d’utilisation est initialisée, le fichier HTL est exécuté. Au cours de cette étape, HTL récupère généralement l’état de plusieurs variables de membres de la classe de l’utilisation et les restitue pour la présentation.

Pour permettre l’accès à ces valeurs à partir du fichier HTL, vous devez définir des méthodes getter personnalisées dans la classe d’utilisation, en respectant la convention de nommage suivante :

* Une méthode de formulaire `getXyz` exposera dans le fichier HTL une propriété d’objet appelée `xyz`.

Dans l’exemple suivant, les méthodes `getTitle` et `getDescription` font en sorte que les propriétés de l’objet `title` et `description` deviennent accessibles dans le contexte du fichier HTL :

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

L’attribut `data-sly-use` est utilisé pour initialiser la classe d’utilisation dans votre code HTL. Dans notre exemple, l’attribut `data-sly-use` indique que nous voulons utiliser la classe `Info`. Nous pouvons utiliser uniquement le nom local de la classe, car nous utilisons une installation locale (le fichier source Java ayant été placé dans le même dossier que le fichier HTL). Si nous utilisions une installation groupée, nous devrions spécifier le nom de classe entièrement qualifié.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-2}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Identificateur local {#local-identifier}

L’identificateur `info` (après le point dans `data-sly-use.info`) est utilisé dans le fichier HTL pour identifier la classe. La portée de cet identificateur est globale dans le fichier, une fois qu’il a été déclaré. Elle ne se limite pas à l’élément qui contient l’instruction `data-sly-use`. 

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

Désormais, lorsque nous accédons à `/content/my-example.html`, le code HTML suivant est renvoyé :

### `view-source:http://<host>:<port>/content/my-example.html` {#view-source-http-localhost-content-my-example-html-1}

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

## Au-delà des concepts de base {#beyond-the-basics}

Dans cette section, nous allons présenter d’autres fonctionnalités qui vont au-delà du simple exemple ci-dessus :

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

Dans notre cas, l’instruction :

`get("text", String.class)`

La chaîne est alors inversée et exposée via la méthode :

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

Le modèle `extra` prend un seul paramètre, `text`. Il initialise ensuite la classe d’utilisation Java `ExtraHelper` avec le nom local `extraHelper` et lui transmet la valeur du paramètre du modèle `text` comme paramètre de la classe d’utilisation `text`.

Le corps du modèle obtient la propriété `extraHelper.reversedText` (qui, de son côté, appelle en fait `ExtraHelper.getReversedText()`) et affiche cette valeur.

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

Le fichier `info.html` contient désormais deux instructions `data-sly-use`, celle d’origine qui importe la classe d’utilisation Java `Info`, et une nouvelle qui importe le fichier du modèle sous le nom local `extra`.

Notez que nous aurions pu placer le bloc de modèle à l’intérieur du fichier `info.html` pour éviter la deuxième `data-sly-use`, mais un fichier de modèle distinct est plus courant et plus facile à réutiliser.

La classe `Info` est employée comme précédemment, en appelant ses méthodes getter `getLowerCaseTitle()` et `getLowerCaseDescription()` via leurs propriétés HTL correspondantes `info.lowerCaseTitle` et `info.lowerCaseDescription`.

Nous effectuons ensuite un `data-sly-call` au modèle `extra` et lui transmettons la valeur `properties.description` comme paramètre `text`.

La classe d’utilisation Java `Info.java` est modifiée pour gérer le nouveau paramètre texte :

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

Le paramètre `text` est récupéré avec `get("text", String.class)`, et la valeur est inversée et rendue disponible en tant qu’objet HTL `reversedText` par la méthode getter `getReversedText()`.

### Classe Java regroupée {#bundled-java-class}

Avec une classe d’utilisation groupée, la classe doit être compilée, mise en package et déployée dans AEM à l’aide du mécanisme de déploiement standard de regroupement OSGi. Contrairement à installation locale, la classe d’utilisation **de déclaration du package** doit être appelé normalement :

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-6}

```java
package org.example.app.components;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    ...
}
```

et l’instruction `data-sly-use` doit référencer le nom de classe entièrement qualifié, par opposition au simple nom de classe local :

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

**`<div data-sly-use.`** `localName`**`="`**`UseClass`**`">`**

Le système traite l’instruction comme suit :

(1)

* Si un fichier local `UseClass.java` existe dans le même répertoire que le fichier HTL, essayez de compiler et de charger cette classe. En cas de réussite, passez à (2).
* Sinon, interprétez `UseClass` comme un nom de classe entièrement qualifié et essayez de le charger depuis l’environnement OSGi. En cas de réussite, passez à (2).
* Sinon, interprétez `UseClass` comme un chemin d’accès vers un fichier HTL ou JavaScript et chargez ce fichier. En cas de réussite, accédez à la partie (4).

(2)

* Essayez d’adapter la `Resource` active à `UseClass`. En cas de réussite, passez à (3).
* Sinon, essayez d’adapter la `Request` active à `UseClass`. En cas de réussite, passez à (3).
* Sinon, essayez d’instancier `UseClass` avec un constructeur à zéro argument. En cas de réussite, accédez à la partie (3).

(3)

* Dans HTL, liez l’objet nouvellement adapté ou créé au nom `localName`.
* Si `UseClass` implémente [`io.sightly.java.api.Use`](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html), appelez ensuite la méthode `init`, en transmettant le contexte d’exécution actif (sous la forme d’un objet `javax.scripting.Bindings`).

(4)

* Si `UseClass` est un chemin d’accès vers un fichier HTL contenant un `data-sly-template`, préparez le modèle.
* Autrement, si `UseClass` est un chemin d’accès vers une classe d’utilisation JavaScript, préparez la classe d’utilisation (voir [Use-API JavaScript](use-api-javascript.md)).

Voici quelques points importants à propos de la description ci-dessus :

* Toute classe qui est adaptable à partir de `Resource` ou de `Request`, ou qui dispose d’un constructeur sans argument peut être une classe d’utilisation. La classe ne doit pas nécessairement étendre `WCMUsePojo` ni même mettre en œuvre `Use`.
* Cependant, si la classe d’utilisation *doit* implémenter `Use`, alors sa méthode `init` est automatiquement appelée avec le contexte actif, ce qui vous permet d’y placer le code d’initialisation qui dépend de ce contexte.
* Une classe d’utilisation qui étend `WCMUsePojo` est juste un cas spécial d’implémentation de `Use`. Elle fournit les méthodes de contexte pratiques et sa méthode `activate` est appelée automatiquement depuis `Use.init`.

### Implémentation directe de l’interface {#directly-implement-interface-use}

Bien que la façon la plus courante de créer une classe d’utilisation soit d’étendre `WCMUsePojo`, il est également possible d’implémenter directement l’interface [`io.sightly.java.api.Use`](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html) elle-même.

L’interface `Use` ne définit qu’une seule méthode :

[`public void init(javax.script.Bindings bindings)`](https://helpx.adobe.com/fr/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use#init(javax.script.Bindings))

La méthode `init` sera appelée lors de l’initialisation de la classe avec un objet `Bindings` qui contient tous les objets de contexte et tous les paramètres transmis dans la classe d’utilisation.

Toute fonctionnalité supplémentaire (comme l’équivalent de `WCMUsePojo.getProperties()`) doit être implémentée explicitement en utilisant l’objet [`javax.script.Bindings`](http://docs.oracle.com/javase/7/docs/api/javax/script/Bindings.html). Par exemple :

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

### Adaptable depuis la ressource {#adaptable-from-resource}

Une autre option consiste à utiliser une classe d’assistance qui est adaptable depuis `org.apache.sling.api.resource.Resource`.

Disons que vous devez écrire un script HTL qui affiche le mimetype d’une ressource dans la gestion des ressources numériques. Dans ce cas, vous savez que quand votre script HTL est appelé, il sera dans le contexte d’une `Resource` qui enveloppe un `Node` JCR de type `dam:Asset`.

Vous savez qu’un nœud `dam:Asset` a une structure comme celle-ci :

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

Nous montrons ici la ressource (une image JPEG) qui est fournie avec une installation par défaut d’AEM dans le cadre de l’exemple de projet Geometrixx. La ressource est appelée `jane_doe.jpg` et son mimetype est `image/jpeg`.

Pour accéder à la ressource à partir de HTL, vous pouvez déclarer [`com.day.cq.dam.api.Asset`](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/granite/asset/api/Asset.html) comme classe dans l’instruction `data-sly-use`, puis utiliser une méthode GET de `Asset` pour récupérer les informations souhaitées. Par exemple :

### `mimetype.html` {#mimetype-html}

```xml
<div data-sly-use.asset="com.day.cq.dam.api.Asset">
  <p>${asset.mimeType}</p>
</div>
```

L’instruction `data-sly-use` ordonne à HTL d’adapter la `Resource` active à une `Asset` et de lui donner le nom local `asset`. Elle appelle ensuite la méthode `getMimeType` de `Asset` en utilisant le raccourci getter de HTL : `asset.mimeType`.

### Adaptable à partir de la requête {#adaptable-from-request}

Il est également possible d’employer comme classe d’utilisation toute classe adaptable depuis [`org.apache.sling.api.SlingHttpServletRequest`](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html).

Comme dans le cas ci-dessus d’une classe d’utilisation adaptable depuis `Resource`, une classe d’utilisation adaptable depuis [`SlingHttpServletRequest`](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) peut être spécifiée dans l’instruction `data-sly-use`. Lors de l’exécution, la requête active sera adaptée à la classe donnée et l’objet résultant sera mis à disposition au sein de HTL.
