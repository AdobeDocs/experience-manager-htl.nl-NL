---
title: HTML JavaScript Use-API
seo-title: HTML JavaScript Use-API
description: Met de HTML-sjabloontaal - HTML - JavaScript Use-API heeft een HTML-bestand toegang tot de hulplijncode die in JavaScript is geschreven.
seo-description: Met de HTML-sjabloontaal - HTML - JavaScript Use-API heeft een HTML-bestand toegang tot de hulplijncode die in JavaScript is geschreven.
uuid: 7ab34b10-30ac-44d6-926b-0234f52e5541
contentOwner: User
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: reference
discoiquuid: 18871af8-e44b-4eec-a483-fcc765dae58f
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: bd1962e25d152be4f1608d0a83d8d5b3e728b4aa

---


# HTML JavaScript Use-API {#htl-javascript-use-api}

Met de HTML-sjabloontaal (HTML) JavaScript Use-API kan een HTML-bestand toegang krijgen tot hulplijncode die in JavaScript is geschreven. Hierdoor kunnen alle complexe bedrijfslogica worden ingekapseld in de code JavaScript, terwijl de code HTML slechts op directe prijsverhogingsproductie behandelt.

## Een eenvoudig voorbeeld {#a-simple-example}

We definiëren een component, `info`die zich bevindt op

**`/apps/my-example/components/info`**

Het bevat twee bestanden:

* **`info.js`**: een JavaScript-bestand dat de use-klasse definieert.
* `info.html`: een HTML-bestand dat de component definieert `info`. Deze code gebruikt de functionaliteit van `info.js` via de use-API.

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

Wij creëren ook een inhoudsknoop die de **`info`** component bij gebruikt

**`/content/my-example`**, met eigenschappen:

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

De bijbehorende logica kan worden geschreven met de volgende JavaScript-code aan de ***serverzijde*** , die zich in een `component.js` bestand rechts naast de sjabloon bevindt:

```
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

Hiermee wordt geprobeerd de beschrijving van verschillende bronnen `title` te nemen en uit te snijden naar 50 tekens.

## Afhankelijkheden {#dependencies}

Stel je voor dat we een hulpprogrammaklasse hebben die al is uitgerust met slimme functies, zoals de standaardlogica voor de navigatitel of die een tekenreeks tot een bepaalde lengte knipt:

```
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

Het afhankelijkheidspatroon kan ook worden gebruikt om de logica van een andere component uit te breiden (die doorgaans de `sling:resourceSuperType` van de huidige component is).

Stel u voor dat de bovenliggende component al de component bevat `title`en dat we **`description`** ook een component willen toevoegen:

```
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

In het geval van **`data-sly-template`** verklaringen die van componenten onafhankelijk kunnen zijn, kan het nuttig zijn om parameters tot bijbehorende gebruik-API over te gaan.

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

De bijbehorende logica kan worden geschreven met de volgende JavaScript-code aan de ***serverzijde*** , die zich in een `template.js` bestand rechts naast het sjabloonbestand bevindt:

```
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

De doorgegeven parameters worden ingesteld op het `this` trefwoord.
