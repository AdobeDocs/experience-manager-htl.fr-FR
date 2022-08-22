---
title: Objets globaux HTL
description: Découvrez les objets énumérables, les objets pris en charge par Java et les objets pris en charge par JavaScript dans HTL.
exl-id: ca590b92-f1b3-4e44-a04a-a2c10dff256f
source-git-commit: 5ab1275c984135fe946f36905bbc979cf19edd80
workflow-type: tm+mt
source-wordcount: '0'
ht-degree: 0%

---


# Objets globaux HTL {#htl-global-objects}

Sans rien avoir à spécifier, HTL permet d’accéder à de nombreux objets utiles au développeur. Ces objets s’ajoutent à tout autre objet qui a pu être intégré avec [Use-API.](java-use-api.md)

>[!NOTE]
>
>Pour les développeurs familiarisés avec le développement JSP dans AEM, HTL permet d’accéder à tous les objets qui étaient couramment disponibles en JSP après avoir inclus `global.jsp`.

## Objets énumérables {#enumerable-objects}

Ces objets vous permettent d’accéder facilement aux informations fréquemment utilisées. Leur contenu est accessible avec notation par points et ils peuvent être itérés à l’aide de `data-sly-list` ou `data-sly-repeat`.

| Nom de variable | Description | Prise en charge par |
|--- |--- |--- |
| `properties` | Liste des propriétés de la ressource active | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `pageProperties` | Liste des propriétés de page de la page active | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `inheritedPageProperties` | Liste des propriétés de page héritées de la page active | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |

## Objets pris en charge par Java {#java-backed-objects}

Les objets suivants sont pris en charge par l’objet Java correspondant.

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

Il est possible de prendre en charge la logique HTL avec JavaScript. Toutefois, la méthode préférée ou recommandée est l’utilisation de [Modèles Sling](https://sling.apache.org/documentation/bundles/models.html).
