---
title: Objets globaux HTL
description: Découvrez les objets énumérables, pris en charge par Java et pris en charge par JavaScript dans HTL.
exl-id: ca590b92-f1b3-4e44-a04a-a2c10dff256f
source-git-commit: c6bb6f0954ada866cec574d480b6ea5ac0b51a3f
workflow-type: tm+mt
source-wordcount: '164'
ht-degree: 87%

---


# Objets globaux HTL {#htl-global-objects}

Sans rien avoir à spécifier, HTL permet d’accéder à de nombreux objets utiles au développeur. Ces objets s’ajoutent à tout autre objet qui a pu être intégré avec [Use-API](java-use-api.md).

>[!NOTE]
>
>Pour les développeurs habitués au développement JSP dans AEM, HTL permet d’accéder à tous les objets qui étaient couramment disponibles dans JSP après avoir inclus `global.jsp`.

## Objets énumérables {#enumerable-objects}

Ces objets vous permettent d’accéder facilement aux informations fréquemment utilisées. Vous pouvez accéder à leur contenu avec la notation de point et procéder à leur itération à l’aide de `data-sly-list` ou `data-sly-repeat`.

| Nom de variable | Description | Pris en charge par |
|--- |--- |--- |
| `properties` | Liste des propriétés de la ressource activ | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `pageProperties` | Liste des propriétés de la page active | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `inheritedPageProperties` | Liste des propriétés héritées de la page active | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |

## Objets pris en charge par Java {#java-backed-objects}

L’objet Java correspondant renvoie chacun des objets suivants.

| Nom de variable | Description |
|---|---|
| `component` | `com.day.cq.wcm.api.components.Component` |
| `componentContext` | `com.day.cq.wcm.api.components.ComponentContext` |
| `currentContentPolicy` | `com.day.cq.wcm.api.policies.ContentPolicy` |
| `currentContentPolicyProperties` | `com.day.cq.wcm.api.policies.ContentPolicy` |
| `currentDesign` | `com.day.cq.wcm.api.designer.Design` |
| `currentNode` | `javax.jcr.Node` |
| `currentPage` | `com.day.cq.wcm.api.Page` |
| `currentSession` | `javax.servlet.http.HttpSession` |
| `currentStyle` | `com.day.cq.wcm.api.designer.Style` |
| `designer` | `com.day.cq.wcm.api.designer.Designer` |
| `editContext` | `com.day.cq.wcm.api.components.EditContext` |
| `log` | `org.slf4j.Logger` |
| `out` | `java.io.PrintWriter` |
| `pageManager` | `com.day.cq.wcm.api.PageManager` |
| `reader` | `java.io.BufferedReader` |
| `request` | `org.apache.sling.api.SlingHttpServletRequest` |
| `resolver` | `org.apache.sling.api.resource.ResourceResolver` |
| `resource` | `org.apache.sling.api.resource.Resource` |
| `resourceDesign` | `com.day.cq.wcm.api.designer.Design` |
| `resourcePage` | `com.day.cq.wcm.api.Page` |
| `response` | `org.apache.sling.api.SlingHttpServletResponse` |
| `sling` | `org.apache.sling.api.scripting.SlingScriptHelper` |
| `slyWcmHelper` | `com.adobe.cq.sightly.WCMScriptHelper` |
| `wcmmode` | `com.adobe.cq.sightly.SightlyWCMMode` |
| `xssAPI` | `com.adobe.granite.xss.XSSAPI` |

## Objets pris en charge par JavaScript {#javascript-backed-objects}

Il est possible de prendre en charge la logique HTL avec JavaScript. Cependant, la méthode préférée ou recommandée consiste à utiliser les [modèles Sling](https://sling.apache.org/documentation/bundles/models.html).
