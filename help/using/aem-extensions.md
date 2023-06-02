---
title: Extensions AEM
description: AEM propose des extensions de spécification HTL spécifiques à AEM pour votre commodité en tant que développeur.
exl-id: d78cb84d-f958-45e2-9c6c-df86a68277d5
source-git-commit: 88edbd2fd66de960460df5928a3b42846d32066b
workflow-type: tm+mt
source-wordcount: '308'
ht-degree: 100%

---

# Extensions AEM {#aem-extensions}

Tout comme les [extensions Apache Sling de la spécification HTL](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#extensions-of-the-htl-specification-1), AEM propose des options d’expression supplémentaires qui facilitent l’utilisation des concepts AEM directement dans les scripts HTL.

## i18n {#i18n}

Il est possible d’utiliser les [trois mêmes options supplémentaires](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#i18n) que dans Apache Sling avec `i18n` :

* `locale`
* `hint`
* `basename`

Toutefois, dans AEM, la [prise en charge de l’internationalisation](https://experienceleague.adobe.com/docs/experience-manager-65/developing/components/internationalization/i18n-dev.html?lang=fr) de HTL est implémentée à l’aide de l’API du package `com.day.cq.i18n`.

## data-sly-include {#data-sly-include}

Dans AEM, `data-sly-include` peut prendre en charge une option `wcmmode` supplémentaire qui contrôle le [mode de gestion de contenu web](https://developer.adobe.com/experience-manager/reference-materials/cloud-service/javadoc/com/day/cq/wcm/api/WCMMode.html) pour le script inclus. Les valeurs autorisées sont les noms des constantes d’énumération disponibles.

## data-sly-resource {#data-sly-resource}

En plus des chemins d’accès et des `Resources`, l’élément de bloc `data-sly-resource` peut également fonctionner avec [`Maps`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Map.html) ou [`Records`.](https://github.com/apache/sling-org-apache-sling-scripting-sightly-runtime/blob/master/src/main/java/org/apache/sling/scripting/sightly/Record.java) Ces deux approches nécessitent de fournir la propriété de chaîne `resourceName`. Sa valeur est utilisée pour créer une [ressource synthétique](https://www.javadoc.io/doc/org.apache.sling/org.apache.sling.api/latest/org/apache/sling/api/resource/SyntheticResource.html) qui sera incluse dans le contexte du rendu. Le reste des propriétés de `Record` ou de `Map` qui ont été transmises à `data-sly-resource` seront utilisées comme des propriétés `Resource` normales. Si la propriété `sling:resourceType` est absente de ce mappage, on suppose que le type de ressource correspond soit à la valeur de l’[option d’expression](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#229-resource) `resourceType`, soit au type de ressource de la ressource active qui oriente le rendu.

Compte tenu des propriétés de mappage/enregistrement suivantes disponibles dans la portée du script comme `map` :

```javascript
{
    resourceName: "myText",
    "sling:resourceType": "core/wcm/components/text/v2/text",
    "text": "Hello World!"
}
```

Et compte tenu de la balise suivante :

```html
<div class="outer" data-sly-resource="${map}"></div>
```

La sortie suivante est attendue :

```html
<div class="outer">
    <div class="myText">
        <div data-cmp-data-layer="{&quot;text-e58d65c472&quot;:{&quot;@type&quot;:&quot;core/wcm/components/text/v2/text&quot;,&quot;xdm:text&quot;:&quot;<p>Hello world!</p>&quot;}}" id="text-e58d65c472" class="cmp-text">
            <p>Hello world!</p>
        </div>
  </div>
</div>
```
