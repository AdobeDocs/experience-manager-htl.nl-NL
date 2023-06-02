---
title: HTML Java Use-API
description: Met de HTML Java Use-API kan een HTML-bestand toegang krijgen tot hulplijnmethoden in een aangepaste Java-klasse.
exl-id: 9a9a2bf8-d178-4460-a3ec-cbefcfc09959
source-git-commit: 88edbd2fd66de960460df5928a3b42846d32066b
workflow-type: tm+mt
source-wordcount: '1510'
ht-degree: 0%

---


# HTML Java Use-API {#htl-java-use-api}

Met de HTML Java Use-API kan een HTML-bestand toegang krijgen tot hulplijnmethoden in een aangepaste Java-klasse.

## Hoofdletters gebruiken {#use-case}

Met de HTML Java Use-API kan een HTML-bestand via `data-sly-use`. Hierdoor kunnen alle complexe bedrijfslogica worden ingekapseld in de code van Java, terwijl de code HTML slechts op directe prijsverhogingsproductie behandelt.

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

De `bindings` De kaart kan voorwerpen bevatten die context aan het momenteel uitgevoerde manuscript van HTML verstrekken dat het gebruik-API voorwerp voor zijn verwerking kan gebruiken.

## Een eenvoudig voorbeeld {#a-simple-example}

In dit voorbeeld wordt het gebruik van de Use-API geïllustreerd.

>[!NOTE]
>
>Dit voorbeeld is vereenvoudigd om het gebruik ervan eenvoudig te illustreren. In een productieomgeving wordt aanbevolen [Sling-modellen.](https://sling.apache.org/documentation/bundles/models.html)

We beginnen met een HTL-component, genaamd `info`, die geen use-klasse heeft. Het bestaat uit één bestand, `/apps/my-example/components/info.html`

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

We voegen ook inhoud toe voor deze component die moet worden gerenderd op `/content/my-example/`:

```xml
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

Wanneer deze inhoud wordt benaderd, wordt het HTML-bestand uitgevoerd. Binnen de HTML-code gebruiken we het contextobject `properties` om toegang te krijgen tot de huidige bron `title` en `description` en deze weer te geven. Het uitvoerbestand `/content/my-example.html` wordt:

```html
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Een Use-Class toevoegen {#adding-a-use-class}

De `info` heeft geen use-klasse nodig om de eenvoudige functie ervan uit te voeren. Er zijn echter gevallen waarin u dingen moet doen die niet in HTML kunnen worden gedaan en dus een gebruiksklasse nodig hebt. Houd echter rekening met het volgende:

>[!NOTE]
>
>Een gebruiksklasse mag alleen worden gebruikt wanneer er iets niet alleen in HTML kan worden gedaan.

Stel bijvoorbeeld dat u de opdracht `info` component om de `title` en `description` eigenschappen van de bron, maar allemaal in kleine letters. Aangezien HTML geen methode voor het verlagen van tekenreeksen heeft, hebt u een use-klasse nodig. We kunnen dit doen door een Java-gebruiksklasse toe te voegen en te wijzigen `/apps/my-example/component/info/info.html` als volgt:

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

Daarnaast maken we `/apps/my-example/component/info/Info.java`.

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

Zie de [Javadocs voor `com.adobe.cq.sightly.WCMUsePojo`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) voor meer informatie .

Laten we nu door de verschillende delen van de code lopen.

### Lokale vs. Bundle Java Class {#local-vs-bundle-java-class}

De Java-gebruiksklasse kan op twee manieren worden geïnstalleerd:

* **Lokaal** - In een lokale installatie wordt het Java-bronbestand naast het HTML-bestand in dezelfde opslagmap geplaatst. De bron wordt automatisch gecompileerd op bestelling. Er is geen afzonderlijke compilatie of verpakkingsstap vereist.
* **Bundel** - In een bundelinstallatie moet de klasse Java worden gecompileerd en geïmplementeerd binnen een OSGi-bundel met behulp van het standaard AEM bundelimplementatiemechanisme (zie de sectie [Gebundelde Java-klasse](#bundled-java-class)).

Houd rekening met de volgende twee punten om te weten welke methode u moet gebruiken wanneer:

* A **lokale Java-gebruiksklasse** wordt aanbevolen wanneer de gebruiksklasse specifiek is voor de desbetreffende component.
* A **Java-use-class bundelen** wordt aanbevolen wanneer de Java-code een service implementeert die wordt benaderd vanuit meerdere HTML-componenten.

In dit voorbeeld wordt een lokale installatie gebruikt.

### Java Package is Repository Path {#java-package-is-repository-path}

Wanneer een lokale installatie wordt gebruikt, moet de pakketnaam van de use-klasse overeenkomen met die van de opslagplaats, waarbij eventuele koppeltekens in het pad worden vervangen door onderstrepingstekens in de pakketnaam.

In dit geval `Info.java` bevindt zich op `/apps/my-example/components/info` het pakket is dus `apps.my_example.components.info`:

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {

   ...

}
```

>[!NOTE]
>
>Het gebruik van koppeltekens in de namen van opslagplaats-items wordt aanbevolen bij AEM ontwikkeling. Afbreekstreepjes zijn echter niet toegestaan in Java-pakketnamen. Daarom **alle afbreekstreepjes in het opslagplaatsingspad moeten in de pakketnaam worden omgezet in onderstrepingstekens**.

### Uitbreiden `WCMUsePojo` {#extending-wcmusepojo}

Er zijn verschillende manieren om een Java-klasse op te nemen in HTML (zie de sectie [Alternatieven voor `WCMUsePojo`](#alternatives-to-wcmusepojo)), de eenvoudigste is de uitbreiding van de `WCMUsePojo` klasse. Voor ons voorbeeld `/apps/my-example/component/info/Info.java`:

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo

    ...
}
```

### De klasse initialiseren {#initializing-the-class}

Wanneer de use-klasse wordt uitgebreid van `WCMUsePojo`, wordt de initialisatie uitgevoerd door de `activate` in dit geval `/apps/my-example/component/info/Info.java`

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

De [activate](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) wordt gebruikt om (in lidvariabelen) de waarden vooraf te berekenen en op te slaan nodig in uw code HTML, die op de huidige context (het huidige verzoek en de middel, bijvoorbeeld) wordt gebaseerd.

De `WCMUsePojo` klasse biedt toegang tot dezelfde set contextobjecten als in een HTML-bestand (zie het document [Algemene objecten.](global-objects.md))

In een klasse die zich uitbreidt `WCMUsePojo`, contextobjecten kunnen via de naam worden benaderd

[`<T> T get(String name, Class<T> type)`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)

U kunt ook veelgebruikte contextobjecten rechtstreeks benaderen met de toepasselijke gebruiksmethode die in deze tabel wordt vermeld.

| Object | Handigheid |
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

Nadat de use-class is geïnitialiseerd, wordt het HTML-bestand uitgevoerd. Tijdens dit stadium zal HTML typisch in de staat van diverse lidvariabelen van de use-klasse trekken en hen voor presentatie teruggeven.

Als u vanuit het HTML-bestand toegang tot deze waarden wilt bieden, moet u aangepaste gettermethoden definiëren in de use-klasse volgens de volgende naamgevingsconventie:

* Een methode van het formulier `getXyz` zal in het HTML-bestand een objecteigenschap met de naam `xyz`.

In het volgende voorbeeldbestand `/apps/my-example/component/info/Info.java`, de methoden `getTitle` en `getDescription` resultaat in de objecteigenschappen `title` en `description` toegankelijk worden in de context van het HTML-bestand.

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

### data-smart-use Attribute {#data-sly-use-attribute}

De `data-sly-use` wordt gebruikt om de use-class binnen uw code te initialiseren HTML. In ons voorbeeld `data-sly-use` attribute declares that we want the class `Info`. We kunnen alleen de lokale naam van de klasse gebruiken, omdat we een lokale installatie gebruiken (als u het Java-bronbestand in dezelfde map als het HTML-bestand hebt geplaatst). Als we een bundel zouden installeren, zouden we de volledig gekwalificeerde klassenaam moeten opgeven.

Let op het gebruik in dit `/apps/my-example/component/info/info.html` voorbeeld.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Lokale id {#local-identifier}

De id `info` (na de punt in `data-sly-use.info`) wordt gebruikt in het HTML-bestand om de klasse te identificeren. Het bereik van deze id is globaal binnen het bestand, nadat deze is gedeclareerd. Het is niet beperkt tot het element dat het `data-sly-use` instructie.

Let op het gebruik in dit `/apps/my-example/component/info/info.html` voorbeeld.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Eigenschappen ophalen {#getting-properties}

De id `info` wordt vervolgens gebruikt om toegang te krijgen tot de objecteigenschappen `title` en `description` die zijn blootgesteld via de methoden getter `Info.getTitle` en `Info.getDescription`.

Let op het gebruik in dit `/apps/my-example/component/info/info.html` voorbeeld.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Uitvoer {#output}

Nu, wanneer wij toegang hebben `/content/my-example.html` het retourneert het volgende: `/content/my-example.html` bestand.

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

>[!NOTE]
>
>Dit voorbeeld is vereenvoudigd om het gebruik ervan eenvoudig te illustreren. In een productieomgeving wordt aanbevolen [Sling-modellen.](https://sling.apache.org/documentation/bundles/models.html)

## Buiten de basisbeginselen {#beyond-the-basics}

In deze sectie worden enkele andere functies geïntroduceerd die verder gaan dan het eenvoudige voorbeeld dat eerder is beschreven.

* Parameters doorgeven aan een use-klasse
* Java-gebruiksklasse gebundeld

### Parameters doorgeven {#passing-parameters}

Parameters kunnen bij initialisatie worden doorgegeven aan een use-klasse.

Zie de link [documentatie van de HTML-scriptengine.](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#passing-parameters-to-java-use-objects)

### Gebundelde Java-klasse {#bundled-java-class}

Met een gebundelde gebruiks-klasse moet de klasse worden gecompileerd, worden verpakt, en in AEM worden opgesteld gebruikend het standaard OSGi mechanisme van de bundelplaatsing. In tegenstelling tot een lokale installatie, zou de use-class pakketverklaring normaal zoals in dit moeten worden genoemd `/apps/my-example/component/info/Info.java` voorbeeld.

```java
package org.example.app.components;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    ...
}
```

En de `data-sly-use` De instructie moet verwijzen naar de volledig gekwalificeerde klassenaam, in tegenstelling tot alleen de lokale klassenaam zoals in deze `/apps/my-example/component/info/info.html` voorbeeld.

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```
