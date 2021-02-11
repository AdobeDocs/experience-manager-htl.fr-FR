---
title: Langage d’expression HTL
description: Le langage HTL (HTML Template Language) utilise un langage d’expression pour accéder aux structures de données qui fournissent les éléments dynamiques de la sortie HTML.
translation-type: tm+mt
source-git-commit: f7e46aaac2a4b51d7fa131ef46692ba6be58d878
workflow-type: tm+mt
source-wordcount: '1854'
ht-degree: 79%

---


# Langage d’expression HTL {#htl-expression-language}

Le langage HTL (HTML Template Language) utilise un langage d’expression pour accéder aux structures de données qui fournissent les éléments dynamiques de la sortie HTML. Ces expressions sont délimitées par les caractères `${` et `}`. Pour éviter du code HTML mal structuré, les expressions peuvent uniquement être utilisées dans les valeurs d’attributs, dans le contenu de l’élément ou dans les commentaires.

```xml
<!-- ${component.path} -->
<h1 class="${component.name}">
    ${properties.jcr:title}
</h1>
```

Les Expressions peuvent être échappées en préattendant un caractère `\`, par exemple `\${test}` sera rendu `${test}`.

>[!NOTE]
>
>Pour tester les exemples fournis sur cette page, vous pouvez utiliser un environnement d&#39;exécution en direct appelé [boucle d&#39;impression en lecture seule ](https://github.com/Adobe-Marketing-Cloud/aem-sightly-repl).

La syntaxe d’expression inclut des [variables](#variables), des [littéraux](#literals), des [opérateurs](#operators) et des [options](#options) :

## Variables {#variables}

Les variables sont des conteneurs qui stockent des valeurs de données ou des objets. Les noms des variables s’appellent des identificateurs.

Sans rien avoir à spécifier, HTL permet d’accéder à tous les objets qui étaient couramment disponibles en JSP après avoir inclus `global.jsp`. La page [Objets globaux](global-objects.md) contient la liste de tous les objets auxquels vous pouvez accéder avec HTL.

### Accès à la propriété   {#property-access}

Il existe deux manières d’accéder aux propriétés des variables : avec une notation par points ou avec une notation d’accolades :

```xml
${currentPage.title}  
${currentPage['title']} or ${currentPage["title"]}
```

La notation par points, plus simple, est préférable dans la plupart des cas, et la notation d’accolades doit être utilisée pour accéder à des propriétés qui contiennent des caractères d’identificateur non valides ou pour accéder à des propriétés de façon dynamique. Les deux chapitres suivants fournissent des détails sur ces deux cas.

Les propriétés accessibles peuvent être des fonctions. Cependant, la transmission des arguments n’est pas prise en charge. Ainsi, seules les fonctions n’ayant pas prévu d’arguments peuvent y accéder, par exemple des accesseurs Get. Il s’agit d’une limitation souhaitable, qui est conçue pour réduire la quantité de logique intégrée dans les expressions. Si nécessaire, l’instruction [`data-sly-use`](block-statements.md#use) peut être utilisée pour transmettre des paramètres à la logique.

L’exemple ci-dessus montre également que les fonctions getter Java, comme `getTitle()`, sont accessibles sans ajouter `get` et en mettant en minuscule le caractère qui suit.

### Caractères d&#39;identificateur valides {#valid-identifier-characters}

Les noms des variables, appelés identificateurs, respectent certaines règles. Ils doivent se début avec une lettre (`A`-`Z` et `a`-`z`) ou un trait de soulignement (`_`), et les caractères suivants peuvent également être des chiffres (`0`-`9`) ou des deux-points (`:`). Les lettres unicode telles que `å` et `ü` ne peuvent pas être utilisées dans les identificateurs.

Étant donné que le caractère deux-points (`:`) est commun dans les noms de propriété AEM, il convient de souligner qu’il s’agit bien d’un caractère d’identificateur valide :

`${properties.jcr:title}`

La notation d’accolades peut être utilisée pour accéder aux propriétés qui contiennent des caractères non valides d’identificateur, comme le caractère espace dans l’exemple ci-dessous :

`${properties['my property']}`

### Accès aux membres de manière dynamique {#accessing-members-dynamically}

```xml
${properties[myVar]}
```

### Manipulation permissive de valeurs null {#permissive-handling-of-null-values}

```xml
${currentPage.lastModified.time.toString}
```

## Littéraux {#literals}

Un littéral est une notation qui représente une valeur fixe.

### Booléen    {#boolean}

Un booléen représente une entité logique et peut avoir deux valeurs : `true` et `false`.

`${true} ${false}`

### Nombres {#numbers}

Il n’existe qu’un seul type de nombre : les entiers positifs. D’autres formats de nombre, comme la virgule flottante, sont pris en charge dans les variables, mais ne peuvent pas être exprimés en tant que littéraux.

`${42}`

### Chaînes {#strings}

Les chaînes représentent des données textuelles et peuvent être guillemets simples ou doublons :

`${'foo'} ${"bar"}`

En plus des caractères ordinaires, les caractères spéciaux suivants peuvent être utilisés :

* `\\` Barre oblique inverse
* `\'` Guillemet simple (ou apostrophe)
* `\"` Guillemet double
* `\t` Tab
* `\n` Nouvelle ligne
* `\r` Retour chariot
* `\f` Saut de page
* `\b` Retour arrière
* `\uXXXX` Caractère Unicode spécifié par les quatre chiffres en hexadécimal XXXX.\
    Certaines séquences d’échappement unicode sont utiles :

   * `\u0022` pour `"`
   * `\u0027` pour `'`

Pour les caractères qui ne sont pas répertoriés ci-dessus, un caractère de barre oblique inverse s’affiche avant un message d’erreur.

Voici quelques exemples d’utilisation d’échappement de chaîne :

```xml
<p>${'it\'s great, she said "yes!"'}</p>
<p title="${'it\'s great, she said \u0022yes!\u0022'}">...</p>
```

ce qui entraînera le résultat suivant, car HTL applique un échappement en fonction du contexte :

```xml
<p>it&#39;s great, she said &#34;yes!&#34;</p>
<p title="it&#39;s great, she said &#34;yes!&#34;">...</p>
```

### Tableaux    {#arrays}

Un tableau est un ensemble de valeurs organisé qui peut être mentionné avec un nom et un index. Il peut combiner plusieurs types d’éléments.

```xml
${[1,2,3,4]}
${myArray[2]}
```

Les tableaux permettent de fournir une liste de valeurs du modèle.

```xml
<ul data-sly-list="${[1,2,3,4]}">
  <li>${item}</li>
</ul>
```

## Opérateurs    {#operators}

### Opérateurs logiques {#logical-operators}

Ces opérateurs sont généralement utilisés avec des valeurs booléennes. Cependant, comme en JavaScript, ils renvoient la valeur de l’un des opérandes spécifiés. Ainsi, si vous les utilisez avec des valeurs non booléennes, ils peuvent renvoyer une valeur non booléenne.

Si une valeur peut être convertie en `true`, la valeur est dite vraie. Si une valeur peut être convertie en `false`, la valeur est dite fausse. Les valeurs qui peuvent être converties en `false` sont des variables non définies, des valeurs nulles, le nombre zéro et des chaînes vides.

#### Logique du « SAUF »{#logical-not}

`${!myVar}` renvoie  `false` si son opérande unique peut être converti en  `true`; dans le cas contraire, elle renvoie  `true`.

Cela peut par exemple être utilisé pour inverser une condition de test, par exemple l’affichage d’un élément uniquement s’il n’y a aucune page enfant :

```xml
<p data-sly-test="${!currentPage.hasChild}">current page has no children</p>
```

#### Logique du « ET »    {#logical-and}

`${varOne && varTwo}` renvoie  `varOne` s&#39;il est faux ; dans le cas contraire, elle renvoie  `varTwo`.

Cet opérateur peut être utilisé pour tester deux conditions en même temps, par exemple en vérifiant l’existence de deux propriétés :

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

L’opérateur logique ET peut également être utilisé pour afficher de manière conditionnelle des attributs HTML, car HTL supprime les attributs dont les valeurs définies de manière dynamique donnent la valeur false ou une chaîne vide. Ainsi, dans l’exemple ci-dessous, l’attribut `class` est affiché uniquement si `logic.showClass` est vrai et si `logic.className` existe et n’est pas vide :

```xml
<div class="${logic.showClass && logic.className}">...</div>
```

#### Logique du « OU »{#logical-or}

`${varOne || varTwo}` renvoie  `varOne` s&#39;il s&#39;agit d&#39;une vérité ; dans le cas contraire, elle renvoie  `varTwo`.

Cet opérateur peut être utilisé pour tester si l’une des deux conditions s’applique, par exemple en vérifiant l’existence d’au moins une propriété :

```xml
<div data-sly-test="${properties.jcr:title || properties.jcr:description}">...</div>
```

Comme l’opérateur logique OU renvoie la première variable correcte, il peut également être utilisé de manière pratique pour fournir des valeurs de substitution.

Il peut également être utilisé pour afficher de manière conditionnelle des attributs HTML, car HTL supprime les attributs dont les valeurs sont définies par des expressions dont la valeur est false ou une chaîne vide. Ainsi, l&#39;exemple ci-dessous affiche le titre **`properties.jcr:`** s&#39;il existe et n&#39;est pas vide, sinon il revient à l&#39;affichage de **`properties.jcr:description`** s&#39;il existe et n&#39;est pas vide, sinon il affiche le message &quot;aucun titre ou description fourni&quot; :

```xml
<p>${properties.jcr:title || properties.jcr:description || "no title or description provided"}</p>
```

### Opérateur conditionnel (ternaire){#conditional-ternary-operator}

`${varCondition ? varOne : varTwo}` renvoie `varOne` si `varCondition` est vrai ; autrement il renvoie `varTwo`.

Cet opérateur peut généralement être utilisé pour définir des conditions dans les expressions, comme l’affichage d’un message différent selon l’état de la page :

```xml
<p>${currentPage.isLocked ? "page is locked" : "page can be edited"}</p>
```

>[!TIP]
>
>Puisque les caractères deux-points sont également autorisés dans les identificateurs, il est préférable de séparer les opérateurs ternaires avec un espace blanc pour clarifier l’analyseur :

```xml
<p>${properties.showDescription ? properties.jcr:description : properties.jcr:title}</p>
```

### Opérateurs de comparaison    {#comparison-operators}

Les opérateurs d’égalité et d’inégalité prennent uniquement en charge les opérandes de même type. Lorsque les types ne correspondent pas, un message d’erreur s’affiche.

* Les chaînes sont égales si elles ont la même séquence de caractères.
* Les nombres sont égaux s’ils ont la même valeur.
* Les booléens sont égaux si les deux sont `true` ou `false`.
* Les variables nulles ou indéfinies sont égales à elles-mêmes et entre elles.

`${varOne == varTwo}` renvoie  `true` si  `varOne` et  `varTwo` sont égaux.

`${varOne != varTwo}` renvoie  `true` si  `varOne` et  `varTwo` ne sont pas égaux.

Les opérateurs relationnels prennent uniquement en charge les opérandes qui sont des nombres. Pour tous les autres types, un message d’erreur s’affiche.

`${varOne > varTwo}` renvoie  `true` si  `varOne` est supérieur à  `varTwo`.

`${varOne < varTwo}` renvoie  `true` si  `varOne` est inférieur à  `varTwo`.

`${varOne >= varTwo}` renvoie `true` si `varOne` est plus grand ou égal à `varTwo`.

`${varOne <= varTwo}` renvoie `true` si `varOne` est plus petit ou égal à `varTwo`.

### Regroupement des parenthèses {#grouping-parentheses}

L’opérateur de regroupement `()`  contrôle la priorité de l’évaluation dans les expressions.

`${varOne && (varTwo || varThree)}`

## Options {#options}

Les options d’expression permettent d’agir sur l’expression et de la modifier, ou servent de paramètres lorsqu’elles sont utilisées en même temps que des instructions de bloc.

Tout ce qui apparaît après `@` est optionnel :

```xml
${myVar @ optOne}
```

Les options peuvent être affectées d’une valeur, qui peut être une variable ou un littéral :

```xml
${myVar @ optOne=someVar}
${myVar @ optOne='bar'}
${myVar @ optOne=10}
${myVar @ optOne=true}
```

 Les options sont séparées par une virgule :

```xml
${myVar @ optOne, optTwo=bar}
```

Les expressions paramétriques contenant uniquement des options sont également disponibles :

```xml
${@ optOne, optTwo=bar}
```

### Format de chaîne    {#string-formatting}

Option qui remplace les espaces réservés répertoriés {*n*} avec la variable correspondante :

```xml
${'Page {0} of {1}' @ format=[current, total]}
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

`@extension` fonctionne dans tous les scénarios, en vérifiant si l&#39;extension doit être ajoutée ou non.

```xml
${ link @ extension = 'html' }
```

### Mise en forme des nombres et des dates {#number-date-formatting}

HTL permet le formatage natif des nombres et des dates, sans écrire de code personnalisé. Il prend également en charge le fuseau horaire et le paramètre régional.

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
>Pour obtenir des détails complets sur le format que vous pouvez utiliser, voir [spécification HTL](https://github.com/Adobe-Marketing-Cloud/htl-spec/blob/master/SPECIFICATION.md).

### Internationalisation    {#internationalization}

Convertit la chaîne dans la langue de la *source* active (voir ci-dessous), en utilisant le [dictionnaire](https://docs.adobe.com/content/help/en/experience-manager-65/developing/components/internationalization/i18n-translator.html) actif. Si aucune traduction n’est trouvée, la chaîne d’origine est utilisée.

```xml
${'Page' @ i18n}
```

L’option hint peut être utilisée pour proposer un commentaire à des traducteurs, indiquant le contexte dans lequel le texte est utilisé :

```xml
${'Page' @ i18n, hint='Translation Hint'}
```

La source par défaut de la langue est `resource`, ce qui signifie que le texte est traduit dans la même langue que le contenu. Il est possible de remplacer cette valeur par `user`, ce qui signifie que la langue est prise à partir des paramètres régionaux du navigateur ou des paramètres régionaux de l’utilisateur connecté :

```xml
${'Page' @ i18n, source='user'}
```

Les paramètres régionaux explicites remplacent les paramètres de la source :

```xml
${'Page' @ i18n, locale='en-US'}
```

Pour inclure des variables dans une chaîne traduite, l’option format peut être utilisée :

```xml
${'Page {0} of {1}' @ i18n, format=[current, total]}
```

### Jonction des tableaux    {#array-join}

Par défaut, lors de l’affichage d’un tableau en texte, HTL affiche des valeurs séparées par des virgules (sans espace).

Utilisez une option de jonction pour spécifier un autre séparateur :

```xml
${['one', 'two'] @ join='; '}
```

### Contexte d’affichage    {#display-context}

Le contexte de l’affichage d’une expression HTL fait référence à son emplacement dans la structure de la page HTML. Par exemple, si l’expression apparaît à un endroit qui générerait un nœud de texte lors du rendu, on dit qu’elle se trouve dans un contexte `text`. Si elle se trouve dans la valeur d’un attribut, on dit qu’elle se trouve dans un contexte `attribute`, et ainsi de suite.

À l’exception des contextes de script (JS) et de style (CSS), HTL détecte automatiquement le contexte des expressions et les échappe correctement, pour éviter les problèmes de sécurité XSS. Dans le cas des scripts et des CSS, le comportement de contexte souhaité doit être défini de façon explicite. En outre, le comportement de contexte peut également être défini de façon explicite dans tout autre cas où un remplacement du comportement automatique est souhaité.

Nous avons ici trois variables dans trois contextes différents :

* `properties.link` ( `uri` contexte)
* `properties.title` (`attribute` contexte)
* `properties.text` (`text` contexte)

HTL échappera chacun de ces éléments différemment en fonction des exigences de sécurité de leurs contextes respectifs. Aucun paramètre explicite de contexte n’est requis dans des situations normales comme celle-ci :

```xml
<a href="${properties.link}" title="${properties.title}">${properties.text}</a>
```

Pour produire les balises en toute sécurité (c’est-à-dire à l’endroit où l’expression elle-même évalue le code HTML), le contexte `html` est utilisé :

```xml
<div>${properties.richText @ context='html'}</div>
```

Le contexte explicite doit être défini pour les contextes de style :

```xml
<span style="color: ${properties.color @ context='styleToken'};">...</span>
```

Le contexte explicite doit être défini pour les contextes de script :

```xml
<span onclick="${properties.function @ context='scriptToken'}();">...</span>
```

L’échappement et la protection XSS peuvent également être désactivées :

```xml
<div>${myScript @ context='unsafe'}</div>
```

### Paramètres de contexte    {#context-settings}

| Contexte | Quand utiliser la personnalisation | Effets |
|--- |--- |--- |
| `text` | Valeur par défaut pour le contenu à l’intérieur des éléments | Code tous les caractères HTML spéciaux. |
| `html` | Pour produire les balises en toute sécurité | Filtre le code HTML pour respecter les règles de confidentialité d’AntiSamy, en supprimant ce qui ne correspond pas aux règles. |
| `attribute` | Défaut des valeurs d’attribut | Code tous les caractères HTML spéciaux. |
| `uri` | Pour afficher les liens et les chemins d’accès par défaut pour les valeurs d’attribut href et src | Valide l’URI pour écrire en tant que valeur d’attribut href ou src, ne produit rien si la validation échoue. |
| `number` | Pour afficher les nombres | Valide l’URI pour contenir un entier, produit zéro si la validation échoue. |
| `attributeName` | Par défaut pour data-sly-attribute lors de la définition des noms d’attribut | Valide le nom d’attribut, ne génère rien si la validation échoue. |
| `elementName` | Par défaut pour data-sly-element | Valide le nom d’élément, ne génère rien si la validation échoue. |
| `scriptToken` | Pour les identificateurs JS, les nombres littéraux ou les chaînes littérales | Valide le jeton JavaScript, ne génère rien si la validation échoue. |
| `scriptString` | Dans les chaînes JS | Code les caractères qui sortiraient de la chaîne. |
| `scriptComment` | Dans les commentaires JS | Valide le commentaire JavaScript, ne produit rien si la validation échoue. |
| `styleToken` | Pour les identificateurs CSS, les nombres, les dimensions, les chaînes, les couleurs hexadécimales ou les fonctions. | Valide le jeton CSS, ne produit rien si la validation échoue. |
| `styleString` | Dans les chaînes CSS | Code les caractères qui sortiraient de la chaîne. |
| `styleComment` | Dans les commentaires CSS | Valide le commentaire CSS, ne produit rien si la validation échoue. |
| `unsafe` | Uniquement si aucun élément ci-dessus ne fonctionne. | Désactive totalement l’échappement et la protection XSS. |
