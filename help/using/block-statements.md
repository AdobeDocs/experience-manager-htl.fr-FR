---
title: À quoi renvoient les instructions de bloc HTL ?
description: Découvrez les instructions de bloc HTL ou le langage de modèle de HTML (HTL). Les instructions de bloc sont des attributs de données personnalisés ajoutés directement au HTML existant.
exl-id: a517dcef-ab7a-4d4c-a1a9-2e57aad034f7
source-git-commit: 79d299766da07dae001708b396b05c73cd70d4cc
workflow-type: ht
source-wordcount: '1563'
ht-degree: 100%

---

# Instructions de bloc HTL {#htl-block-statements}

Les instructions du bloc HTL sont des attributs `data` personnalisés ajoutés directement au code HTML existant. Cela permet l’annotation simple et discrète d’une page HTML statique du prototype, qui la convertit en un modèle dynamique de fonctionnement sans rompre la validité du code HTML.

## Présentation des blocs {#overview}

Les plug-ins du bloc HTL sont définis par les attributs `data-sly-*` définis sur des éléments HTML. Les éléments peuvent contenir une balise de fermeture ou peuvent se fermer automatiquement. Les attributs peuvent contenir des valeurs (qui peuvent être des chaînes ou des expressions statiques) ou simplement des attributs booléens (sans valeur).

```xml
<tag data-sly-BLOCK></tag>                                 <!--/* A block is simply consists in a data-sly attribute set on an element. */-->
<tag data-sly-BLOCK/>                                      <!--/* Empty elements (without a closing tag) should have the trailing slash. */-->
<tag data-sly-BLOCK="string value"/>                       <!--/* A block statement usually has a value passed, but not necessarily. */-->
<tag data-sly-BLOCK="${expression}"/>                      <!--/* The passed value can be an expression as well. */-->
<tag data-sly-BLOCK="${@ myArg='foo'}"/>                   <!--/* Or a parametric expression with arguments. */-->
<tag data-sly-BLOCKONE="value" data-sly-BLOCKTWO="value"/> <!--/* Several block statements can be set on a same element. */-->
```

Tous les attributs `data-sly-*` évalués sont supprimés des balises générées.

### Identifiants {#identifiers}

Une instruction de bloc peut également être suivie d’un identifiant :

```xml
<tag data-sly-BLOCK.IDENTIFIER="value"></tag>
```

L’identifiant peut être utilisé par l’instruction de bloc de différentes manières. Voici quelques exemples :

```xml
<!--/* Example of statements that use the identifier to set a variable with their result: */-->
<div data-sly-use.navigation="MyNavigation">${navigation.title}</div>
<div data-sly-test.isEditMode="${wcmmode.edit}">${isEditMode}</div>
<div data-sly-list.child="${currentPage.listChildren}">${child.properties.jcr:title}</div>
<div data-sly-template.nav>Hello World</div>

<!--/* The attribute statement uses the identifier to know to which attribute it should apply it's value: */-->
<div data-sly-attribute.title="${properties.jcr:title}"></div> <!--/* This will create a title attribute */-->
```

Les identifiants de niveau supérieur ne respectent pas la casse (car ils peuvent être définis par des attributs HTML qui ne respectent pas la casse), mais toutes leurs propriétés respectent la casse.

## Instructions de bloc disponibles {#available-block-statements}

Un certain nombre d’instructions de bloc sont disponibles. Lorsqu’elles sont utilisées sur le même élément, la liste de priorités suivante définit la manière dont les instructions de bloc sont évaluées :

1. `data-sly-template`
1. `data-sly-set`, `data-sly-test`, `data-sly-use`
1. `data-sly-call`
1. `data-sly-text`
1. `data-sly-element`, `data-sly-include`, `data-sly-resource`
1. `data-sly-unwrap`
1. `data-sly-list`, `data-sly-repeat`
1. `data-sly-attribute`

Lorsque deux instructions de bloc ont la même priorité, leur ordre d’évaluation va de gauche à droite.

### utiliser {#use}

`data-sly-use` initialise un objet d’assistance (défini dans JavaScript ou Java) et l’expose par une variable.

Initialisez un objet JavaScript dont le fichier source se situe dans le même répertoire que le modèle. Notez que le nom du fichier doit être utilisé :

```xml
<div data-sly-use.nav="navigation.js">${nav.foo}</div>
```

Initialisez une classe Java dont le fichier source se situe dans le même répertoire que le modèle. Notez que vous devez utiliser le nom de la classe, et non le nom du fichier :

```xml
<div data-sly-use.nav="Navigation">${nav.foo}</div>
```

Initialisez une classe Java dont la classe est installée en tant que partie intégrante d’un lot OSGi. Notez que son nom de classe entièrement qualifié doit être utilisé :

```xml
<div data-sly-use.nav="org.example.Navigation">${nav.foo}</div>
```

Les paramètres peuvent être transmis à l’initialisation à l’aide d’options. En général, cette fonction ne doit être utilisée qu’avec un code HTL, qui se trouve lui-même dans un bloc `data-sly-template` :

```xml
<div data-sly-use.nav="${'navigation.js' @parentPage=currentPage}">${nav.foo}</div>
```

Initialisez un autre modèle HTL qui peut être appelé en utilisant `data-sly-call` :

```xml
<div data-sly-use.nav="navTemplate.html" data-sly-call="${nav.foo}"></div>
```

>[!NOTE]
>
>Pour plus d’informations sur Use-API, reportez-vous à la section :
>
>* [Utilisation d’une API Java](use-api-java.md) 
>* [Utilisation d’une API JavaScript](use-api-javascript.md) 


#### data-sly-use avec des ressources {#data-sly-use-with-resources}

Cela permet d’obtenir des ressources directement dans HTL avec `data-sly-use` et ne nécessite pas d’écriture de code pour l’obtenir.

Par exemple :

```xml
<div data-sly-use.product=“/etc/commerce/product/12345”>
  ${ product.title }
</div>
```

>[!TIP]
>
>Voir aussi la section [Chemin d’accès pas toujours obligatoire](#path-not-required).

### unwrap {#unwrap}

`data-sly-unwrap` supprime l’élément hôte du balisage généré tout en conservant son contenu. Cela permet d’exclure les éléments qui sont requis dans le cadre de la logique de présentation de HTL, mais qui ne sont pas souhaités dans la sortie actuelle.

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

### définir {#set}

`data-sly-set` définit un nouvel identifiant avec une valeur prédéfinie.

```xml
<span data-sly-set.profile="${user.profile}">Hello, ${profile.firstName} ${profile.lastName}!</span>
<a class="profile-link" href="${profile.url}">Edit your profile</a>
```

### texte {#text}

`data-sly-text` remplace le contenu de son élément hôte par le texte spécifié.

Par exemple, cela

```xml
<p>${properties.jcr:description}</p>
```

est équivalent à

```xml
<p data-sly-text="${properties.jcr:description}">Lorem ipsum</p>
```

Les deux affichent la valeur de `jcr:description` sous forme de texte du paragraphe. L’avantage de la deuxième méthode est qu’elle permet l’annotation discrète du HTML tout en conservant le contenu statique de l’espace réservé du concepteur d’origine.

### attribut   {#attribute}

`data-sly-attribute` ajoute des attributs à l’élément hôte.

Par exemple, cela

```xml
<div title="${properties.jcr:title}"></div>
```

est équivalent à

```xml
<div title="Lorem Ipsum" data-sly-attribute.title="${properties.jcr:title}"></div>
```

Tous deux définissent l’attribut `title` à la valeur de `jcr:title`. L’avantage de la deuxième méthode est qu’elle permet l’annotation discrète du HTML tout en conservant le contenu statique de l’espace réservé du concepteur original.

Les attributs sont résolus de gauche à droite. L’instance la plus à droite d’un attribut (littéral ou défini via `data-sly-attribute`) étant prioritaire sur toutes les instances du même attribut (défini littéralement ou via `data-sly-attribute`) définies à sa gauche.

Notez qu’un attribut (`literal` ou défini via `data-sly-attribute`) dont la valeur correspond à la chaîne vide sera supprimé dans le balisage final. La seule exception à cette règle est qu’un attribut littéral défini sur une chaîne vide littérale sera préservé. Par exemple :

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

### element   {#element}

`data-sly-element` remplace le nom de l’élément hôte.

Par exemple,

```xml
<h1 data-sly-element="${titleLevel}">text</h1>
```

Remplace le `h1` par la valeur de `titleLevel`.

Pour des raisons de sécurité, `data-sly-element` accepte uniquement les noms d’éléments suivants :

```xml
a abbr address article aside b bdi bdo blockquote br caption cite code col colgroup
data dd del dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header i ins
kbd li main mark nav ol p pre q rp rt ruby s samp section small span strong sub
sup table tbody td tfoot th thead time tr u var wbr
```

Pour définir d’autres éléments, la sécurité XSS doit être désactivée ( `@context='unsafe'`).

### test {#test}

`data-sly-test` supprime de manière conditionnelle l’élément hôte et son contenu. La valeur `false` supprime l’élément ; la valeur `true` conserve l’élément.

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

### répéter {#repeat}

Avec `data-sly-repeat`, vous pouvez répéter un élément plusieurs fois en fonction de la liste spécifiée.

```xml
<div data-sly-repeat="${currentPage.listChildren}">${item.name}</div>
```

Cela fonctionne de la même manière que `data-sly-list`, sauf que vous n’avez pas besoin d’un élément conteneur.

L’exemple suivant montre que vous pouvez également faire référence à l’*élément* pour les attributs :

```xml
<div data-sly-repeat="${currentPage.listChildren}" data-sly-attribute.class="${item.name}">${item.name}</div>
```

### liste {#list}

`data-sly-list` répète le contenu de l’élément hôte pour chaque propriété énumérable de l’objet fourni.

Voici une boucle simple :

```xml
<dl data-sly-list="${currentPage.listChildren}">
    <dt>index: ${itemList.index}</dt>
    <dd>value: ${item.title}</dd>
</dl>
```

Les variables par défaut suivantes sont disponibles dans le cadre de la liste :

* `item` : l’élément actuel dans l’itération.
* `itemList` : objet contenant les propriétés suivantes :
* `index` : compteur basé sur zéro (`0..length-1`).
* `count` : compteur basé sur un (`1..length`).
* `first` : `true` si l’élément actif est le premier élément.
* `middle` : `true` si l’élément actif n’est ni le premier ni le dernier élément.
* `last` : `true` si l’élément actif est le dernier élément.
* `odd` : `true` si `index` est impair.
* `even` : `true` si `index` est pair.

La définition d’un identificateur sur l’instruction `data-sly-list` vous permet de renommer les variables `itemList` et `item`. `item` deviendra `<variable>` et `itemList` deviendra `<variable>List`.

```xml
<dl data-sly-list.child="${currentPage.listChildren}">
    <dt>index: ${childList.index}</dt>
    <dd>value: ${child.title}</dd>
</dl>
```

Vous pouvez également accéder aux propriétés de façon dynamique :

```xml
<dl data-sly-list.child="${myObj}">
    <dt>key: ${child}</dt>
    <dd>value: ${myObj[child]}</dd>
</dl>
```

### ressource   {#resource}

`data-sly-resource` comprend le résultat du rendu de la ressource indiquée par le processus de résolution et de rendu Sling.

Une ressource simple comprend :

```xml
<article data-sly-resource="path/to/resource"></article>
```

#### Chemin d’accès pas toujours obligatoire {#path-not-required}

Notez que l’utilisation d’un chemin avec `data-sly-resource` n’est pas obligatoire si vous avez déjà la ressource. Si vous avez déjà la ressource, vous pouvez l’utiliser directement.

Par exemple, ce qui suit est correct.

```xml
<sly data-sly-resource="${resource.path @ decorationTagName='div'}"></sly>
```

Toutefois, ce qui suit est également parfaitement acceptable.

```xml
<sly data-sly-resource="${resource @ decorationTagName='div'}"></sly>
```

Il est recommandé d’utiliser directement la ressource, lorsque c’est possible, pour les raisons suivantes.

* Si vous disposez déjà de la ressource, une nouvelle résolution à l’aide du chemin d’accès représente un travail supplémentaire et inutile.
* L’utilisation du chemin d’accès lorsque vous disposez déjà de la ressource peut entraîner des résultats inattendus, car les ressources Sling peuvent être enveloppées ou synthétiques et ne pas être fournies au chemin d’accès donné.

#### Options {#resource-options}

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
>AEM offre une logique claire et simple pour contrôler les balises de décoration qui encapsulent les éléments inclus. Pour plus de détails, consultez la rubrique [Balise de décoration](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/implementing/developing/full-stack/components-templates/decoration-tag.html?lang=fr) dans la documentation sur les composants de développement.

### inclure {#include}

`data-sly-include` : remplace le contenu de l’élément hôte par le balisage généré par le fichier modèle HTML indiqué (HTL, JSP, ESP, etc.) lorsqu’il est traité par son moteur de modèle correspondant. Le contexte de rendu du fichier inclus ne comprendra pas le contexte HTL actif (celui du fichier d’inclusion). Par conséquent, pour l’inclusion des fichiers HTL, le `data-sly-use` actif doit être répété dans le fichier inclus (dans ce cas, il est généralement préférable d’utiliser `data-sly-template` et `data-sly-call`)

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

### Request-attributes {#request-attributes}

Dans `data-sly-include` et `data-sly-resource`, vous pouvez transmettre les `requestAttributes` pour les utiliser dans le script HTL récepteur.

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

Par exemple, via un modèle Sling, vous pouvez consommer la valeur des `requestAttributes` spécifiés.

Dans cet exemple, la disposition est injectée via le Map de la classe Use :

```xml
@Model(adaptables=SlingHttpServletRequest.class)
public class ProductSettings {
  @Inject @Optional @Default(values="empty")
  public String layout;

}
```

### modèle et appels {#template-call}

Les blocs de modèle peuvent être utilisés comme des appels de fonction : dans leur déclaration, ils peuvent obtenir des paramètres, qui peuvent ensuite être transmis lors de leur appel. Ils permettent également la récursion.

`data-sly-template` définit un modèle. L’élément hôte et son contenu ne sont pas rendus par HTL.

`data-sly-call` appelle un modèle défini avec data-sly-template. Le contenu du modèle appelé (paramétrable de manière optionnelle) remplace le contenu de l’élément hôte de l’appel.

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

## Élément sly {#sly-element}

La balise HTML `<sly>` peut être utilisée pour supprimer l’élément actif et ne laisser s’afficher que ses tâches enfants. Sa fonctionnalité est similaire à celle de l’élément de bloc `data-sly-unwrap` :

```xml
<!--/* This will display only the output of the 'header' resource, without the wrapping <sly> tag */-->
<sly data-sly-resource="./header"></sly>
```

Bien que n’étant pas une balise HTML 5 valide, la balise `<sly>` peut être affichée dans la sortie finale à l’aide de `data-sly-unwrap` :

```xml
<sly data-sly-unwrap="${false}"></sly> <!--/* outputs: <sly></sly> */-->
```

L’objectif de l’élément `<sly>` est de rendre plus évident le fait que l’élément n’est pas une sortie. Si vous le souhaitez, vous pouvez continuer à utiliser `data-sly-unwrap`.

Comme avec `data-sly-unwrap`, essayez de réduire l’utilisation de cet élément.
