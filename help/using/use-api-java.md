---
title: HTML Java Use-API
description: 'De Java Use-API van HTL (de HTML-sjabloontaal) biedt een HTL-bestand toegang tot helpermethoden in een aangepaste Java-klasse. '
translation-type: tm+mt
source-git-commit: f7e46aaac2a4b51d7fa131ef46692ba6be58d878
workflow-type: tm+mt
source-wordcount: '2558'
ht-degree: 1%

---


# HTML Java Use-API {#htl-java-use-api}

Met de HTML Template Language (HTL) Java Use-API kan een HTML-bestand via `data-sly-use` toegang krijgen tot hulpmethoden in een aangepaste Java-klasse. Hierdoor kunnen alle complexe bedrijfslogica worden ingekapseld in de code van Java, terwijl de code HTML slechts op directe prijsverhogingsproductie behandelt.

Een Java Use-API-object kan een eenvoudige POJO zijn, die wordt geïnstantieerd door een bepaalde implementatie via de standaardconstructor van de POJO.

Met de API-API-POJO&#39;s kunt u ook een openbare methode, init genoemd, met de volgende handtekening toegankelijk maken:

```java
    /**
     * Initializes the Use bean.
     *
     * @param bindings All bindings available to the HTL scripts.
     **/
    public void init(javax.script.Bindings bindings);
```

De `bindings`-kaart kan objecten bevatten die context bieden aan het momenteel uitgevoerde HTML-script dat het Use-API-object kan gebruiken voor de verwerking ervan.

## Een eenvoudig voorbeeld {#a-simple-example}

We beginnen met een HTML-component die geen use-class heeft. Bestaat uit één bestand, `/apps/my-example/components/info.html`

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html}

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

Er wordt ook inhoud voor deze component toegevoegd die moet worden gerenderd bij `/content/my-example/`:

### `http://<host>:<port>/content/my-example.json` {#http-localhost-content-my-example-json}

```java
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

Wanneer deze inhoud wordt benaderd, wordt het HTML-bestand uitgevoerd. Binnen de HTML-code gebruiken we het contextobject `properties` om toegang te krijgen tot `title` en `description` en deze weer te geven. De HTML-uitvoer is:

### `view-source:http://<host>:<port>/content/my-example.html` {#view-source-http-localhost-content-my-example-html}

```xml
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Een Use-Class {#adding-a-use-class} toevoegen

De **info** component aangezien het staat heeft geen gebruik-klasse nodig om zijn (zeer eenvoudige) functie uit te voeren. Er zijn echter gevallen waarin u dingen moet doen die niet in HTML kunnen worden gedaan en dus een gebruiksklasse nodig hebt. Houd echter rekening met het volgende:

>[!NOTE]
>
>Een gebruiksklasse mag alleen worden gebruikt wanneer er iets niet alleen in HTML kan worden gedaan.

Stel dat u de component `info` de eigenschappen `title` en `description` van de bron wilt laten weergeven, maar allemaal in kleine letters. Aangezien HTML geen methode voor het verlagen van tekenreeksen heeft, hebt u een use-klasse nodig. We kunnen dit doen door een Java-gebruiksklasse toe te voegen en de `info.html` als volgt te wijzigen:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-1}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    private String lowerCaseTitle;
    private String lowerCaseDescription;

    @Override
    public void activate() throws Exception {
        lowerCaseTitle = getProperties().get("title", "").toLowerCase();
        lowerCaseDescription = getProperties().get("description", "").toLowerCase();
    }

    public String getLowerCaseTitle() {
        return lowerCaseTitle;
    }

    public String getLowerCaseDescription() {
        return lowerCaseDescription;
    }
}
```

In de volgende secties lopen we door de verschillende delen van de code.

### Lokaal vs. Bundle Java Class {#local-vs-bundle-java-class}

De Java-gebruiksklasse kan op twee manieren worden geïnstalleerd: **local** of **bundle**. In dit voorbeeld wordt een lokale installatie gebruikt.

In een lokale installatie wordt het Java-bronbestand naast het HTML-bestand in dezelfde opslagmap geplaatst. De bron wordt automatisch gecompileerd op bestelling. Er is geen afzonderlijke compilatie of verpakkingsstap vereist.

In een bundelinstallatie, moet de klasse van Java binnen een bundel OSGi worden gecompileerd en worden opgesteld gebruikend het standaard AEM bundelplaatsingsmechanisme (zie [Gebundelde Klasse van Java](#bundled-java-class)).

>[!NOTE]
>
>Een **lokale Java-use-class** wordt aanbevolen wanneer de use-class specifiek is voor de betreffende component.
>
>Een **bundel Java use-class** wordt geadviseerd wanneer de code van Java een dienst uitvoert die van veelvoudige componenten van HTML wordt betreden.

### Java-pakket is opslagpad {#java-package-is-repository-path}

Wanneer een lokale installatie wordt gebruikt, moet de pakketnaam van de use-klasse overeenkomen met die van de opslagplaats, waarbij eventuele koppeltekens in het pad worden vervangen door onderstrepingstekens in de pakketnaam.

In dit geval bevindt `Info.java` zich in `/apps/my-example/components/info`, zodat het pakket `apps.my_example.components.info` is:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-1}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {

   ...

}
```

>[!NOTE]
>
>Het gebruik van koppeltekens in de namen van opslagplaats-items wordt aanbevolen bij AEM ontwikkeling. Afbreekstreepjes zijn echter niet toegestaan in Java-pakketnamen. Daarom moeten **alle afbreekstreepjes in het repository pad worden geconverteerd naar onderstrepingstekens in de pakketnaam**.

### `WCMUsePojo` {#extending-wcmusepojo} uitbreiden

Hoewel er verschillende manieren zijn om een Java-klasse op te nemen met HTML (zie Alternatieven voor `WCMUsePojo`), is het eenvoudigst om de klasse `WCMUsePojo` uit te breiden:

#### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-2}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo

    ...
}
```

### De klasse {#initializing-the-class} initialiseren

Wanneer de use-klasse van `WCMUsePojo` wordt uitgebreid, wordt de initialisering uitgevoerd door de `activate` methode met voeten te treden:

### /apps/my-example/component/info/Info.java {#apps-my-example-component-info-info-java-3}

```java
...

public class Info extends WCMUsePojo {
    private String lowerCaseTitle;
    private String lowerCaseDescription;

    @Override
    public void activate() throws Exception {
        lowerCaseTitle = getProperties().get("title", "").toLowerCase();
        lowerCaseDescription = getProperties().get("description", "").toLowerCase();
    }

...

}
```

### Context {#context}

Doorgaans wordt de methode [activate](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) gebruikt om (in lidvariabelen) de waarden die nodig zijn in uw HTML-code vooraf te berekenen en op te slaan, op basis van de huidige context (bijvoorbeeld de huidige aanvraag en bron).

De klasse `WCMUsePojo` biedt toegang tot dezelfde set contextobjecten als in een HTML-bestand (zie [Algemene objecten](global-objects.md)).

In een klasse die `WCMUsePojo` uitbreidt, kunnen contextvoorwerpen door naam worden betreden gebruikend

[`<T> T get(String name, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)

U kunt ook veelgebruikte contextobjecten rechtstreeks benaderen via de juiste **goedkope methode**:

|  |  |
|---|---|
| [PageManager](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/PageManager.html) | [getPageManager()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageManager()) |
| [Pagina](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getCurrentPage()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentPage()) |
| [Pagina](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getResourcePage()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourcePage()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getPageProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageProperties()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getProperties()) |
| [Designer](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [getDesigner()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getDesigner()) |
| [Ontwerp](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Design.html) | [getCurrentDesign()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentDesign()) |
| [Stijl](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Style.html) | [getCurrentStyle()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentStyle()) |
| [Component](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/components/Component.html) | [getComponent()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getComponent()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getInheritedProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getInheritedProperties) |
| [Resource](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/Resource.html) | [getResource()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResource()) |
| [ResourceResolver](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [getResourceResolver()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourceResolver()) |
| [SlingHttpServletRequest](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [getRequest()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getRequest()) |
| [SlingHttpServletResponse](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [getResponse()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResponse()) |
| [SlingScriptHelper](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [getSlingScriptHelper()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getSlingScriptHelper()) |

### Gettermethoden {#getter-methods}

Nadat de use-class is geïnitialiseerd, wordt het HTML-bestand uitgevoerd. Tijdens dit stadium zal HTML typisch in de staat van diverse lidvariabelen van de use-klasse trekken en hen voor presentatie teruggeven.

Als u toegang tot deze waarden vanuit het HTML-bestand wilt bieden, moet u aangepaste gettermethoden definiëren in de use-klasse volgens de volgende naamgevingsconventie:

* Een methode van de vorm `getXyz` zal binnen het HTML- dossier een objecten bezit genoemd `xyz` blootstellen.

In het volgende voorbeeld leiden de methoden `getTitle` en `getDescription` ertoe dat de objecteigenschappen `title` en `description` toegankelijk worden binnen de context van het HTML-bestand:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-4}

```java
...

public class Info extends WCMUsePojo {

    ...

    public String getLowerCaseTitle() {
        return lowerCaseTitle;
    }

    public String getLowerCaseDescription() {
        return lowerCaseDescription;
    }
}
```

### data-smart-use, kenmerk {#data-sly-use-attribute}

Het `data-sly-use` attribuut wordt gebruikt om de gebruik-klasse binnen uw code te initialiseren HTML. In ons voorbeeld declareert het `data-sly-use`-kenmerk dat we de klasse `Info` willen gebruiken. We kunnen alleen de lokale naam van de klasse gebruiken, omdat we een lokale installatie gebruiken (als u het Java-bronbestand in dezelfde map als het HTML-bestand hebt geplaatst). Als we een bundel zouden installeren, zouden we de volledig gekwalificeerde klassenaam moeten opgeven.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-2}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Lokale id {#local-identifier}

De id `info` (na de punt in `data-sly-use.info`) wordt gebruikt binnen het HTML-bestand om de klasse te identificeren. Het bereik van deze id is globaal binnen het bestand, nadat deze is gedeclareerd. Het is niet beperkt tot het element dat de `data-sly-use` verklaring bevat.

### `/apps/my-example/component/info/info.html`{#apps-my-example-component-info-info-html-3}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Eigenschappen {#getting-properties} ophalen

De id `info` wordt vervolgens gebruikt om toegang te krijgen tot de objecteigenschappen `title` en `description` die via de methoden getter `Info.getTitle` en `Info.getDescription` zijn weergegeven.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-4}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Uitvoer {#output}

Als we nu toegang krijgen tot `/content/my-example.html`, wordt de volgende HTML geretourneerd:

### `view-source:http://<host>:<port>/content/my-example.html` {#view-source-http-localhost-content-my-example-html-1}

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

## Buiten de basisbeginselen {#beyond-the-basics}

In deze sectie introduceren we enkele andere functies die verder gaan dan het eenvoudige voorbeeld hierboven:

* Parameters doorgeven aan een use-klasse.
* Java-gebruiksklasse gebundeld.
* Alternatieven voor `WCMUsePojo`

### Parameters {#passing-parameters} doorgeven

Parameters kunnen bij initialisatie worden doorgegeven aan een use-klasse. We zouden bijvoorbeeld iets als dit kunnen doen:

### `/content/my-example/component/info/info.html` {#content-my-example-component-info-info-html}

```xml
<div data-sly-use.info="${'Info' @ text='Some text'}">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
    <p>${info.upperCaseText}</p>
</div>
```

Hier geven we een parameter met de naam `text` door. De use-klasse dan uppercase het koord dat wij terugwinnen en het resultaat met `info.upperCaseText` tonen. Hier is de aangepaste use-class:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-5}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {

    ...

    private String reverseText;

    @Override
    public void activate() throws Exception {

        ...

        String text = get("text", String.class);
        reverseText = new StringBuffer(text).reverse().toString();

    }

    public String getReverseText() {
        return reverseText;
    }

    ...
}
```

De parameter wordt betreden door de `WCMUsePojo` methode [`<T> T get(String paramName, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

In ons geval luidt de verklaring:

`get("text", String.class)`

De tekenreeks wordt vervolgens omgekeerd en via de methode vrijgegeven:

`getReverseText()`

### Alleen parameters doorgeven van data-SLUIT-sjabloon {#only-pass-parameters-from-data-sly-template}

Hoewel het bovenstaande voorbeeld technisch correct is, heeft het eigenlijk niet veel zin om een waarde van HTML over te gaan om een gebruiksklasse te initialiseren, wanneer de waarde in kwestie in de uitvoeringscontext van de HTML-code (of, triviaal, is de waarde statisch, zoals hierboven) beschikbaar is.

De reden is dat de use-class altijd toegang heeft tot dezelfde uitvoeringscontext als de HTML-code. Dit brengt een invoerpunt van beste praktijken op:

>[!NOTE]
>
>Het overgaan van een parameter tot een use-klasse zou slechts moeten worden gedaan wanneer de use-klasse in een `data-sly-template` dossier wordt gebruikt dat zelf van een ander HTML- dossier met parameters wordt geroepen die moeten worden overgegaan.

Laten we bijvoorbeeld een afzonderlijk `data-sly-template`-bestand maken naast ons bestaande voorbeeld. Het nieuwe bestand `extra.html` wordt aangeroepen. Het bevat een `data-sly-template` blok genoemd `extra`:

### `/apps/my-example/component/info/extra.html` {#apps-my-example-component-info-extra-html}

```xml
<template data-sly-template.extra="${@ text}"
          data-sly-use.extraHelper="${'ExtraHelper' @ text=text}">
  <p>${extraHelper.reversedText}</p>
</template>
```

De sjabloon `extra` heeft één parameter, `text`. Vervolgens wordt de Java-use-class `ExtraHelper` geïnitialiseerd met de lokale naam `extraHelper` en wordt deze de waarde van de sjabloonparameter `text` doorgegeven als de use-class-parameter `text`.

Het lichaam van het malplaatje krijgt het bezit `extraHelper.reversedText` (dat, onder de kap, eigenlijk `ExtraHelper.getReversedText()`) roept en toont die waarde.

We passen ook onze bestaande `info.html` aan om deze nieuwe sjabloon te gebruiken:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-5}

```xml
<div data-sly-use.info="Info"
     data-sly-use.extra="extra.html">

  <h1>${info.lowerCaseTitle}</h1>
  <p>${info.lowerCaseDescription}</p>

  <div data-sly-call="${extra.extra @ text=properties.description}"></div>

</div>
```

Het bestand `info.html` bevat nu twee `data-sly-use`-instructies, het oorspronkelijke bestand dat de Java-gebruiksklasse `Info` importeert en een nieuw bestand dat het sjabloonbestand onder de lokale naam `extra` importeert.

Merk op dat wij het malplaatjeblok in het `info.html` dossier konden plaatsen om tweede `data-sly-use` te vermijden, maar een afzonderlijk malplaatjedossier is gemeenschappelijker, en herbruikbaarder.

De `Info`-klasse wordt als voorheen gebruikt en roept de methoden `getLowerCaseTitle()` en `getLowerCaseDescription()` aan via hun corresponderende HTML-eigenschappen `info.lowerCaseTitle` en `info.lowerCaseDescription`.

Vervolgens voeren we een `data-sly-call` uit naar de sjabloon `extra` en geven we deze de waarde `properties.description` door als de parameter `text`.

De Java-use-class `Info.java` wordt gewijzigd en verwerkt de nieuwe tekstparameter:

### `/apps/my-example/component/info/ExtraHelper.java` {#apps-my-example-component-info-extrahelper-java}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class ExtraHelper extends WCMUsePojo {
    private String reversedText;
    ...

    @Override
    public void activate() throws Exception {
        String text = get("text", String.class);
        reversedText = new StringBuilder(text).reverse().toString();

        ...
    }

    public String getReversedText() {
        return reversedText;
    }
}
```

De parameter `text` wordt opgehaald met `get("text", String.class)`, de waarde wordt omgekeerd en beschikbaar gemaakt als HTML-object `reversedText` via de getter `getReversedText()`.

### Gebundelde Java-klasse {#bundled-java-class}

Met een bundel gebruiks-klasse moet de klasse worden gecompileerd, worden verpakt en in AEM worden opgesteld gebruikend het standaard OSGi bundelplaatsingsmechanisme. In tegenstelling tot een lokale installatie, zou de use-klasse **pakketverklaring** normaal moeten worden genoemd:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-6}

```java
package org.example.app.components;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    ...
}
```

en de instructie `data-sly-use` moet verwijzen naar de volledig gekwalificeerde klassenaam, in tegenstelling tot alleen de lokale klassenaam:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-6}

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```

### Alternatieven voor `WCMUsePojo` {#alternatives-to-wcmusepojo}

De gemeenschappelijkste manier om een Java te creëren gebruik-klasse is `WCMUsePojo` uit te breiden. Er zijn echter nog een aantal andere opties. Om deze varianten te begrijpen helpt het om te begrijpen hoe de verklaring van HTML `data-sly-use` onder de kap werkt.

Stel dat u de volgende instructie `data-sly-use` hebt:

**`<div data-sly-use.`** `localName`**`="`**`UseClass`**`">`**

Het systeem verwerkt de instructie als volgt:

(1)

* Als er een lokaal bestand `UseClass.java` in dezelfde map staat als het HTML-bestand, probeert u die klasse te compileren en te laden. Ga naar (2) als de bewerking succesvol was.
* Anders, interpreteer `UseClass` als volledig - gekwalificeerde klassennaam en probeer om het van het milieu te laden OSGi. Ga naar (2) als de bewerking succesvol was.
* Anders interpreteert u `UseClass` als een pad naar een HTML- of JavaScript-bestand en laadt u dat bestand. Indien succesvol (4).

(2)

* Probeer de huidige `Resource` aan `UseClass` aan te passen. Ga indien gelukt naar (3).
* Anders, probeer om huidige `Request` aan `UseClass` aan te passen. Ga indien gelukt naar (3).
* Anders, probeer om `UseClass` met een nul-argumentaannemer te concretiseren. Ga indien gelukt naar (3).

(3)

* Bind in HTML het zojuist aangepaste of gemaakte object aan de naam `localName`.
* Als `UseClass` [`io.sightly.java.api.Use`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html) dan roept `init` methode, die de huidige uitvoeringscontext (in de vorm van een `javax.scripting.Bindings` voorwerp) overgaat.

(4)

* Als `UseClass` een weg aan een HTML- dossier is dat `data-sly-template` bevat, bereidt het malplaatje voor.
* Als `UseClass` anders een pad naar een JavaScript-gebruiksklasse is, bereidt u de gebruiksklasse voor (zie [JavaScript Use-API](use-api-javascript.md)).

Enkele belangrijke punten van de bovenstaande beschrijving:

* Elke klasse die kan worden aangepast vanuit `Resource`, aanpasbaar vanuit `Request` of met een constructor met nulargument kan een use-klasse zijn. De klasse hoeft `WCMUsePojo` niet uit te breiden of `Use` zelfs te implementeren.
* Als de use-class *echter `Use` implementeert, wordt de `init`-methode automatisch aangeroepen met de huidige context, zodat u initialisatiecode kunt plaatsen die afhankelijk is van die context.*
* Een use-klasse die `WCMUsePojo` uitbreidt is enkel een speciaal geval van het uitvoeren van `Use`. Het verstrekt de methodes van de gemakcontext en zijn `activate` methode wordt automatisch geroepen van `Use.init`.

### Direct implementeren van interfacegebruik {#directly-implement-interface-use}

Terwijl de gemeenschappelijkste manier om een gebruik-klasse tot stand te brengen is `WCMUsePojo` uit te breiden, is het ook mogelijk om de [`io.sightly.java.api.Use`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html) interface zelf direct uit te voeren.

De `Use` interface bepaalt slechts één methode:

[`public void init(javax.script.Bindings bindings)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use#init(javax.script.Bindings))

De methode `init` wordt aangeroepen bij initialisatie van de klasse met een object `Bindings` dat alle contextobjecten en parameters bevat die worden doorgegeven aan de use-klasse.

Alle extra functionaliteit (zoals het equivalent van `WCMUsePojo.getProperties()`) moet uitdrukkelijk worden uitgevoerd gebruikend [`javax.script.Bindings`](http://docs.oracle.com/javase/7/docs/api/javax/script/Bindings.html) voorwerp. Bijvoorbeeld:

### `Info.java` {#info-java}

```java
import io.sightly.java.api.Use;

public class MyComponent implements Use {
   ...
    @Override
    public void init(Bindings bindings) {

        // All standard objects/binding are available
        Resource resource = (Resource)bindings.get("resource");
        ValueMap properties = (ValueMap)bindings.get("properties");
        ...

        // Parameters passed to the use-class are also available
        String param1 = (String) bindings.get("param1");
    }
    ...
}
```

Het belangrijkste geval voor het uitvoeren van de `Use` interface zelf in plaats van het uitbreiden `WCMUsePojo` is wanneer u wenst om een subklasse van een reeds bestaande klasse als use-klasse te gebruiken.

### Aanpasbaar vanuit bron {#adaptable-from-resource}

Een andere optie is het gebruik van een hulpklasse die kan worden aangepast vanuit `org.apache.sling.api.resource.Resource`.

Laten we zeggen dat u een HTML-script moet schrijven dat het mimetype van een DAM-element weergeeft. In dit geval weet u dat wanneer uw HTML- manuscript wordt geroepen, het binnen de context van `Resource` zal zijn die JCR `Node` met nodetype `dam:Asset` verpakt.

U weet dat een `dam:Asset` knoop een structuur als dit heeft:

### Structuur opslagplaats {#repository-structure}

```java
{
  "content": {
    "dam": {
      "geometrixx": {
        "portraits": {
          "jane_doe.jpg": {
            ...
          "jcr:content": {
            ...
            "metadata": {
              ...
            },
            "renditions": {
              ...
              "original": {
                ...
                "jcr:content": {
                  "jcr:primaryType": "nt:resource",
                  "jcr:lastModifiedBy": "admin",
                  "jcr:mimeType": "image/jpeg",
                  "jcr:lastModified": "Fri Jun 13 2014 15:27:39 GMT+0200",
                  "jcr:data": ...,
                  "jcr:uuid": "22e3c598-4fa8-4c5d-8b47-8aecfb5de399"
                }
              },
              "cq5dam.thumbnail.319.319.png": {
                  ...
              },
              "cq5dam.thumbnail.48.48.png": {
                  ...
              },
              "cq5dam.thumbnail.140.100.png": {
                  ...
              }
            }
          }  
        }
      }
    }
  }
}
```

Hier wordt het element (een JPEG-afbeelding) weergegeven dat wordt geleverd bij een standaardinstallatie van AEM als onderdeel van de geometrixx van het voorbeeldproject. Het element wordt `jane_doe.jpg` genoemd en zijn mimetype is `image/jpeg`.

Als u toegang wilt krijgen tot het element vanuit HTML, kunt u [`com.day.cq.dam.api.Asset`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/granite/asset/api/Asset.html) declareren als de klasse in de instructie `data-sly-use` en vervolgens een methode get van `Asset` gebruiken om de gewenste informatie op te halen. Bijvoorbeeld:

### `mimetype.html` {#mimetype-html}

```xml
<div data-sly-use.asset="com.day.cq.dam.api.Asset">
  <p>${asset.mimeType}</p>
</div>
```

De instructie `data-sly-use` geeft HTML de opdracht om de huidige `Resource` aan te passen aan een `Asset` en deze de lokale naam `asset` te geven. Vervolgens wordt de methode `getMimeType` van `Asset` aangeroepen met behulp van de HTL getter shorthand: `asset.mimeType`.

### Aanpasbaar van aanvraag {#adaptable-from-request}

Het is ook mogelijk om als use-klasse om het even welke klasse aan te passen die van [`org.apache.sling.api.SlingHttpServletRequest`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) aanpasbaar is

Net als bij het bovenstaande geval van een gebruiksklasse die kan worden aangepast vanuit `Resource`, kan een gebruiksklasse die kan worden aangepast vanuit [`SlingHttpServletRequest`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) worden opgegeven in de instructie `data-sly-use`. Na uitvoering wordt het huidige verzoek aangepast aan de opgegeven klasse en wordt het resulterende object beschikbaar gesteld in HTML.
