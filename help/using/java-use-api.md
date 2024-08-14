---
title: HTML Java Use-API
description: Met de HTML Java Use-API kan een HTML-bestand toegang krijgen tot hulplijnmethoden in een aangepaste Java-klasse.
exl-id: 9a9a2bf8-d178-4460-a3ec-cbefcfc09959
source-git-commit: 5e1dce693dc61300530837c996f45d793c0b07e6
workflow-type: tm+mt
source-wordcount: '1135'
ht-degree: 0%

---


# HTML Java Use-API {#htl-java-use-api}

Met de HTML Java Use-API kan een HTML-bestand toegang krijgen tot hulplijnmethoden in een aangepaste Java-klasse.

## Hoofdletters gebruiken {#use-case}

Met de HTML Java Use-API kan een HTML-bestand via `data-sly-use` toegang krijgen tot hulplijnmethoden in een aangepaste Java-klasse. Met deze methode kunnen alle complexe bedrijfslogica worden ingekapseld in de Java-code, terwijl de HTML-code alleen betrekking heeft op directe opmaakproductie.

Een Java Use-API-object kan een eenvoudige POJO zijn, geïnstantieerd door een bepaalde implementatie via de standaardconstructor van de POJO.

Met de API-API-POJO&#39;s kunt u ook een openbare methode, init genoemd, met de volgende handtekening toegankelijk maken:

```java
    /**
     * Initializes the Use bean.
     *
     * @param bindings All bindings available to the HTL scripts.
     **/
    public void init(javax.script.Bindings bindings);
```

De `bindings` -kaart kan objecten bevatten die context bieden voor het momenteel uitgevoerde HTML-script dat het Use-API-object kan gebruiken voor de verwerking ervan.

## Een eenvoudig voorbeeld {#a-simple-example}

In dit voorbeeld wordt het gebruik van de Use-API geïllustreerd.

>[!NOTE]
>
>Dit voorbeeld is vereenvoudigd om het gebruik ervan te illustreren. In een productiemilieu, adviseert de Adobe dat u [ het Verdelen modellen ](https://sling.apache.org/documentation/bundles/models.html) gebruikt.

Begin met een HTML-component, `info,` genoemd, die geen use-klasse heeft. Het bestaat uit één bestand, `/apps/my-example/components/info.html`

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

Voeg inhoud voor deze component toe die moet worden gerenderd bij `/content/my-example/` :

```xml
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

Wanneer deze inhoud wordt benaderd, wordt het HTML-bestand uitgevoerd. Binnen de HTML-code wordt het contextobject `properties` gebruikt om de `title` en `description` van de huidige bron te openen en weer te geven. Het uitvoerbestand `/content/my-example.html` is als volgt:

```html
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Een Use-Class toevoegen {#adding-a-use-class}

De `info` -component in zijn huidige vorm heeft geen use-klasse nodig om zijn eenvoudige functie uit te voeren. Er zijn echter gevallen waarin u dingen moet doen die niet in HTML kunnen worden gedaan en dus een gebruiksklasse nodig hebt. Houd echter rekening met het volgende:

>[!NOTE]
>
>Een gebruiksklasse mag alleen worden gebruikt wanneer er iets niet alleen in HTML kan worden gedaan.

Stel dat u de component `info` de eigenschappen `title` en `description` van de bron wilt laten weergeven, maar allemaal in kleine letters. Omdat HTML geen methode heeft voor het verlagen van tekenreeksen, kunt u een Java-gebruiksklasse toevoegen en `/apps/my-example/component/info/info.html` als volgt wijzigen:

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

Daarnaast wordt `/apps/my-example/component/info/Info.java` gemaakt.

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

Gelieve te zien de [ documenten van Java voor `com.adobe.cq.sightly.WCMUsePojo` ](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) voor meer details.

Laten we nu eens kijken naar de verschillende delen van de code.

### Lokale vs. Bundle Java Class {#local-vs-bundle-java-class}

De Java-gebruiksklasse kan op twee manieren worden geïnstalleerd:

* **Lokaal** - in lokale installatie, wordt het brondossier van Java geplaatst naast het HTML- dossier, in de zelfde bewaarplaatsomslag. De bron wordt automatisch gecompileerd op bestelling. Er is geen afzonderlijke compilatie of verpakkingsstap vereist.
* **Bundel** - in een bundel installeert, moet de klasse van Java binnen een bundel worden gecompileerd en worden opgesteld OSGi gebruikend het standaard AEM plaatsingsmechanisme van de bundel (zie de sectie [ Gebundelde Klasse van Java ](#bundled-java-class)).

Houd rekening met de volgende twee punten om te weten welke methode u moet gebruiken wanneer:

* A **lokale Java gebruik-klasse** wordt geadviseerd wanneer gebruik-klasse voor de component in kwestie specifiek is.
* A **bundel Java gebruik-klasse** wordt geadviseerd wanneer de code van Java een dienst uitvoert die van veelvoudige componenten HTML wordt betreden.

In dit voorbeeld wordt een lokale installatie gebruikt.

### Java Package is Repository Path {#java-package-is-repository-path}

Wanneer u een lokale installatie gebruikt, moet de pakketnaam van de use-klasse overeenkomen met de locatie van de opslagplaats. Onderstrepingstekens in de pakketnaam vervangen eventuele koppeltekens in het pad.

In dit geval bevindt `Info.java` zich bij `/apps/my-example/components/info` zodat het pakket `apps.my_example.components.info` is:

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {

   ...

}
```

>[!NOTE]
>
>Het gebruik van koppeltekens in de namen van opslagplaats-items wordt aanbevolen bij AEM ontwikkeling. Afbreekstreepjes zijn echter niet toegestaan in Java-pakketnamen. Om deze reden, **moeten alle koppeltekens in de opslagweg in onderstrepingstekens in de pakketnaam** worden omgezet.

### Uitbreiden `WCMUsePojo` {#extending-wcmusepojo}

Hoewel er verschillende manieren zijn om een Java-klasse op te nemen in HTML, is het eenvoudigst om de `WCMUsePojo` -klasse uit te breiden. In dit voorbeeld `/apps/my-example/component/info/Info.java` :

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo

    ...
}
```

### De klasse initialiseren {#initializing-the-class}

Wanneer de use-class vanaf `WCMUsePojo` wordt uitgebreid, wordt initialisatie uitgevoerd door de `activate` -methode te overschrijven, in dit geval in `/apps/my-example/component/info/Info.java`

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

Typisch, activeert [ ](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) methode wordt gebruikt om (in lidvariabelen) de waarden vooraf samen te stellen en op te slaan nodig in uw code HTML, die op de huidige context (het huidige verzoek en het middel, bijvoorbeeld) wordt gebaseerd.

De `WCMUsePojo` klasse verleent toegang tot de zelfde reeks contextvoorwerpen zoals beschikbaar binnen een HTML- dossier (zie het document [ Globale Voorwerpen ](global-objects.md).)

In een klasse die `WCMUsePojo` uitbreidt, kunt u tot contextvoorwerpen toegang hebben gebruikend hun namen:

[`<T> T get(String name, Class<T> type)`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)

U kunt ook rechtstreeks toegang krijgen tot veelgebruikte contextobjecten met de in deze tabel vermelde handige methode.

| Object | Handigheidmethode |
|---|---|
| [`PageManager`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/PageManager.html) | [`getPageManager()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getPageManager--) |
| [`Page`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/Page.html) | [`getCurrentPage()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getCurrentPage--) |
| [`Page`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/Page.html) | [`getResourcePage()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResourcePage--) |
| [`ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) | [`getPageProperties()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getPageProperties--) |
| [`ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) | [`getProperties()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getProperties--) |
| [`Designer`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [`getDesigner()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getDesigner--) |
| [`Design`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/designer/Design.html) | [`getCurrentDesign()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getCurrentDesign--) |
| [`Style`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/designer/Style.html) | [`getCurrentStyle()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getCurrentStyle--) |
| [`Component`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/components/Component.html) | [`getComponent()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getComponent--) |
| [`ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) | [`getInheritedProperties()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getInheritedPageProperties--) |
| [`Resource`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/Resource.html) | [`getResource()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResource--) |
| [`ResourceResolver`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [`getResourceResolver()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResourceResolver--) |
| [`SlingHttpServletRequest`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [`getRequest()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getRequest--) |
| [`SlingHttpServletResponse`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [`getResponse()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResponse--) |
| [`SlingScriptHelper`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [`getSlingScriptHelper()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getSlingScriptHelper--) |

### Getter-methoden {#getter-methods}

Nadat de use-class is geïnitialiseerd, wordt het HTML-bestand uitgevoerd. Tijdens dit stadium, trekt HTML typisch in de staat van diverse lidvariabelen van de use-klasse en geeft hen voor presentatie terug.

Als u vanuit het HTML-bestand toegang tot deze waarden wilt bieden, moet u aangepaste gettermethoden definiëren in de use-klasse volgens de volgende naamgevingsconventie:

* Een methode van het formulier `getXyz` maakt in het HTML-bestand bekend dat een objecteigenschap `xyz` wordt genoemd.

In het volgende voorbeeldbestand `/apps/my-example/component/info/Info.java` leiden de methoden `getTitle` en `getDescription` ertoe dat de objecteigenschappen `title` en `description` toegankelijk worden binnen de context van het HTML-bestand.

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

### `data-sly-use` Kenmerk {#data-sly-use-attribute}

Het kenmerk `data-sly-use` wordt gebruikt om de use-class in uw HTML-code te initialiseren. In het voorbeeld declareert het kenmerk `data-sly-use` dat de klasse `Info` wordt gebruikt. U kunt alleen de lokale naam van de klasse gebruiken omdat u een lokale installatie gebruikt (nadat u het Java-bronbestand in dezelfde map als het HTML-bestand hebt geplaatst). Als u een bundelinstallatie gebruikte, moet u de volledig gekwalificeerde klassenaam opgeven.

Let op het gebruik in dit `/apps/my-example/component/info/info.html` voorbeeld.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Lokale id {#local-identifier}

De id `info` (na de punt in `data-sly-use.info` ) wordt gebruikt in het HTML-bestand om de klasse te identificeren. Het bereik van deze id is globaal binnen het bestand, nadat deze is gedeclareerd. Het is niet beperkt tot het element dat de instructie `data-sly-use` bevat.

Let op het gebruik in dit `/apps/my-example/component/info/info.html` voorbeeld.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Eigenschappen ophalen {#getting-properties}

De id `info` wordt vervolgens gebruikt om toegang te krijgen tot de objecteigenschappen `title` en `description` die via de methoden getter `Info.getTitle` en `Info.getDescription` werden weergegeven.

Let op het gebruik in dit `/apps/my-example/component/info/info.html` voorbeeld.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Uitvoer {#output}

Wanneer `/content/my-example.html` nu wordt benaderd, wordt het volgende `/content/my-example.html` -bestand geretourneerd.

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

>[!NOTE]
>
>Dit voorbeeld is vereenvoudigd om het gebruik ervan te illustreren. In een productiemilieu, adviseert de Adobe dat u [ het Verdelen modellen ](https://sling.apache.org/documentation/bundles/models.html) gebruikt.

## Buiten de basisbeginselen {#beyond-the-basics}

Deze sectie introduceert enkele extra functies die verder gaan dan het eerder beschreven voorbeeld.

* Parameters doorgeven aan een use-klasse
* Java-gebruiksklasse gebundeld

### Parameters doorgeven {#passing-parameters}

Parameters kunnen bij initialisatie worden doorgegeven aan een use-klasse.

Voor details, gelieve te verwijzen naar de het Verdelen [ documentatie van de Motor van HTML Scripting ](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#passing-parameters-to-java-use-objects).

### Gebundelde Java-klasse {#bundled-java-class}

Met een gebundelde gebruiks-klasse moet de klasse worden gecompileerd, worden verpakt, en in AEM worden opgesteld gebruikend het standaard OSGi mechanisme van de bundelplaatsing. In tegenstelling tot een lokale installatie, zou de use-class pakketverklaring normaal zoals in dit `/apps/my-example/component/info/Info.java` voorbeeld moeten worden genoemd.

```java
package org.example.app.components;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    ...
}
```

En de instructie `data-sly-use` moet verwijzen naar de volledig gekwalificeerde klassenaam, in tegenstelling tot alleen de lokale klassenaam zoals in dit `/apps/my-example/component/info/info.html` -voorbeeld.

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```
