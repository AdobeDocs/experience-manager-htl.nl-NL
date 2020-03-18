---
title: HTML Java Use-API
seo-title: HTML Java Use-API
description: 'De Java Use-API van HTL (de HTML-sjabloontaal) biedt een HTL-bestand toegang tot helpermethoden in een aangepaste Java-klasse. '
seo-description: 'De Java Use-API van HTL (de HTML-sjabloontaal) biedt een HTL-bestand toegang tot helpermethoden in een aangepaste Java-klasse. '
uuid: b340f8f7-a193-45c8-aa39-5c6e2c0194ea
contentOwner: User
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: reference
discoiquuid: 126ebc9d-5f7b-47a4-aea2-c8840d34864c
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 6de5ed20e4463c0c2e804e24cb853336229a7c1f

---


# HTML Java Use-API{#htl-java-use-api}

Met de HTML Template Language (HTL) Java Use-API kan een HTML-bestand helpermethoden benaderen in een aangepaste Java-klasse. Hierdoor kunnen alle complexe bedrijfslogica worden ingekapseld in de code van Java, terwijl de code HTML slechts op directe prijsverhogingsproductie behandelt.

## Een eenvoudig voorbeeld {#a-simple-example}

We beginnen met een HTML-component die *geen* use-class heeft. Het bestaat uit één bestand, `/apps/my-example/components/info.html`

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html}

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

Er wordt ook inhoud voor deze component toegevoegd die moet worden gerenderd bij **`/content/my-example/`**:

### `http://localhost:4502/content/my-example.json` {#http-localhost-content-my-example-json}

```java
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

Wanneer deze inhoud wordt benaderd, wordt het HTML-bestand uitgevoerd. Binnen de HTML-code gebruiken we het contextobject **`properties`** om toegang te krijgen tot de huidige bron `title` `description` en deze weer te geven. De HTML-uitvoer is:

### `view-source:http://localhost:4502/content/my-example.html` {#view-source-http-localhost-content-my-example-html}

```xml
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Een Use-Class toevoegen {#adding-a-use-class}

De **info** -component in zijn huidige vorm heeft geen use-class nodig om zijn (zeer eenvoudige) functie uit te voeren. Er zijn echter gevallen waarin u dingen moet doen die niet in HTML kunnen worden gedaan en dus een gebruiksklasse nodig hebt. Houd echter rekening met het volgende:

>[!NOTE]
>
>*Een gebruiksklasse mag alleen worden gebruikt wanneer er iets niet alleen in HTML kan worden gedaan.*

Stel dat u wilt dat de `info` component de `title` en **`description`** eigenschappen van de bron weergeeft, maar allemaal in kleine letters. Aangezien HTML geen methode voor het verlagen van tekenreeksen heeft, hebt u een use-klasse nodig. Dit kunnen we doen door een Java-gebruiksklasse toe te voegen en de klasse **`info.html`** als volgt te wijzigen:

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

### Lokale vs Bundle Java-klasse {#local-vs-bundle-java-class}

De Java-gebruiksklasse kan op twee manieren worden geïnstalleerd: **lokaal** of **bundel**. *In dit voorbeeld wordt een lokale installatie gebruikt.*

In een lokale installatie wordt het Java-bronbestand naast het HTML-bestand in dezelfde opslagmap geplaatst. De bron wordt automatisch gecompileerd op bestelling. Er is geen afzonderlijke compilatie of verpakkingsstap vereist.

In een bundelinstallatie, moet de klasse van Java binnen een bundel OSGi worden gecompileerd en worden opgesteld gebruikend het standaard AEM bundelplaatsingsmechanisme (zie [Gebundelde Klasse](#bundled-java-class)van Java).

>[!NOTE]
>
>Een **lokale Java-gebruiksklasse** wordt aanbevolen wanneer de use-klasse specifiek is voor de desbetreffende component.
>
>Een **bundel Java-use-class** wordt aanbevolen wanneer de Java-code een service implementeert die wordt benaderd vanuit meerdere HTML-componenten.

### Java-pakket is opslagpad {#java-package-is-repository-path}

Wanneer een lokale installatie wordt gebruikt, moet de pakketnaam van de use-klasse overeenkomen met die van de opslagplaats, waarbij eventuele koppeltekens in het pad worden vervangen door onderstrepingstekens in de pakketnaam.

In dit geval **`Info.java`** bevindt zich op **`/apps/my-example/components/info`** de plaats van het pakket **`apps.my_example.components.info`** :

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
>Het gebruik van afbreekstreepjes in de namen van opslagplaats-items wordt aanbevolen bij de ontwikkeling van AEM. Afbreekstreepjes zijn echter niet toegestaan in Java-pakketnamen. Daarom moeten **alle afbreekstreepjes in het opslagplaatsingspad worden geconverteerd naar onderstrepingstekens in de pakketnaam**.

### Uitbreiding `WCMUsePojo`{#extending-wcmusepojo}

Hoewel er verschillende manieren zijn om een Java-klasse op te nemen in HTML (zie Alternatieven voor `WCMUsePojo`), is het eenvoudigst om de `WCMUsePojo` klasse uit te breiden:

#### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-2}

```java
package apps.my_example.components.info;
 
import com.adobe.cq.sightly.WCMUsePojo;
 
public class Info extends WCMUsePojo {
    
    ...
}
```

### De klasse initialiseren {#initializing-the-class}

Wanneer de use-klasse van **`WCMUsePojo`** wordt uitgebreid, wordt de initialisering uitgevoerd door de **`activate`** methode met voeten te treden:

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

Typisch, wordt de [activeringsmethode gebruikt](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html) om (in lidvariabelen) de waarden vooraf te berekenen en op te slaan nodig in uw code HTML, die op de huidige context (het huidige verzoek en middel, bijvoorbeeld) wordt gebaseerd.

De `WCMUsePojo` klasse biedt toegang tot dezelfde set contextobjecten als in een HTML-bestand (zie [Algemene objecten](global-objects.md)).

In een klasse die **`WCMUsePojo`** is uitgebreid, kunnen contextobjecten *via de naam* worden benaderd

[`<T> T get(String name, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

U kunt ook veelgebruikte contextobjecten rechtstreeks benaderen met de juiste **gebruiksmethode**:

|  |  |
|---|---|
| [PageManager](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/PageManager.html) | [getPageManager()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageManager()) |
| [Pagina](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getCurrentPage()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentPage()) |
| [Pagina](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getResourcePage()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourcePage()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getPageProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageProperties()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getProperties()) |
| [Designer](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [getDesigner()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getDesigner()) |
| [Ontwerp](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Design.html) | [getCurrentDesign()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentDesign()) |
| [Stijl](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Style.html) | [getCurrentStyle()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentStyle()) |
| [Component](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/components/Component.html) | [getComponent()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getComponent()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getInheritedProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse#getInheritedProperties.html()) |
| [Resource](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/Resource.html) | [getResource()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResource()) |
| [ResourceResolver](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [getResourceResolver()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourceResolver()) |
| [SlingHttpServletRequest](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [getRequest()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getRequest()) |
| [SlingHttpServletResponse](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [getResponse()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResponse()) |
| [SlingScriptHelper](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [getSlingScriptHelper()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getSlingScriptHelper()) |

### Methoden van Getter {#getter-methods}

Nadat de use-class is geïnitialiseerd, wordt het HTML-bestand uitgevoerd. Tijdens dit stadium zal HTML typisch in de staat van diverse lidvariabelen van de use-klasse trekken en hen voor presentatie teruggeven.

Als u toegang tot deze waarden vanuit het HTML-bestand wilt bieden, moet u aangepaste gettermethoden definiëren in de use-class **volgens de volgende naamgevingsconventie**:

* Een methode van het formulier **`getXyz`** maakt in het HTML-bestand een objecteigenschap met de naam ***xyz*** beschikbaar.

In het volgende voorbeeld resulteren de methoden **`getTitle`** en **`getDescription`** er bijvoorbeeld in dat de objecteigenschappen toegankelijk **`title`** **`description`** worden binnen de context van het HTML-bestand:

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

Het **`data-sly-use`** attribuut wordt gebruikt om de use-klasse binnen uw code te initialiseren HTML. In ons voorbeeld declareert het `data-sly-use` kenmerk dat we de klasse willen gebruiken **`Info`**. We kunnen alleen de lokale naam van de klasse gebruiken, omdat we een lokale installatie gebruiken (als u het Java-bronbestand in dezelfde map als het HTML-bestand hebt geplaatst). Als we een bundel zouden gebruiken, zouden we de volledig gekwalificeerde klassenaam moeten opgeven (zie Bundle-installatie [van](#LocalvsBundleJavaClass)gebruiksklasse).

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-2}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Lokale id {#local-identifier}

De id &#39;**`info`**&#39; (na de punt in **`data-sly-use.info`**) wordt gebruikt in het HTML-bestand om de klasse te identificeren. Het bereik van deze id is globaal binnen het bestand, nadat deze is gedeclareerd. Het is niet beperkt tot het element dat de `data-sly-use` instructie bevat.

### `/apps/my-example/component/info/info.html`{#apps-my-example-component-info-info-html-3}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Eigenschappen ophalen {#getting-properties}

De id `info` wordt vervolgens gebruikt om toegang te krijgen tot de objecteigenschappen **`title`** en **`description`** die via de methoden getter `Info.getTitle` en **`Info.getDescription`** beschikbaar zijn gemaakt.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-4}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Output {#output}

Wanneer we nu toegang krijgen, retourneert **`/content/my-example.html`** dit de volgende HTML:

### `view-source:http://localhost:4502/content/my-example.html` {#view-source-http-localhost-content-my-example-html-1}

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

## Buiten de basisbeginselen {#beyond-the-basics}

In deze sectie zullen we een aantal andere functies toevoegen die verder gaan dan het eenvoudige voorbeeld hierboven:

* Parameters doorgeven aan een use-klasse.
* Java-gebruiksklasse gebundeld.
* Alternatieven voor `WCMUsePojo`

### Parameters doorgeven {#passing-parameters}

Parameters kunnen bij initialisatie worden doorgegeven aan een use-klasse. We zouden bijvoorbeeld iets als dit kunnen doen:

### `/content/my-example/component/info/info.html` {#content-my-example-component-info-info-html}

```xml
<div data-sly-use.info="${'Info' @ text='Some text'}">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
    <p>${info.upperCaseText}</p>
</div>
```

Hier geven we een parameter door die we **`text`** noemen. De use-klasse dan uppercase het koord wij terugwinnen en tonen het resultaat met `info.upperCaseText`. Hier is de aangepaste use-class:

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

De parameter is toegankelijk via de `WCMUsePojo` methode

[ `<T> T get(String paramName, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

In ons geval is de verklaring

`get("text", String.class)`

De tekenreeks wordt vervolgens omgekeerd en via de methode weergegeven

**`getReverseText()`**

### Alleen parameters doorgeven van data-smart-template {#only-pass-parameters-from-data-sly-template}

Hoewel het bovenstaande voorbeeld technisch correct is, heeft het eigenlijk niet veel zin om een waarde van HTML over te gaan om een gebruiksklasse te initialiseren, wanneer de waarde in kwestie in de uitvoeringscontext van de HTML-code (of, triviaal, is de waarde statisch, zoals hierboven) beschikbaar is.

De reden is dat de use-class altijd toegang heeft tot dezelfde uitvoeringscontext als de HTML-code. Dit brengt een invoerpunt van beste praktijken op:

>[!NOTE]
>
>Het overgaan van een parameter tot een use-klasse zou slechts moeten worden gedaan wanneer de use-klasse in een **gegeven-slim-malplaatjedossier** wordt gebruikt dat zelf van een ander HTML- dossier met parameters wordt geroepen die moeten worden overgegaan.

Laten we bijvoorbeeld een afzonderlijk `data-sly-template` bestand maken naast ons bestaande voorbeeld. We bellen het nieuwe bestand `extra.html`. Het bevat een **`data-sly-template`** blok genoemd **`extra`**:

### `/apps/my-example/component/info/extra.html` {#apps-my-example-component-info-extra-html}

```xml
<template data-sly-template.extra="${@ text}"
          data-sly-use.extraHelper="${'ExtraHelper' @ text=text}">
  <p>${extraHelper.reversedText}</p>
</template>
```

De sjabloon **`extra`** neemt één parameter, **`text`**. Vervolgens wordt de Java-use-class geïnitialiseerd `ExtraHelper` met de lokale naam **`extraHelper`** en wordt de waarde van de sjabloonparameter doorgegeven **`text`** als de use-class-parameter **`text`**.

Het lichaam van het malplaatje krijgt het bezit `extraHelper.reversedText` (dat, onder de kap, eigenlijk roept `ExtraHelper.getReversedText()`) en toont die waarde.

We passen ook onze bestaande sjabloon aan **`info.html`** om deze nieuwe sjabloon te gebruiken:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-5}

```xml
<div data-sly-use.info="Info" 
     data-sly-use.extra="extra.html">
    
  <h1>${info.lowerCaseTitle}</h1>
  <p>${info.lowerCaseDescription}</p>
    
  <div data-sly-call="${extra.extra @ text=properties.description}"></div>

</div>
```

Het bestand bevat `info.html` nu twee **`data-sly-use`** instructies, het oorspronkelijke bestand dat de **`Info`** Java-gebruiksklasse importeert en een nieuw bestand dat het sjabloonbestand onder de lokale naam importeert `extra`.

We hadden het sjabloonblok in het **`info.html`** bestand kunnen plaatsen om het tweede te voorkomen, **`data-sly-use`** maar een afzonderlijk sjabloonbestand komt vaker voor en is herbruikbaarder.

De **`Info`** klasse wordt als voorheen gebruikt en roept de methoden getter aan **`getLowerCaseTitle()`** en `getLowerCaseDescription()` via de overeenkomende HTML-eigenschappen `info.lowerCaseTitle` en - **`info.lowerCaseDescription`**.

Dan voeren wij een **`data-sly-call`** aan het malplaatje uit **`extra`** en overgaan het de waarde `properties.description` als parameter **`text`**.

De Java-gebruiksklasse `Info.java` is gewijzigd en verwerkt de nieuwe tekstparameter:

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

De **`text`** parameter wordt opgehaald met **`get("text", String.class)`**, de waarde wordt omgekeerd en beschikbaar gemaakt als het HTML-object `reversedText` via de getter `getReversedText()`.

### Gebundelde Java-klasse {#bundled-java-class}

Met een bundel gebruiks-klasse moet de klasse worden gecompileerd, worden verpakt en in AEM worden opgesteld gebruikend het standaard OSGi bundelplaatsingsmechanisme. In tegenstelling tot een lokale installatie, zou de gebruik-klasse **pakketverklaring** normaal moeten worden genoemd:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-6}

```java
package org.example.app.components;
 
import com.adobe.cq.sightly.WCMUsePojo;
 
public class Info extends WCMUsePojo {
    ...
}
```

en de `data-sly-use` instructie moet verwijzen naar de *volledig gekwalificeerde klassenaam*, in tegenstelling tot alleen de lokale klassenaam:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-6}

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```

### Alternatieven voor `WCMUsePojo`{#alternatives-to-wcmusepojo}

De meest gebruikelijke manier om een Java-gebruiksklasse te maken is uitbreiden `WCMUsePojo`. Er zijn echter nog een aantal andere opties. Om deze varianten te begrijpen, helpt het om te begrijpen hoe de HTML- `data-sly-use` instructie onder de kap werkt.

Stel dat u de volgende `data-sly-use` instructie hebt:

**`<div data-sly-use.`** `localName`**`="`** `UseClass`**`">`**

Het systeem verwerkt de instructie als volgt:

(1)

* Als er een lokaal bestand *UseClass.java* in dezelfde map staat als het HTML-bestand, probeert u die klasse te compileren en te laden. Ga naar (2) als de bewerking succesvol was.

* Anders, interpreteer *UseClass* als volledig - gekwalificeerde klassennaam en probeer om het van het milieu te laden OSGi. Ga naar (2) als de bewerking succesvol was.
* Anders interpreteert u *UseClass* als een pad naar een HTML- of JavaScript-bestand en laadt u dat bestand. Indien succesvol (4).

(2)

* Probeer de huidige **`Resource`** instelling aan te passen naar *`UseClass.`* Indien geslaagd, ga naar (3).

* Anders kunt u proberen de huidige instelling aan te passen **`Request`** aan *`UseClass`*. Ga indien gelukt naar (3).

* Probeer anders *`UseClass`* met een nulargumentconstructor te instantiëren. Ga indien gelukt naar (3).

(3)

* Bind in HTML het nieuwe aangepaste of gemaakte object aan de naam *`localName`*.
* Als *`UseClass`* implementeert de methode [ vervolgens aanroepen, wordt de huidige uitvoeringscontext doorgegeven (in de vorm van een `io.sightly.java.api.Use`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html) `init` `javax.scripting.Bindings` object).

(4)

* Als *`UseClass`* een pad is naar een HTML-bestand met een `data-sly-template`sjabloon, bereidt u de sjabloon voor.

* Als dit een pad naar een JavaScript-gebruiksklasse *`UseClass`* is, bereidt u de gebruiksklasse voor (zie [JavaScript Use-API](use-api-javascript.md)).

Enkele belangrijke punten van de bovenstaande beschrijving:

* Elke klasse die kan worden aangepast vanuit `Resource`, kan kunnen worden aangepast vanuit `Request`of met een constructor met het argument nul, kan een use-klasse zijn. De klasse hoeft niet uit te breiden `WCMUsePojo` of te implementeren `Use`.

* Nochtans, als de use-klasse *uitvoert* `Use`, dan zal zijn **`init`** methode automatisch met de huidige context worden geroepen, toelatend u om initialiseringscode daar te plaatsen die van die context afhangt.

* Een gebruiksklasse die zich uitbreidt `WCMUsePojo` is slechts een speciaal geval van implementatie **`Use`**. Het verstrekt de methodes van de gemakcontext en zijn **`activate`** methode wordt automatisch geroepen van `Use.init`.

### Interfacegebruik rechtstreeks implementeren {#directly-implement-interface-use}

Hoewel de gemeenschappelijkste manier om een gebruik-klasse tot stand te brengen is uit te breiden `WCMUsePojo`, is het ook mogelijk om de `[io.sightly.java.api.Use](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html)`interface zelf direct uit te voeren.

De `Use` interface definieert slechts één methode:

`[public void init(javax.script.Bindings bindings)](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use#init(javax.script.Bindings))`

De **`init`** methode wordt aangeroepen bij initialisatie van de klasse met een **`Bindings`** object dat alle contextobjecten en parameters bevat die worden doorgegeven aan de use-klasse.

Alle extra functionaliteit (zoals het equivalent van `WCMUsePojo.getProperties()`) moet expliciet worden geïmplementeerd met behulp van het ` [javax.script.Bindings](http://docs.oracle.com/javase/7/docs/api/javax/script/Bindings.html)` object. Bijvoorbeeld:

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

Een andere mogelijkheid is om een hulpklasse te gebruiken die kan worden aangepast van **`org.apache.sling.api.resource.Resource`**.

Laten we zeggen dat u een HTML-script moet schrijven dat het mimetype van een DAM-element weergeeft. In dit geval weet u dat wanneer uw HTML-script wordt aangeroepen, dit zich binnen de context van een script bevindt **`Resource`** dat een JCR **`Node`** met een notdetype omsluit **`dam:Asset`**.

U weet dat een **`dam:Asset`** knooppunt een dergelijke structuur heeft:

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

Hier wordt het element (een JPEG-afbeelding) weergegeven dat wordt geleverd bij een standaardinstallatie van AEM als onderdeel van de geometrixx van het voorbeeldproject. Het element wordt aangeroepen **`jane_doe.jpg`** en het bijbehorende mimetype is **`image/jpeg`**.

Als u het element wilt openen vanuit HTML, kunt u declareren ` [com.day.cq.dam.api.Asset](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/granite/asset/api/Asset.html)` als de klasse in de **`data-sly-use`** instructie: en gebruik dan een methode van krijgen om de gewenste informatie **`Asset`** terug te winnen. Bijvoorbeeld:

### `mimetype.html` {#mimetype-html}

```xml
<div data-sly-use.asset="com.day.cq.dam.api.Asset">
  <p>${asset.mimeType}</p>
</div>
```

De `data-sly-use` instructie geeft HTML de opdracht de huidige **`Resource`** aan een **`Asset`** en geeft deze de lokale naam **`asset`**. Vervolgens wordt de **`getMimeType`** methode aangeroepen voor het `Asset` gebruik van de HTL getter shorthand: `asset.mimeType`.

### Aanpasbaar van aanvraag {#adaptable-from-request}

Het is ook mogelijk om een klasse die kan worden aangepast vanuit een gebruiksklasse te gebruiken **` [org.apache.sling.api.SlingHttpServletRequest](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html)`**

Net als bij het bovenstaande geval van een gebruiksklasse die kan worden aangepast vanuit `Resource`, kan een gebruiksklasse die kan worden aangepast vanuit [`SlingHttpServletRequest`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) de `data-sly-use` instructie worden opgegeven. Na uitvoering wordt het huidige verzoek aangepast aan de opgegeven klasse en wordt het resulterende object beschikbaar gesteld in HTML.
