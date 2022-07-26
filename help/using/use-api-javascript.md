---
title: HTML JavaScript Use-API
description: Leer hoe een HTML-bestand met de HTML-sjabloontaal (HTL) van JavaScript toegang heeft tot hulplijncode die in JavaScript is geschreven.
exl-id: e98bfbd5-fa64-48c7-bd14-477d4c5e1788
source-git-commit: 7b53eff0652f650ffb8caae0e69aa349b5c548eb
workflow-type: tm+mt
source-wordcount: '326'
ht-degree: 0%

---

# HTML JavaScript Use-API {#htl-javascript-use-api}

Met de JavaScript Use-API (HTML-sjabloontaal) van JavaScript kan een HTML-bestand toegang krijgen tot hulplijncode die in JavaScript is geschreven. Hierdoor kunnen alle complexe bedrijfslogica worden ingekapseld in de code JavaScript, terwijl de code HTML slechts op directe prijsverhogingsproductie behandelt.

De volgende conventies worden gebruikt.

```javascript
/**
 * In the following example '/libs/dep1.js' and 'dep2.js' are optional
 * dependencies needed for this script's execution. Dependencies can
 * be specified using an absolute path or a relative path to this
 * script's own path.
 *
 * If no dependencies are needed the dependencies array can be omitted.
 */
use(['dep1.js', 'dep2.js'], function (Dep1, Dep2) {
    // implement processing
  
    // define this Use object's behavior
    return {
        propertyName: propertyValue
        functionName: function () {}
    }
});
```

## Een eenvoudig voorbeeld {#a-simple-example}

We definiëren een component, `info`, gevestigd te

`/apps/my-example/components/info`

Het bevat twee bestanden:

* **`info.js`**: een JavaScript-bestand dat de use-klasse definieert.
* **`info.html`**: een HTML-bestand dat de component definieert `info`. Deze code gebruikt de functionaliteit van `info.js` via de use-API.

### /apps/my-example/component/info/info.js {#apps-my-example-component-info-info-js}

```java
"use strict";
use(function () {
    var info = {};
    info.title = resource.properties["title"];
    info.description = resource.properties["description"];
    return info;
});
```

### /apps/my-example/component/info/info.html {#apps-my-example-component-info-info-html}

```xml
<div data-sly-use.info="info.js">
    <h1>${info.title}</h1>
    <p>${info.description}</p>
</div>
```

We maken ook een inhoudsknooppunt dat gebruikmaakt van de `info` component bij

`/content/my-example`, met eigenschappen:

* `sling:resourceType = "my-example/component/info"`
* `title = "My Example"`
* `description = "This is some example content."`

Hier volgt de resulterende structuur van de opslagplaats:

### Structuur opslagplaats {#repository-structure}

```java
{
  "apps": {
    "my-example": {
      "components": {
        "info": {
          "info.html": {
            ...
          },
          "info.js": {
            ...
          }
        }
      }
    }
 },
 "content": {
    "my-example": {
      "sling:resourceType": "my-example/component/info",
      "title": "My Example",
      "description": "This is some example content."
    }
  }
}
```

Overweeg de volgende componentsjabloon:

```xml
<section class="component-name" data-sly-use.component="component.js">
    <h1>${component.title}</h1>
    <p>${component.description}</p>
</section>
```

De corresponderende logica kan worden geschreven met de volgende JavaScript-code op de server in een `component.js` bestand rechts naast de sjabloon:

```javascript
use(function () {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };

    var title = currentPage.getNavigationTitle() || currentPage.getTitle() || currentPage.getName();
    var description = properties.get(Constants.DESCRIPTION_PROP, "").substr(0, Constants.DESCRIPTION_LENGTH);

    return {
        title: title,
        description: description
    };
});
```

Dit probeert de `title` uit verschillende bronnen en snijdt de beschrijving uit tot 50 tekens.

## Afhankelijkheden {#dependencies}

Stel je voor dat we een hulpprogrammaklasse hebben die al is uitgerust met slimme functies, zoals de standaardlogica voor de navigatitel of die een tekenreeks tot een bepaalde lengte knipt:

```javascript
use(['../utils/MyUtils.js'], function (utils) {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };

    var title = utils.getNavigationTitle(currentPage);
    var description = properties.get(Constants.DESCRIPTION_PROP, "").substr(0, Constants.DESCRIPTION_LENGTH);

    return {
        title: title,
        description: description
    };
});
```

## Uitbreiden {#extending}

Het afhankelijkheidspatroon kan ook worden gebruikt om de logica van een andere component uit te breiden (dit is doorgaans het `sling:resourceSuperType` van de huidige component).

Stel dat de bovenliggende component al de `title`en wij willen een `description` ook:

```javascript
use(['../parent-component/parent-component.js'], function (component) {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };

    component.description = utils.shortenString(properties.get(Constants.DESCRIPTION_PROP, ""), Constants.DESCRIPTION_LENGTH);

    return component;
});
```

## Parameters doorgeven aan een sjabloon {#passing-parameters-to-a-template}

In het geval van `data-sly-template` instructies die onafhankelijk kunnen zijn van componenten, kan het handig zijn om parameters door te geven aan de gekoppelde use-API.

Zo in onze component roepen wij een malplaatje dat in een verschillend dossier wordt gevestigd:

```xml
<section class="component-name" data-sly-use.tmpl="template.html" data-sly-call="${tmpl.templateName @ page=currentPage}"></section>
```

Dit is de sjabloon in `template.html`:

```xml
<template data-sly-template.templateName="${@ page}" data-sly-use.tmpl="${'template.js' @ page=page, descriptionLength=50}">
    <h1>${tmpl.title}</h1>
    <p>${tmpl.description}</p>
</template>
```

De corresponderende logica kan worden geschreven met de volgende JavaScript-code op de server in een `template.js` bestand rechts naast het sjabloonbestand:

```javascript
use(function () {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description"
    };

    var title = this.page.getNavigationTitle() || this.page.getTitle() || this.page.getName();
    var description = this.page.getProperties().get(Constants.DESCRIPTION_PROP, "").substr(0, this.descriptionLength);

    return {
        title: title,
        description: description
    };
});
```

De doorgegeven parameters worden ingesteld op de `this` trefwoord.
