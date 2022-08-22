---
title: Extensions d’AEM
description: AEM propose des extensions de spécification HTL à AEM à des fins pratiques en tant que développeur.
source-git-commit: 6d97bc5d0ab89dffaf56a54c73c94d069bb31ca6
workflow-type: tm+mt
source-wordcount: '308'
ht-degree: 0%

---


# Extensions d’AEM {#aem-extensions}

Semblable au [Extensions Apache Sling de la spécification HTL,](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#extensions-of-the-htl-specification-1) AEM propose des options d’expression supplémentaires qui facilitent l’utilisation des concepts AEM directement dans les scripts HTL.

## i18n {#i18n}

Identique [trois options supplémentaires](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#i18n) comme dans Apache Sling peut être utilisé avec `i18n`:

* `locale`
* `hint`
* `basename`

Toutefois, dans AEM, la variable [prise en charge de l’internationalisation](https://experienceleague.adobe.com/docs/experience-manager-65/developing/components/internationalization/i18n-dev.html) pour HTL est implémenté à l’aide de l’API de la fonction `com.day.cq.i18n` module.

## data-sly-include {#data-sly-include}

Dans AEM, `data-sly-include` peut prendre la valeur `wcmmode` qui contrôlent la variable [Mode WCM](https://developer.adobe.com/experience-manager/reference-materials/cloud-service/javadoc/com/day/cq/wcm/api/WCMMode.html) pour le script inclus. Les valeurs autorisées sont les noms des constantes d’énumération disponibles.

## data-sly-resource {#data-sly-resource}

En plus des chemins et `Resources`, la variable `data-sly-resource` L’élément block peut également fonctionner avec [`Maps`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Map.html) ou [`Records`.](https://github.com/apache/sling-org-apache-sling-scripting-sightly-runtime/blob/master/src/main/java/org/apache/sling/scripting/sightly/Record.java) Avec les deux approches, `resourceName` La propriété String doit être fournie. Sa valeur est utilisée pour créer une [Ressource synchrone](https://www.javadoc.io/doc/org.apache.sling/org.apache.sling.api/latest/org/apache/sling/api/resource/SyntheticResource.html) qui sera inclus dans le contexte du rendu. Le reste des propriétés de la variable `Record` ou le `Map` qui a été transmis à `data-sly-resource` sera utilisé normalement. `Resource` propriétés. Si la variable `sling:resourceType` est absente de cette map, le type de ressource sera supposé correspondre à la valeur de la propriété `resourceType` [option d&#39;expression](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#229-resource) ou le type de ressource de la ressource active qui oriente le rendu.

Compte tenu des propriétés de mappage/enregistrement suivantes disponibles dans la portée du script comme `map`:

```javascript
{
    resourceName: "myText",
    "sling:resourceType": "core/wcm/components/text/v2/text",
    "text": "Hello World!"
}
```

Et selon les balises suivantes :

```html
<div class="outer" data-sly-resource="${map}"></div>
```

La sortie suivante est attendue :

```html
<div class="outer">
    <div class="myText">
        <div data-cmp-data-layer="{&quot;text-e58d65c472&quot;:{&quot;@type&quot;:&quot;core/wcm/components/text/v2/text&quot;,&quot;xdm:text&quot;:&quot;<p>Hello world!</p>&quot;}}" id="text-e58d65c472" class="cmp-text">
            <p>Hello world!</p>
        </div>
  </div>
</div>
```
