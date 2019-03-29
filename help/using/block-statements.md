---
title: Instructions du bloc HTL
seo-title: Instructions du bloc HTL
description: Les instructions du bloc HTL sont des attributs de données personnalisés
  ajoutés directement au code HTML existant.
seo-description: Les instructions du bloc HTL sont des attributs de données personnalisés
  ajoutés directement au code HTML existant.
uuid: 0624fb6e-6989-431b-aabc-1138325393f1
contentOwner: Utilisateur
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: référence
discoiquuid: 58aa6ea8-1d45-4f6f-a77e-4819f593a19d
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 796c55d3d85e6b5a3efaa5c04a25be1b0b4e54dd

---


# Instructions du bloc HTL {#htl-block-statements}

Les instructions du bloc HTL sont des attributs `data` personnalisés ajoutés directement au code HTML existant. Cela permet l’annotation simple et discrète d’une page HTML statique du prototype, qui la convertit en un modèle dynamique de fonctionnement sans rompre la validité du code HTML.

## Élément sly {#sly-element}

L’**élément &lt;sly&gt;** n’apparaît pas dans le code HTML résultant et peut être utilisé à la place de data-sly-unwrap. L’objectif de l’élément &lt;sly&gt; est de rendre plus évident le fait que l’élément n’est pas généré. Si vous le souhaitez, vous pouvez continuer à utiliser data-sly-unwrap.

```xml
<sly data-sly-test.varone="${properties.yourProp}"/>
```

Comme pour data-sly-unwrap, essayez de réduire l’utilisation de cet élément au minimum.

## use {#use}

**`data-sly-use`** : initialise un objet d’assistance (défini dans JavaScript ou Java) et l’expose par une variable.

Initialisez un objet JavaScript dont le fichier source se situe dans le même répertoire que le modèle. Notez que le nom du fichier doit être utilisé :

```xml
<div data-sly-use.nav="navigation.js">${nav.foo}</div>
```

Initialisez une classe Java dont le fichier source se situe dans le même répertoire que le modèle. Notez que le nom de la classe doit être utilisé, et non le nom du fichier :

```xml
        <div data-sly-use.nav="Navigation">${nav.foo}</div>
```

Initialisez une classe Java dont la classe est installée en tant que partie intégrante d’un lot OSGi. Notez que son nom de classe entièrement qualifié doit être utilisé :

```xml
        <div data-sly-use.nav="org.example.Navigation">${nav.foo}</div>
```

Les paramètres peuvent être transmis à l’initialisation à l’aide d’*options*. En général, cette fonction doit être utilisée uniquement avec du code HTL, qui se trouve lui-même dans un bloc `data-sly-template` :

```xml
<div data-sly-use.nav="${'navigation.js' @parentPage=currentPage}">${nav.foo}</div>
```

Initialisez un autre modèle HTL qui peut être appelé en utilisant **`data-sly-call`** :

```xml
<div data-sly-use.nav="navTemplate.html" data-sly-call="${nav.foo}"></div>
```

>[!NOTE]
>
>Pour plus d’informations sur Use-API, reportez-vous à la section :
>
>* [Utilisation d’une API Java](use-api-java.md) 
>* [Utilisation d’une API JavaScript](use-api-javascript.md) 
>



## unwrap   {#unwrap}

**`data-sly-unwrap`** : supprime l’élément hôte de la balise générée tout en conservant son contenu. Cela permet d’exclure les éléments qui sont requis dans le cadre de la logique de présentation de HTL, mais qui ne sont pas souhaités dans la sortie actuelle.

Toutefois, cette instruction doit être utilisée avec modération. En général, il est préférable de conserver la balise HTL aussi près que possible de la balise de sortie attendue. En d’autres termes, en ajoutant des instructions de bloc HTL, essayez autant que possible d’annoter simplement le HTML existant, sans ajouter de nouveaux éléments.

Par exemple, cela

```xml
<p data-sly-use.nav="navigation.js">Hello World</p>
```

génère

```xml
<p>Hello World</p>
```

Tandis que cela

```xml
<p data-sly-use.nav="navigation.js" data-sly-unwrap>Hello World</p>
```

génère

```xml
Hello World
```

Il est également possible de déployer un élément de manière conditionnelle :

```xml
<div class="popup" data-sly-unwrap="${isPopup}">content</div>
```

## text   {#text}

**`data-sly-text`** : remplace le contenu de son élément hôte par le texte spécifié.

Par exemple, cela

```xml
<p>${properties.jcr:description}</p>
```

est équivalent à

```xml
<p data-sly-text="${properties.jcr:description}">Lorem ipsum</p>
```

Tous deux afficheront la valeur **`jcr:description` comme texte du paragraphe. ** L’avantage de la deuxième méthode est qu’elle permet l’annotation discrète du HTML tout en conservant le contenu statique de l’espace réservé du concepteur d’origine.

## attribut   {#attribute}

**data-sly-attribute** : ajoute des attributs à l’élément hôte.

Par exemple, cela

```xml
<div title="${properties.jcr:title}"></div>
```

est équivalent à

```xml
<div title="Lorem Ipsum" data-sly-attribute.title="${properties.jcr:title}"></div>
```

Tous deux définissent l’attribut `title` sur la valeur **`jcr:title`**. L’avantage de la deuxième méthode est qu’elle permet l’annotation discrète du HTML tout en conservant le contenu statique de l’espace réservé du concepteur d’origine.

Les attributs sont résolus de gauche à droite. L’instance la plus à droite d’un attribut (littéral ou défini par l’intermédiaire de l’attribut **`data-sly-attribute`**) a la priorité sur toutes les autres instances d’un même attribut (littéral ou défini par l’intermédiaire de l’attribut **`data-sly-attribute`**) définies à sa gauche.

Notez qu’un attribut (**`literal`** ou défini par l’intermédiaire de **`data-sly-attribute`**) dont la valeur *correspond* à la chaîne vide sera supprimé dans le balisage final. La seule exception à cette règle : un attribut *littéral* défini sur une chaîne vide *littérale* est conservé. Par exemple :

```xml
<div class="${''}" data-sly-attribute.id="${''}"></div>
```

produit,

```xml
<div></div>
```

mais,

```xml
<div class="" data-sly-attribute.id=""></div>
```

produit,

```xml
<div class=""></div>
```

Pour définir plusieurs attributs, transmettez les paires clé-valeur d’une prise d’objet carte correspondant aux attributs et leurs valeurs. Par exemple, si

```xml
attrMap = {
    title: "myTitle",
    class: "myClass",
    id: "myId"
}
```

Alors,

```xml
<div data-sly-attribute="${attrMap}"></div>
```

produit,

```xml
<div title="myTitle" class="myClass" id="myId"></div>
```

## element   {#element}

**`data-sly-element`** : remplace le nom de l’élément hôte.

Par exemple :

```xml
<h1 data-sly-element="${titleLevel}">text</h1>
```

Remplace **`h1`** par la valeur de **`titleLevel`**.

Pour des raisons de sécurité, `data-sly-element` accepte uniquement les noms d’éléments suivants :

```xml
a abbr address article aside b bdi bdo blockquote br caption cite code col colgroup
data dd del dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header i ins
kbd li main mark nav ol p pre q rp rt ruby s samp section small span strong sub 
sup table tbody td tfoot th thead time tr u var wbr
```

Pour définir d’autres éléments, la sécurité XSS doit être désactivée ( `@context='unsafe'`).

## test {#test}

**`data-sly-test` :** supprime de manière conditionnelle l’élément hôte et son contenu. La valeur `false` supprime l’élément ; la valeur `true` conserve l’élément.

Par exemple, le rendu de l’élément `p` et de son contenu ne sera effectué que si `isShown` a la valeur `true` :

```xml
<p data-sly-test="${isShown}">text</p>
```

Le résultat d’un test peut être affecté à une variable pouvant être utilisée ultérieurement. Ceci est généralement utilisé pour construire la logique conditionnelle, puisqu’il n’y a aucune autre instruction explicite :

```xml
<p data-sly-test.abc="${a || b || c}">is true</p>
<p data-sly-test="${!abc}">or not</p>
```

La variable, une fois définie, a une portée globale dans le fichier HTL.

Voici quelques exemples de comparaison de valeurs :

```xml
<div data-sly-test="${properties.jcr:title == 'test'}">TEST</div>
<div data-sly-test="${properties.jcr:title != 'test'}">NOT TEST</div>

<div data-sly-test="${properties['jcr:title'].length > 3}">Title is longer than 3</div>
<div data-sly-test="${properties['jcr:title'].length >= 0}">Title is longer or equal to zero </div> 

<div data-sly-test="${properties['jcr:title'].length > aemComponent.MAX_LENGTH}">
    Title is longer than the limit of ${aemComponent.MAX_LENGTH}
</div>
```

## repeat {#repeat}

data-sly-repeat vous permet de *répéter* un élément plusieurs fois en fonction de la liste spécifiée.

```xml
<div data-sly-repeat="${currentPage.listChildren}">${item.name}</div>
```

Cela fonctionne de la même manière que data-sly-list, sauf que vous n’avez pas besoin d’élément conteneur.

L’exemple suivant montre que vous pouvez également faire référence à *item* pour les attributs :

```xml
<div data-sly-repeat="${currentPage.listChildren}" data-sly-attribute.class="${item.name}">${item.name}</div>
```

## list {#list}

**`data-sly-list`** : répète le contenu de l’élément hôte pour chaque propriété énumérable dans l’objet fourni.

Voici une boucle simple :

```xml
<dl data-sly-list="${currentPage.listChildren}">
    <dt>index: ${itemList.index}</dt>
    <dd>value: ${item.title}</dd>
</dl>
```

Les variables par défaut suivantes sont disponibles dans le cadre de la liste :

**`item`** : l’élément actuel dans l’itération.

**`itemList`** : objet contenant les propriétés suivantes :

**`index`** : compteur de base zéro (`0..length-1`).

**`count`** : compteur de base un (`1..length`).

`first` : `true` si l’élément actif est le premier élément.

**`middle`** : `true` si l’élément actif n’est ni le premier ni le dernier élément.

**`last`** : `true` si l’élément actif est le dernier élément.

**`odd`** : `true` si `index` est impair.

**`even`** : `true` si `index` est pair.

La définition d’un identificateur dans l’instruction `data-sly-list` vous permet de renommer les variables **`itemList`** et `item`. **`item`** devient *** `<variable>`*** et **`itemList`** devient **`*<variable>*List`**.

```xml
<dl data-sly-list.child="${currentPage.listChildren}">
    <dt>index: ${childList.index}</dt>
    <dd>value: ${child.title}</dd>
</dl>
```

Vous pouvez également accéder aux propriétés dynamiquement :

```xml
<dl data-sly-list.child="${myObj}">
    <dt>key: ${child}</dt>
    <dd>value: ${myObj[child]}</dd>
</dl>
```

## ressource   {#resource}

**`data-sly-resource`** : inclut le résultat du rendu de la ressource indiquée par la résolution Sling et le processus de rendu.

Une ressource unique inclut :

```xml
<article data-sly-resource="path/to/resource"></article>
```

Les options autorisent un certain nombre de variantes supplémentaires :

Manipulation de chemin d’accès de la ressource :

```xml
<article data-sly-resource="${ @ path='path/to/resource'}"></article>
<article data-sly-resource="${'resource' @ prependPath='my/path'}"></article>
<article data-sly-resource="${'my/path' @ appendPath='resource'}"></article>
```

Ajout (ou remplacement) d’un sélecteur :

```xml
<article data-sly-resource="${'path/to/resource' @ selectors='selector'}"></article>
```

Ajout, remplacement ou suppression de plusieurs sélecteurs :

```xml
<article data-sly-resource="${'path/to/resource' @ selectors=['s1', 's2']}"></article>
```

Ajout d’un sélecteur des existants :

```xml
<article data-sly-resource="${'path/to/resource' @ addSelectors='selector'}"></article>
```

Suppression des sélecteurs des existants :

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors='selector1'}"></article>
```

Suppression de tous les sélecteurs :

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors}"></article>
```

Remplacement du type de la ressource :

```xml
<article data-sly-resource="${'path/to/resource' @ resourceType='my/resource/type'}"></article>
```

Modification du mode WCM :

```xml
<article data-sly-resource="${'path/to/resource' @ wcmmode='disabled'}"></article>
```

Par défaut, les balises de décoration AEM sont désactivées. L’option decorationTagName permet de les réactiver et l’option cssClassName permet d’ajouter des classes à cet élément.

```xml
<article data-sly-resource="${'path/to/resource' @ decorationTagName='span',
cssClassName='className'}"></article>
```

>[!NOTE]
>
>AEM offre une logique claire et simple pour contrôler les balises de décoration qui encapsulent les éléments inclus. Pour plus d'informations, voir [Decoration Tag](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/decoration-tag) dans la documentation de développement des composants.

## inclusion {#include}

**`data-sly-include`** : remplace le contenu de l’élément hôte par les balises générées par le fichier de modèle HTML indiqué (HTL, JSP, ESP etc.) lorsqu’il est traité par le moteur de modèle correspondant. Le contexte de rendu du *fichier inclus* ne comprend pas le contexte HTL actif (celui du *fichier inclus*). Par conséquent, pour l’inclusion des fichiers HTL, le **`data-sly-use`** actif doit être répété dans le fichier inclus (dans ce cas, il est généralement préférable d’utiliser **`data-sly-template`** et `data-sly-call`)

Une simple inclusion :

```xml
<section data-sly-include="path/to/template.html"></section>
```

Les JSP peuvent être inclus de la même manière :

```xml
<section data-sly-include="path/to/template.jsp"></section>
```

Les options vous permettent de manipuler le chemin d’accès du fichier :

```xml
<section data-sly-include="${ @ file='path/to/template.html'}"></section>
<section data-sly-include="${'template.html' @ prependPath='my/path'}"></section>
<section data-sly-include="${'my/path' @ appendPath='template.html'}"></section>
```

Vous pouvez également modifier le mode WCM :

```xml
<section data-sly-include="${'template.html' @ wcmmode='disabled'}"></section>
```

## modèle et appels   {#template-call}

`data-sly-template` : définit un modèle. L’élément hôte et son contenu ne sont pas rendus par HTL

`data-sly-call` : appelle un modèle défini avec data-sly-template. Le contenu du modèle appelé (paramétrable de manière optionnelle) remplace le contenu de l’élément hôte de l’appel.

Définissez un modèle statique, puis appelez-le :

```xml
<template data-sly-template.one>blah</template>
<div data-sly-call="${one}"></div>
```

Définissez un modèle dynamique, puis appelez-le avec des paramètres :

```xml
<template data-sly-template.two="${ @ title}"><h1>${title}</h1></template>
<div data-sly-call="${two @ title=properties.jcr:title}"></div>
```

Les modèles situés dans un autre fichier peuvent être initialisés avec `data-sly-use`. Notez que dans ce cas, `data-sly-use` et `data-sly-call` peuvent également être placés sur le même élément :

```xml
<div data-sly-use.lib="templateLib.html">
    <div data-sly-call="${lib.one}"></div>
    <div data-sly-call="${lib.two @ title=properties.jcr:title}"></div>
</div>
```

Le risque de récursivité de modèle est pris en charge :

```xml
<template data-sly-template.nav="${ @ page}">
    <ul data-sly-list="${page.listChildren}">
        <li>
            <div class="title">${item.title}</div>
            <div data-sly-call="${nav @ page=item}" data-sly-unwrap></div>
        </li>
    </ul>
</template>
<div data-sly-call="${nav @ page=currentPage}" data-sly-unwrap></div>
```

## Objets i18n et locale{#i-n-and-locale-objects}

Lorsque vous utilisez i18n et HTL, vous pouvez maintenant également transmettre des objets locale personnalisés.

```xml
${'Hello World' @ i18n, locale=request.locale}
```

## Manipulation d’URL {#url-manipulation}

Un nouveau jeu de manipulations d’URL est disponible.

Voir leurs exemples d’utilisation suivants :

Ajoute l’extension html à un chemin.

```xml
<a href="${item.path @ extension = 'html'}">${item.name}</a>
```

Ajoute l’extension html et un sélecteur à un chemin.

```xml
<a href="${item.path @ extension = 'html', selectors='products'}">${item.name}</a>
```

Ajoute l’extension html et un fragment (#value) à un chemin.

```xml
<a href="${item.path @ extension = 'html', fragment=item.name}">${item.name}</a>
```

## Fonctionnalités HTL prises en charge dans AEM 6.3 {#htl-features-supported-in-aem}

Les nouvelles fonctionnalités HTL suivantes sont prises en charge dans Adobe Experience Manager (AEM) 6.3 :

### Mise en forme des nombres et des dates {#number-date-formatting}

AEM 6.3 prend en charge la mise en forme native des nombres et des dates, sans écrire de code personnalisé. Il prend également en charge le fuseau horaire et le paramètre régional.

Les exemples suivants indiquent que le format est spécifié en premier, puis la valeur nécessitant la mise en forme :

```xml
<h2>${ 'dd-MMMM-yyyy hh:mm:ss' @
           format=currentPage.lastModified,
           timezone='PST',
           locale='fr'}</h2>

<h2>${ '#.00' @ format=300}</h2>
```

>[!NOTE]
>
>Pour obtenir des détails complets sur le format que vous pouvez utiliser, consultez [la spécification HTL](https://github.com/Adobe-Marketing-Cloud/htl-spec/blob/master/SPECIFICATION.md).

### data-sly-use avec des ressources {#data-sly-use-with-resources}

Cela permet d’obtenir des ressources directement dans HTL avec data-sly-use sans avoir à écrire du code.

Par exemple :

```xml
<div data-sly-use.product=“/etc/commerce/product/12345”>
  ${ product.title }
</div>
```

### Request-attributes {#request-attributes}

Dans *data-sly-include* et *data-sly-resource*, vous pouvez maintenant transmettre des attributs *requestAttributes* pour les utiliser dans le script HTL récepteur.

Vous pouvez ainsi transmettre des paramètres correctement dans des scripts ou des composants.

```xml
<sly data-sly-use.settings="com.adobe.examples.htl.core.hashmap.Settings" 
        data-sly-include="${ 'productdetails.html' @ requestAttributes=settings.settings}" />
```

Code Java de la classe Settings, Map est utilisé pour transmettre les attributs requestAttributes :

```xml
public class Settings extends WCMUsePojo {

  // used to pass is requestAttributes to data-sly-resource
  public Map<String, Object> settings = new HashMap<String, Object>();

  @Override
  public void activate() throws Exception {
    settings.put("layout", "flex");
  }
}
```

Par exemple, via un modèle Sling, vous pouvez consommer la valeur des attributs requestAttributes spécifiés.

Dans cet exemple, *layout* est injecté via Map de la classe d’utilisation :

```xml
@Model(adaptables=SlingHttpServletRequest.class)
public class ProductSettings {
  @Inject @Optional @Default(values="empty")
  public String layout;

}
```

### Correctif pour @extension {#fix-for-extension}

@extension fonctionne pour tous les scénarios dans AEM 6.3. Avant, vous pouviez obtenir un résultat comme *www.adobe.com.html*. De plus, il vérifie également si l’extension doit être ajoutée ou non.

```xml
${ link @ extension = 'html' }
```
