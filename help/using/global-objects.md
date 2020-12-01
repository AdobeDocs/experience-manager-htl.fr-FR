---
title: Objets globaux HTL
description: Sans rien avoir à spécifier, HTL permet d’accéder à tous les objets qui étaient couramment disponibles en JSP après avoir inclus global.jsp.
translation-type: tm+mt
source-git-commit: ee712ef61018b5e05ea052484e2a9a6b12e6c5c8
workflow-type: tm+mt
source-wordcount: '220'
ht-degree: 86%

---


# Objets globaux HTL {#htl-global-objects}

Sans rien avoir à spécifier, HTL permet d’accéder à tous les objets qui étaient couramment disponibles en JSP après avoir inclus `global.jsp`. Ces objets s’ajoutent à tout autre objet qui a pu être intégré avec [Use-API](use-api.md).

## Objets énumérables {#enumerable-objects}

Ces objets vous permettent d’accéder facilement aux informations fréquemment utilisées. Vous pouvez accéder à leur contenu avec la notation de point et procéder à leur itération à l’aide de `data-sly-list` ou `data-sly-repeat`.

| Nom de variable | Description |
|--- |--- |
| `properties` | Liste des propriétés de la ressource active. Soutenu par [org.apache.sling.api.resource.ValueMap](https://helpx.adobe.com/fr/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `pageProperties` | Liste des propriétés de la page active. Soutenu par [org.apache.sling.api.resource.ValueMap](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `inheritedPageProperties` | Liste des propriétés héritées de la page active. Soutenu par [org.apache.sling.api.resource.ValueMap](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) |

## Objets pris en charge par l’environnement Java {#java-backed-objects}

Les objets suivants sont pris en charge par l’objet Java correspondant.

Les variables les plus utiles du tableau ci-dessous sont mises en gras.

| Nom de variable | Description |
|---|---|
| **`component`** | `com.day.cq.wcm.api.components.Component` |
| `componentContext` | `com.day.cq.wcm.api.components.ComponentContext` |
| **`currentDesign`** | `com.day.cq.wcm.api.designer.Design` |
| `currentNode` | `javax.jcr.Node` |
| **`currentPage`** | `com.day.cq.wcm.api.Page` |
| `currentSession` | `javax.servlet.http.HttpSession` |
| `currentStyle` | `com.day.cq.wcm.api.designer.Style` |
| `designer` | `com.day.cq.wcm.api.designer.Designer` |
| `editContext` | `com.day.cq.wcm.api.components.EditContext` |
| `log` | `org.slf4j.Logger` |
| `out` | `java.io.PrintWriter` |
| `pageManager` | `com.day.cq.wcm.api.PageManager` |
| `reader` | `java.io.BufferedReader` |
| **`request`** | `org.apache.sling.api.SlingHttpServletRequest` |
| `resolver` | `org.apache.sling.api.resource.ResourceResolver` |
| **`resource`** | `org.apache.sling.api.resource.Resource` |
| **`resourceDesign`** | `com.day.cq.wcm.api.designer.Design` |
| **`resourcePage`** | `com.day.cq.wcm.api.Page` |
| `response` | `org.apache.sling.api.SlingHttpServletResponse` |
| `sling` | `org.apache.sling.api.scripting.SlingScriptHelper` |
| `slyWcmHelper` | `com.adobe.cq.sightly.WCMScriptHelper` |
| **`wcmmode`** | `com.adobe.cq.sightly.SightlyWCMMode` |
| `xssAPI` | `com.adobe.granite.xss.XSSAPI` |

## Objets pris en charge par l’environnement JavaScript {#javascript-backed-objects}

Il est possible de sauvegarder la logique HTML avec JavaScript. Cependant, la méthode préférée ou recommandée est l&#39;utilisation de [modèles Sling.](https://sling.apache.org/documentation/bundles/models.html)

<!-- 

Comment Type: draft

<p> </p> 
<p>JS-specific context variables: These supply access to asynchronous implementations of all the Java objects listed below). To write HTL code that is portable to granite.js, you must use the variables provided by aem and sly, not the native Java variables.</p> 
<ul> 
 <li>wcm
  <ul> 
   <li>currentPage</li> 
   <li>nativePage: [com.day.cq.wcm.apiPage]</li> 
   <li>properties: {<i>enumerable</i>}</li> 
  </ul> </li> 
 <li>granite
  <ul> 
   <li>request
    <ul> 
     <li>parameters: {<i>enumerable</i>}</li> 
     <li>nativeRequest: [org.apache.sling.scripting.core.impl.helper.OnDemandReaderRequest]</li> 
     <li>pathInfo
      <ul> 
       <li>nativePathInfo: [SlingRequestPathInfo: path='/content/geometrixx/en/jcr:content/par/text', selectorString='null', extension='html', suffix='null']</li> 
      </ul> </li> 
    </ul> </li> 
   <li>resource
    <ul> 
     <li>nativeResource: [Paragraph, path=/content/geometrixx/en/jcr:content/par/text, type=wcm/foundation/components/text, cssClass=default, column=0/0, diffInfo=[null], resource=[JcrNodeResource, type=wcm/foundation/components/text, superType=null, path=/content/geometrixx/en/jcr:content/par/text]]</li> 
     <li>path: "/content/geometrixx/en/jcr:content/par/text"</li> 
     <li>properties: {sling:resourceType,jcr:created,jcr:lastModified,jcr:createdBy, textIsRich,jcr:lastModifiedBy,jcr:primaryType}</li> 
    </ul> </li> 
   <li>properties: {sling:resourceType,jcr:created,jcr:lastModified,jcr:createdBy, textIsRich,jcr:lastModifiedBy,jcr:primaryType}</li> 
  </ul> </li> 
</ul> 
<p>JS specific non-HTL related variables. Present due to JS-implementation. Generally not used in templating:</p> 
<ul> 
 <li>console: JS Object</li> 
 <li>exports: JS Object</li> 
 <li>module: JS Object</li> 
 <li>setImmediate: JS Function</li> 
 <li>setTimeout: JS Function</li> 
 <li>use: JS Function</li> 
</ul>
-->
