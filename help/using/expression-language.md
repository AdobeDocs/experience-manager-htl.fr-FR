---
title: Langage d’expression HTL
seo-title: Langage d’expression HTL
description: Le langage HTL (HTML Template Language) utilise un langage d’expression pour accéder aux structures de données qui fournissent les éléments dynamiques de la sortie HTML.
seo-description: 'Le langage HTL (HTML Template Language) utilise un langage d’expression pour accéder aux structures de données qui fournissent les éléments dynamiques de la sortie HTML. '
uuid: 38b4a259-03b5-4847-91c6-e20377600070
contentOwner: Utilisateur
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: référence
discoiquuid: 9ba37ca0-f318-48b0-a791-a944a72502ed
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 84ed515309831fe413abf317d8739f2bba79afdb

---


# Langage d’expression HTL {#htl-expression-language}

Le langage HTL (HTML Template Language) utilise un langage d’expression pour accéder aux structures de données qui fournissent les éléments dynamiques de la sortie HTML. Ces expressions sont délimitées par les caractères `${` et `}`. Pour éviter du code HTML mal structuré, les expressions peuvent uniquement être utilisées dans les valeurs d’attributs, dans le contenu de l’élément ou dans les commentaires.

```xml
<!-- ${component.path} -->
<h1 class="${component.name}">
    ${properties.jcr:title}
</h1>
```

Les expressions peuvent être placées dans une séquence d’échappement en ajoutant un caractère **`\`**, par exemple : **`\${test}`** rendra **`${test}`**.

>[!NOTE]
>
>To try out the examples provided on this page, a live execution environment called the [Read Eval Print Loop](https://github.com/Adobe-Marketing-Cloud/aem-sightly-repl) can be used.

La syntaxe d’expression inclut des [variables](#variables), des [littéraux](#literals), des [opérateurs](#operators) et des [options](#options) :

## Variables {#variables}

Les variables sont des conteneurs qui stockent des valeurs de données ou des objets. Les noms des variables s’appellent des identificateurs.

Sans rien avoir à spécifier, HTL permet d’accéder à tous les objets qui étaient couramment disponibles en JSP après avoir inclus `global.jsp`. La page [Objets globaux](global-objects.md) contient la liste de tous les objets auxquels vous pouvez accéder avec HTL.

### Accès à la propriété   {#property-access}

Il existe deux manières d’accéder aux propriétés des variables : avec une notation par points ou avec une notation d’accolades :

`${currentPage.title}  
${currentPage['title']} or ${currentPage["title"]}`

La notation par points, plus simple, est préférable dans la plupart des cas, et la notation d’accolades doit être utilisée pour accéder à des propriétés qui contiennent des caractères d’identificateur non valides ou pour accéder à des propriétés de façon dynamique. Les deux chapitres suivants fournissent des détails sur ces deux cas.

Les propriétés accessibles peuvent être des fonctions. Cependant, la transmission des arguments n’est pas prise en charge. Ainsi, seules les fonctions n’ayant pas prévu d’arguments peuvent y accéder, par exemple des accesseurs Get. Il s’agit d’une limitation souhaitable, qui est conçue pour réduire la quantité de logique intégrée dans les expressions. Si nécessaire, l’instruction [`data-sly-use`](block-statements.md#use) peut être utilisée pour transmettre des paramètres à la logique.

L’exemple ci-dessus montre également que les fonctions getter Java, comme `getTitle()`, sont accessibles sans ajouter **`get`** et en mettant en minuscule le caractère qui suit.

### Caractères valides de l’identificateur {#valid-indentifier-characters}

Les noms des variables, appelés identificateurs, respectent certaines règles. Ils doivent commencer par une lettre (**`A`**-**`Z`** et **`a`**-**`z`**) ou un trait de soulignement (**`_`**), et les caractères suivants peuvent également être des chiffres (****`9`-`0`****) ou deux-points (**`:`**). Les lettres unicode telles que **`å`** et **`ü`ne peuvent pas être utilisées dans les identificateurs.**

Étant donné que le caractère deux-points (**:**) est courant dans des noms de propriété AEM, il est pratique que ce soit un caractère valide d’identificateur :

`${properties.jcr:title}`

La notation d’accolades peut être utilisée pour accéder aux propriétés qui contiennent des caractères non valides d’identificateur, comme le caractère espace dans l’exemple ci-dessous :

`${properties['my property']}`

### Accès aux membres de manière dynamique {#accessing-members-dynamically}

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

```xml
${properties[myVar]}
```

### Manipulation permissive de valeurs null {#permissive-handling-of-null-values}

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

```xml
${currentPage.lastModified.time.toString}
```

## Littéraux {#literals}

Un littéral est une notation qui représente une valeur fixe.

### Booléen   {#boolean}

Un booléen représente une entité logique et peut avoir deux valeurs : **`true`** et **`false`**.

`${true} ${false}`

### Nombres {#numbers}

Il n’existe qu’un seul type de nombre : les entiers positifs. D’autres formats de nombre, comme la virgule flottante, sont pris en charge dans les variables, mais ne peuvent pas être exprimés en tant que littéraux.

`${42}`

### Chaînes {#strings}

Elles représentent des données de texte, et peuvent être placées dans des guillemets simples ou doubles :

`${'foo'} ${"bar"}`

En plus des caractères ordinaires, les caractères spéciaux suivants peuvent être utilisés :

* **`\\`** Barre oblique inverse
* **`\'`** Guillemet simple (ou apostrophe)
* **`\"`** Guillemet double
* **`\t`** Tabulation
* **`\n`** Nouvelle ligne
* **`\r`** Retour chariot
* **`\f`** Saut de page
* **`\b`** Retour arrière
* `\uXXXX` Caractère Unicode spécifié par les quatre chiffres en hexadécimal XXXX.\
    Certaines séquences d’échappement unicode sont utiles :

   * **\u0022** pour **"**
   * **\u0027** pour **'**

Pour les caractères qui ne sont pas répertoriés ci-dessus, le fait de les précéder d’une barre oblique inverse affichera une erreur.

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

### Tableaux   {#arrays}

Un tableau est un ensemble de valeurs organisé qui peut être mentionné avec un nom et un index. Il peut combiner plusieurs types d’éléments.

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

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

## Opérateurs   {#operators}

### Opérateurs logiques {#logical-operators}

Ces opérateurs sont généralement utilisés avec des valeurs booléennes. Cependant, comme en JavaScript, ils renvoient la valeur de l’un des opérandes spécifiés. Ainsi, si vous les utilisez avec des valeurs non booléennes, ils peuvent renvoyer une valeur non booléenne.

Si une valeur peut être convertie en **`true`**, la valeur est dite vraie. Si une valeur peut être convertie en **`false`**, la valeur est dite fausse. Les valeurs qui peuvent être converties en **`false`sont les suivantes : les variables indéfinies, les valeurs null, le chiffre zéro et les chaînes vides.**

#### Logique du « SAUF »{#logical-not}

**`${!myVar}`** renvoie **`false`** si son opérande unique peut être converti en `true` ; autrement il renvoie **`true`**.

Cela peut par exemple être utilisé pour inverser une condition de test, par exemple l’affichage d’un élément uniquement s’il n’y a aucune page enfant :

```xml
<p data-sly-test="${!currentPage.hasChild}">current page has no children</p>
```

#### Logique du « ET »   {#logical-and}

**`${varOne && varTwo}`** renvoie `varOne` s’il est faux ; autrement il renvoie **varTwo**.

Cet opérateur peut être utilisé pour tester deux conditions en même temps, par exemple en vérifiant l’existence de deux propriétés :

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

L’opérateur logique ET peut également être utilisé pour afficher de manière conditionnelle des attributs HTML, car HTL supprime les attributs dont les valeurs définies de manière dynamique donnent la valeur false ou une chaîne vide. Ainsi, dans l’exemple ci-dessous, l’attribut **`class`** est affiché uniquement si **`logic.showClass`** est vrai et si **`logic.className`** existe et n’est pas vide :

```xml
<div class="${logic.showClass && logic.className}">...</div>
```

#### Logique du « OU »{#logical-or}

**`${varOne || varTwo}`** renvoie **varOne** s’il est vrai ; autrement il renvoie **varTwo**.

Cet opérateur peut être utilisé pour tester si l’une des deux conditions s’applique, par exemple en vérifiant l’existence d’au moins une propriété :

```xml
<div data-sly-test="${properties.jcr:title || properties.jcr:description}">...</div>
```

Comme l’opérateur logique OU renvoie la première variable correcte, il peut également être utilisé de manière pratique pour fournir des valeurs de substitution.

affiche de manière conditionnelle les attributs HTML, car HTL supprime les attributs dont la valeur définie par les expressions donne une valeur false ou une chaîne vide. Ainsi, l’exemple ci-dessous affiche **`properties.jcr:`**title s’il existe et n’est pas vide ; autrement il affiche **`properties.jcr:description`** s’il existe et n’est pas vide ; autrement il affiche le message "no title or description provided" :

```xml
<p>${properties.jcr:title || properties.jcr:description || "no title or description provided"}</p>
```

### Opérateur conditionnel (ternaire){#conditional-ternary-operator}

**`${varCondition ? varOne : varTwo}`** renvoie **`varOne`** si **`varCondition`** est vrai ; autrement il renvoie **`varTwo`**.

Cet opérateur peut généralement être utilisé pour définir des conditions dans les expressions, comme l’affichage d’un message différent selon l’état de la page :

```xml
<p>${currentPage.isLocked ? "page is locked" : "page can be edited"}</p>
```

Il est important de noter que comme les deux-points sont également autorisés dans les identificateurs, il est conseillé de séparer les opérateurs ternaires avec un espace pour offrir plus de clarté à l’analyseur :

```xml
<p>${properties.showDescription ? properties.jcr:description : properties.jcr:title}</p>
```

### Opérateurs de comparaison   {#comparison-operators}

Les opérateurs d’égalité et d’inégalité prennent uniquement en charge les opérandes de même type. Lorsque les types ne correspondent pas, un message d’erreur s’affiche.

* Les chaînes sont égales si elles ont la même séquence de caractères.
* Les nombres sont égaux s’ils ont la même valeur.
* Les booléens sont égaux si les deux sont **`true`** ou **`false`**.

* Les variables nulles ou indéfinies sont égales à elles-mêmes et entre elles.

**`${varOne == varTwo}`** renvoie **`true`** si **`varOne`** et **`varTwo`** sont égaux.

**`${varOne != varTwo}`** renvoie **`true`** si **`varOne`** et **`varTwo`** ne sont pas égaux.

Les opérateurs relationnels prennent uniquement en charge les opérandes qui sont des nombres. Pour tous les autres types, un message d’erreur s’affiche.

**`${varOne > varTwo}`** renvoie **`true`** si **`varOne`** est plus grand que **`varTwo`**.

**`${varOne < varTwo}`** renvoie **`true`** si **`varOne`** est plus petit que **`varTwo`**.

**`${varOne >= varTwo}`** renvoie **`true`** si **`varOne`** est plus grand ou égal à **`varTwo`**.

**`${varOne <= varTwo}`** renvoie **`true`** si **`varOne`** est plus petit ou égal à **`varTwo`**.

### Regroupement des parenthèses {#grouping-parentheses}

L’opérateur de regroupement **`(`** **`)`** contrôle la priorité de l’évaluation dans les expressions.

`${varOne && (varTwo || varThree)}`

## Options {#options}

<!-- 

Comment Type: draft

<p>TODO: review text below.</p>

 -->

Les options d’expression permettent d’agir sur l’expression et de la modifier, ou servent de paramètres lorsqu’elles sont utilisées en même temps que des instructions de bloc.

Tout ce qui apparaît après **`@`** est optionnel :

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

### Format de chaîne   {#string-formatting}

Option qui remplace les espaces réservés répertoriés {*n*} avec la variable correspondante :

```xml
${'Page {0} of {1}' @ format=[current, total]}
```

### Internationalisation   {#internationalization}

Convertit la chaîne dans la langue de la *source* active (voir ci-dessous), en utilisant le [dictionnaire](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/i18n-translator) actif. Si aucune traduction n’est trouvée, la chaîne d’origine est utilisée.

```xml
${'Page' @ i18n}
```

L’option hint peut être utilisée pour proposer un commentaire à des traducteurs, indiquant le contexte dans lequel le texte est utilisé :

```xml
${'Page' @ i18n, hint='Translation Hint'}
```

La source par défaut de la langue est « ressource », ce qui signifie que le texte est traduit dans la même langue que le contenu. Cela peut être changé en « utilisateur », ce qui signifie que la langue est extraite des paramètres régionaux du navigateur ou des paramètres régionaux de l’utilisateur connecté :

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

### Jonction des tableaux   {#array-join}

Par défaut, lors de l’affichage d’un tableau en texte, HTL affiche des valeurs séparées par des virgules (sans espace).

Utilisez une option de jonction pour spécifier un autre séparateur :

```xml
${['one', 'two'] @ join='; '}
```

### Contexte d’affichage   {#display-context}

Le contexte de l’affichage d’une expression HTL fait référence à son emplacement dans la structure de la page HTML. Par exemple, si l’expression apparaît à un endroit qui générerait un nœud de texte lors du rendu, on dit qu’elle se trouve dans un contexte **`text`**. Si elle se trouve dans la valeur d’un attribut, on dit qu’elle se trouve dans un contexte **`attribute`**, et ainsi de suite.

À l’exception des contextes de script (JS) et de style (CSS), HTL détecte automatiquement le contexte des expressions et les échappe correctement, pour éviter les problèmes de sécurité XSS. Dans le cas des scripts et des CSS, le comportement de contexte souhaité doit être défini de façon explicite. En outre, le comportement de contexte peut également être défini de façon explicite dans tout autre cas où un remplacement du comportement automatique est souhaité.

Dans ce cas, nous avons trois variables dans trois contextes différents : **`properties.link`** (contexte `uri`), **`properties.title`** (contexte **`attribute`**) et **`properties.text`** (contexte **`text`**). HTL échappera chacun de ces éléments différemment en fonction des exigences de sécurité de leurs contextes respectifs. Aucun paramètre explicite de contexte n’est requis dans des situations normales comme celle-ci :

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

### Paramètres de contexte   {#context-settings}

| Contexte | Quand utiliser la personnalisation | Effets |
|--- |--- |--- |
| text | Valeur par défaut pour le contenu à l’intérieur des éléments | Code tous les caractères HTML spéciaux. |
| html | Pour produire les balises en toute sécurité | Filtre le code HTML pour respecter les règles de confidentialité d’AntiSamy, en supprimant ce qui ne correspond pas aux règles. |
| attribut | Défaut des valeurs d’attribut | Code tous les caractères HTML spéciaux. |
| uri | Pour afficher les liens et les chemins d’accès par défaut pour les valeurs d’attribut href et src | Valide l’URI pour écrire en tant que valeur d’attribut href ou src, ne produit rien si la validation échoue. |
| number | Pour afficher les nombres | Valide l’URI pour contenir un entier, produit zéro si la validation échoue. |
| attributeName | Par défaut pour data-sly-attribute lors de la définition des noms d’attribut | Valide le nom d’attribut, ne génère rien si la validation échoue. |
| elementName | Par défaut pour data-sly-element | Valide le nom d’élément, ne génère rien si la validation échoue. |
| scriptToken | Pour les identificateurs JS, les nombres littéraux ou les chaînes littérales | Valide le jeton JavaScript, ne génère rien si la validation échoue. |
| scriptString | Dans les chaînes JS | Code les caractères qui sortiraient de la chaîne. |
| scriptComment | Dans les commentaires JS | Valide le commentaire JavaScript, ne produit rien si la validation échoue. |
| styleToken | Pour les identificateurs CSS, les nombres, les dimensions, les chaînes, les couleurs hexadécimales ou les fonctions. | Valide le jeton CSS, ne produit rien si la validation échoue. |
| styleString | Dans les chaînes CSS | Code les caractères qui sortiraient de la chaîne. |
| styleComment | Dans les commentaires CSS | Valide le commentaire CSS, ne produit rien si la validation échoue. |
| unsafe | Uniquement si aucun élément ci-dessus ne fonctionne. | Désactive totalement l’échappement et la protection XSS. |

