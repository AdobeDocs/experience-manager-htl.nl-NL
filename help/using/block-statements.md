---
title: HTML-blokinstructies
seo-title: HTML-blokinstructies
description: HTML Template Language (HTL)-blokinstructies zijn aangepaste gegevenskenmerken die rechtstreeks aan bestaande HTML worden toegevoegd.
seo-description: 'HTML Template Language (HTL)-blokinstructies zijn aangepaste gegevenskenmerken die rechtstreeks aan bestaande HTML worden toegevoegd. '
uuid: 0624fb6e-6989-431b-aabc-1138325393f1
contentOwner: User
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: reference
discoiquuid: 58aa6ea8-1d45-4f6f-a77e-4819f593a19d
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: afc29cbad83caeb549097da3fc33fd9147f1157a

---


# HTML-blokinstructies {#htl-block-statements}

HTML-blokinstructies (HTML Template Language) zijn aangepaste `data` kenmerken die rechtstreeks aan bestaande HTML worden toegevoegd. Hierdoor is het mogelijk een prototype van een statische HTML-pagina eenvoudig en onopvallend te annoteren, zodat deze kan worden omgezet in een functionerende dynamische sjabloon zonder de geldigheid van de HTML-code te verbreken.

## intelligent element {#sly-element}

Het element **** &lt;smart> wordt niet weergegeven in de resulterende HTML en kan worden gebruikt in plaats van de gegevensdoorschijnende terugloop. Het doel van het element &lt;smart> is om het duidelijker te maken dat het element niet wordt uitgevoerd. Als u wilt kunt u gegevens-sluw-unwrap nog gebruiken.

```xml
<sly data-sly-test.varone="${properties.yourProp}"/>
```

Net als bij het opheffen van gegevens, probeert u het gebruik van deze optie tot een minimum te beperken.

## use {#use}

**`data-sly-use`**: Initialiseert een hulpobject (gedefinieerd in JavaScript of Java) en maakt dit toegankelijk via een variabele.

Initialiseer een voorwerp JavaScript, waar het brondossier in de zelfde folder zoals het malplaatje wordt gevestigd. De bestandsnaam moet worden gebruikt:

```xml
<div data-sly-use.nav="navigation.js">${nav.foo}</div>
```

Initialiseer een klasse van Java, waar het brondossier in de zelfde folder zoals het malplaatje wordt gevestigd. De klassenaam moet worden gebruikt, niet de bestandsnaam:

```xml
        <div data-sly-use.nav="Navigation">${nav.foo}</div>
```

Initialiseer een klasse van Java, waar die klasse als deel van een bundel OSGi geïnstalleerd is. De volledig gekwalificeerde klassenaam moet worden gebruikt:

```xml
        <div data-sly-use.nav="org.example.Navigation">${nav.foo}</div>
```

Parameters kunnen aan de initialisatie worden doorgegeven met behulp van *opties*. Over het algemeen mag deze functie alleen worden gebruikt door HTML-code die zich zelf in een `data-sly-template` blok bevindt:

```xml
<div data-sly-use.nav="${'navigation.js' @parentPage=currentPage}">${nav.foo}</div>
```

Initialiseer een andere malplaatje HTML dat dan kan worden geroepen gebruikend **`data-sly-call`**:

```xml
<div data-sly-use.nav="navTemplate.html" data-sly-call="${nav.foo}"></div>
```

>[!NOTE]
>
>Zie voor meer informatie over de Use-API:
>
>* [Java Use-API](use-api-java.md)
>* [JavaScript Use-API](use-api-javascript.md)
>



## terugloop {#unwrap}

**`data-sly-unwrap`**: Verwijdert het hostelement uit de gegenereerde markering en behoudt de inhoud ervan. Hierdoor kunnen elementen worden uitgesloten die vereist zijn als onderdeel van de HTML-presentatielogica, maar die niet gewenst zijn in de werkelijke uitvoer.

Deze verklaring moet echter spaarzaam worden gebruikt. Over het algemeen is het beter om de HTML-markering zo dicht mogelijk bij de gewenste uitvoermarkering te houden. Met andere woorden, wanneer u HTML-blokinstructies toevoegt, probeert u zoveel mogelijk om de bestaande HTML eenvoudig te annoteren zonder nieuwe elementen in te voeren.

Dit

```xml
<p data-sly-use.nav="navigation.js">Hello World</p>
```

produceren

```xml
<p>Hello World</p>
```

Overwegende dat dit

```xml
<p data-sly-use.nav="navigation.js" data-sly-unwrap>Hello World</p>
```

produceren

```xml
Hello World
```

Het is ook mogelijk om een element voorwaardelijk op te heffen:

```xml
<div class="popup" data-sly-unwrap="${isPopup}">content</div>
```

## text {#text}

**`data-sly-text`**: Vervangt de inhoud van het hostelement met de opgegeven tekst.

Dit

```xml
<p>${properties.jcr:description}</p>
```

is gelijk aan

```xml
<p data-sly-text="${properties.jcr:description}">Lorem ipsum</p>
```

Bij beide wordt de waarde van **`jcr:description`** als alineatekst weergegeven. Het voordeel van de tweede methode is dat de onopvallende annotatie van HTML wordt toegestaan terwijl de statische plaatsaanduidingsinhoud van de oorspronkelijke ontwerper behouden blijft.

## attribute {#attribute}

**data-smart-attribute**: Hiermee voegt u kenmerken toe aan het element host.

Dit

```xml
<div title="${properties.jcr:title}"></div>
```

is gelijk aan

```xml
<div title="Lorem Ipsum" data-sly-attribute.title="${properties.jcr:title}"></div>
```

Beide zullen het `title` attribuut aan de waarde van plaatsen **`jcr:title`**. Het voordeel van de tweede methode is dat de onopvallende annotatie van HTML wordt toegestaan terwijl de statische plaatsaanduidingsinhoud van de oorspronkelijke ontwerper behouden blijft.

Attributen worden van links naar rechts opgelost, waarbij de meest rechtse instantie van een kenmerk (letterlijk of via **`data-sly-attribute`**) voorrang krijgt op instanties van hetzelfde kenmerk (letterlijk of via **`data-sly-attribute`**) links gedefinieerd.

Een kenmerk ( **`literal`** ofwel ingesteld via **`data-sly-attribute`**) waarvan de waarde *naar de lege tekenreeks evalueert* , wordt verwijderd uit de laatste opmaak. De enige uitzondering op deze regel is dat een *letterlijk* kenmerk dat is ingesteld op een *letterlijke* lege tekenreeks, behouden blijft. Bijvoorbeeld:

```xml
<div class="${''}" data-sly-attribute.id="${''}"></div>
```

produceert,

```xml
<div></div>
```

maar

```xml
<div class="" data-sly-attribute.id=""></div>
```

produceert,

```xml
<div class=""></div>
```

Als u meerdere kenmerken wilt instellen, geeft u een toewijzingsobject sleutelwaardeparen door die overeenkomen met de kenmerken en hun waarden. Bijvoorbeeld, veronderstellend,

```xml
attrMap = {
    title: "myTitle",
    class: "myClass",
    id: "myId"
}
```

Vervolgens

```xml
<div data-sly-attribute="${attrMap}"></div>
```

produceert,

```xml
<div title="myTitle" class="myClass" id="myId"></div>
```

## element {#element}

**`data-sly-element`**: Vervangt de elementnaam van het hostelement.

Bijvoorbeeld:

```xml
<h1 data-sly-element="${titleLevel}">text</h1>
```

Vervangt de **`h1`** waarde met de waarde van **`titleLevel`**.

Om veiligheidsredenen `data-sly-element` accepteert u alleen de volgende elementnamen:

```xml
a abbr address article aside b bdi bdo blockquote br caption cite code col colgroup
data dd del dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header i ins
kbd li main mark nav ol p pre q rp rt ruby s samp section small span strong sub 
sup table tbody td tfoot th thead time tr u var wbr
```

Voor het instellen van andere elementen moet XSS-beveiliging zijn uitgeschakeld ( `@context='unsafe'`).

## test {#test}

**`data-sly-test`:**Verwijdert voorwaardelijk het gastheerelement en het is inhoud. Een waarde van`false`verwijdert het element. Bij een waarde van blijft het element`true`behouden.

Het `p` element en de inhoud ervan worden bijvoorbeeld alleen gerenderd als `isShown` het `true`:

```xml
<p data-sly-test="${isShown}">text</p>
```

Het resultaat van een test kan aan een variabele worden toegewezen die later kan worden gebruikt. Dit wordt gewoonlijk gebruikt om de logica &quot;if else&quot; samen te stellen, aangezien er geen expliciete instructie else is:

```xml
<p data-sly-test.abc="${a || b || c}">is true</p>
<p data-sly-test="${!abc}">or not</p>
```

De variabele heeft, zodra deze is ingesteld, een algemeen bereik in het HTML-bestand.

Hier volgen enkele voorbeelden van het vergelijken van waarden:

```xml
<div data-sly-test="${properties.jcr:title == 'test'}">TEST</div>
<div data-sly-test="${properties.jcr:title != 'test'}">NOT TEST</div>

<div data-sly-test="${properties['jcr:title'].length > 3}">Title is longer than 3</div>
<div data-sly-test="${properties['jcr:title'].length >= 0}">Title is longer or equal to zero </div> 

<div data-sly-test="${properties['jcr:title'].length > aemComponent.MAX_LENGTH}">
    Title is longer than the limit of ${aemComponent.MAX_LENGTH}
</div>
```

## herhalen {#repeat}

Met data-slim-herhaling kunt u een element veelvoudige tijden *herhalen* die op de gespecificeerde lijst worden gebaseerd.

```xml
<div data-sly-repeat="${currentPage.listChildren}">${item.name}</div>
```

Dit werkt de zelfde manier zoals gegeven-slim-lijst, behalve dat hebt u geen containerelement nodig.

In het volgende voorbeeld wordt getoond dat u ook naar het *item* kunt verwijzen voor kenmerken:

```xml
<div data-sly-repeat="${currentPage.listChildren}" data-sly-attribute.class="${item.name}">${item.name}</div>
```

## list {#list}

**`data-sly-list`**: Herhaalt de inhoud van het hostelement voor elke opsombare eigenschap in het opgegeven object.

Hier volgt een eenvoudige lus:

```xml
<dl data-sly-list="${currentPage.listChildren}">
    <dt>index: ${itemList.index}</dt>
    <dd>value: ${item.title}</dd>
</dl>
```

De volgende standaardvariabelen zijn beschikbaar binnen het bereik van de lijst:

**`item`**: Het huidige item in de herhaling.

**`itemList`**: Object met de volgende eigenschappen:

**`index`**: op nul gebaseerde teller ( `0..length-1`).

**`count`**: op één gebaseerde teller ( `1..length`).

`first`: `true` als het huidige item het eerste item is.

**`middle`**: `true` als het huidige item noch het eerste noch het laatste item is.

**`last`**: `true` als het huidige item het laatste item is.

**`odd`**: `true` als `index` het oneven is.

**`even`**: `true` als `index` het even is.

Als u een id in de `data-sly-list` instructie definieert, kunt u de naam van de **`itemList`** en `item` variabelen wijzigen. **`item`** wordt *** `<variable>`*** en **`itemList`** wordt **`*<variable>*List`**.

```xml
<dl data-sly-list.child="${currentPage.listChildren}">
    <dt>index: ${childList.index}</dt>
    <dd>value: ${child.title}</dd>
</dl>
```

U hebt ook dynamisch toegang tot eigenschappen:

```xml
<dl data-sly-list.child="${myObj}">
    <dt>key: ${child}</dt>
    <dd>value: ${myObj[child]}</dd>
</dl>
```

## resource {#resource}

**`data-sly-resource`**: Bevat het resultaat van het renderen van de opgegeven bron via de sling resolution and rendering process.

Een eenvoudige bron is:

```xml
<article data-sly-resource="path/to/resource"></article>
```

Met de opties kunt u een aantal extra varianten instellen:

Het pad van de bron bewerken:

```xml
<article data-sly-resource="${ @ path='path/to/resource'}"></article>
<article data-sly-resource="${'resource' @ prependPath='my/path'}"></article>
<article data-sly-resource="${'my/path' @ appendPath='resource'}"></article>
```

Een kiezer toevoegen (of vervangen):

```xml
<article data-sly-resource="${'path/to/resource' @ selectors='selector'}"></article>
```

Meerdere kiezers toevoegen, vervangen of verwijderen:

```xml
<article data-sly-resource="${'path/to/resource' @ selectors=['s1', 's2']}"></article>
```

Voeg een kiezer toe aan de bestaande kiezers:

```xml
<article data-sly-resource="${'path/to/resource' @ addSelectors='selector'}"></article>
```

Enkele kiezers uit de bestaande kiezers verwijderen:

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors='selector1'}"></article>
```

Alle kiezers verwijderen:

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors}"></article>
```

Hiermee overschrijft u het brontype van de bron:

```xml
<article data-sly-resource="${'path/to/resource' @ resourceType='my/resource/type'}"></article>
```

Hiermee wijzigt u de WCM-modus:

```xml
<article data-sly-resource="${'path/to/resource' @ wcmmode='disabled'}"></article>
```

Standaard zijn de AEM-decoratietags uitgeschakeld, met de optie decorationTagName kunnen ze weer worden opgehaald en met de cssClassName kunt u klassen aan dat element toevoegen.

```xml
<article data-sly-resource="${'path/to/resource' @ decorationTagName='span',
cssClassName='className'}"></article>
```

>[!NOTE]
>
>AEM biedt duidelijke en eenvoudige logica die de decoratietags regelt die ingesloten elementen laten omlopen. Zie [Decoratietag](https://helpx.adobe.com/experience-manager/6-4/sites/developing/using/decoration-tag.html) in de documentatie over ontwikkelende componenten voor meer informatie.

## include {#include}

**`data-sly-include`**: Hiermee vervangt u de inhoud van het hostelement door de opmaak die is gegenereerd door het opgegeven HTML-sjabloonbestand (HTL, JSP, ESP, enz.) wanneer het wordt verwerkt door de overeenkomstige sjabloonmotor. De renderingcontext van het *ingesloten bestand* omvat niet de huidige HTL-context (die van het *inclusief bestand*); Voor de opname van HTML-bestanden **`data-sly-use`** moet de huidige tekst daarom in het opgenomen bestand worden herhaald (in dat geval is het meestal beter om **`data-sly-template`** en `data-sly-call`)

Een eenvoudig voorbeeld:

```xml
<section data-sly-include="path/to/template.html"></section>
```

JSPs kan de zelfde manier worden omvat:

```xml
<section data-sly-include="path/to/template.jsp"></section>
```

Met opties kunt u het pad van het bestand bewerken:

```xml
<section data-sly-include="${ @ file='path/to/template.html'}"></section>
<section data-sly-include="${'template.html' @ prependPath='my/path'}"></section>
<section data-sly-include="${'my/path' @ appendPath='template.html'}"></section>
```

U kunt ook de WCM-modus wijzigen:

```xml
<section data-sly-include="${'template.html' @ wcmmode='disabled'}"></section>
```

## sjabloon en gesprek {#template-call}

`data-sly-template`: Definieert een sjabloon. Het hostelement en de inhoud ervan worden niet uitgevoerd door HTML

`data-sly-call`: Roept een malplaatje dat met gegeven-slim-malplaatje wordt bepaald. De inhoud van het geroepen malplaatje (naar keuze wordt bepaald) vervangt de inhoud van het gastheerelement van de vraag.

Definieer een statische sjabloon en roep deze vervolgens aan:

```xml
<template data-sly-template.one>blah</template>
<div data-sly-call="${one}"></div>
```

Definieer een dynamische sjabloon en roep deze vervolgens aan met parameters:

```xml
<template data-sly-template.two="${ @ title}"><h1>${title}</h1></template>
<div data-sly-call="${two @ title=properties.jcr:title}"></div>
```

Sjablonen in een ander bestand kunnen worden geïnitialiseerd met `data-sly-use`. Let op: in dit geval `data-sly-use` en `data-sly-call` kan dit ook op hetzelfde element worden geplaatst:

```xml
<div data-sly-use.lib="templateLib.html">
    <div data-sly-call="${lib.one}"></div>
    <div data-sly-call="${lib.two @ title=properties.jcr:title}"></div>
</div>
```

Sjabloonherhaling wordt ondersteund:

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

## i18n- en landinstellingsobjecten {#i-n-and-locale-objects}

Wanneer u i18n en HTML gebruikt, kunt u nu ook aangepaste landinstellingsobjecten doorgeven.

```xml
${'Hello World' @ i18n, locale=request.locale}
```

## Bewerking van URL {#url-manipulation}

Er is een nieuwe set URL-manipulaties beschikbaar.

Zie de volgende voorbeelden van hun gebruik:

Hiermee voegt u de HTML-extensie toe aan een pad.

```xml
<a href="${item.path @ extension = 'html'}">${item.name}</a>
```

Hiermee voegt u de extensie html en een kiezer toe aan een pad.

```xml
<a href="${item.path @ extension = 'html', selectors='products'}">${item.name}</a>
```

Hiermee voegt u de extensie html en een fragment (#value) toe aan een pad.

```xml
<a href="${item.path @ extension = 'html', fragment=item.name}">${item.name}</a>
```

## HTML-functies die worden ondersteund in AEM 6.3 {#htl-features-supported-in-aem}

De volgende nieuwe HTML-functies worden ondersteund in Adobe Experience Manager (AEM) 6.3:

### Getal/datum-opmaak {#number-date-formatting}

AEM 6.3 steunt inheemse het formatteren van aantallen en data, zonder douanecode te schrijven. Dit ondersteunt ook tijdzone en landinstelling.

In het volgende voorbeeld wordt getoond dat de indeling eerst wordt opgegeven en vervolgens de waarde waarvoor opmaak nodig is:

```xml
<h2>${ 'dd-MMMM-yyyy hh:mm:ss' @
           format=currentPage.lastModified,
           timezone='PST',
           locale='fr'}</h2>

<h2>${ '#.00' @ format=300}</h2>
```

>[!NOTE]
>
>Raadpleeg de [HTML-specificatie](https://github.com/Adobe-Marketing-Cloud/htl-spec/blob/master/SPECIFICATION.md)voor volledige informatie over de indeling die u kunt gebruiken.

### gegevensbewust gebruik met bronnen {#data-sly-use-with-resources}

Dit staat toe om middelen direct in HTML met gegeven-slim-gebruik te krijgen en vereist niet om code te schrijven om het middel te krijgen.

Bijvoorbeeld:

```xml
<div data-sly-use.product=“/etc/commerce/product/12345”>
  ${ product.title }
</div>
```

### Aanvraagkenmerken {#request-attributes}

In *gegeven-omvat* en *gegeven-slim-middel* kunt u *requestAttributes* nu overgaan om hen in het ontvangende HTML-manuscript te gebruiken.

Hierdoor kunt u parameters correct in scripts of componenten doorgeven.

```xml
<sly data-sly-use.settings="com.adobe.examples.htl.core.hashmap.Settings" 
        data-sly-include="${ 'productdetails.html' @ requestAttributes=settings.settings}" />
```

Java-code van de klasse Settings, de Kaart wordt gebruikt om in requestAttributes over te gaan:

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

U kunt bijvoorbeeld via een Sling-Model de waarde van de opgegeven requestAttributes gebruiken.

In dit voorbeeld wordt de *lay-out* via de Kaart geïnjecteerd vanuit de klasse Use-class:

```xml
@Model(adaptables=SlingHttpServletRequest.class)
public class ProductSettings {
  @Inject @Optional @Default(values="empty")
  public String layout;

}
```

### Oplossen voor @extension {#fix-for-extension}

De @extension werkt in alle scenario&#39;s in AEM 6.3, alvorens u een resultaat zoals *www.adobe.com.html* kon hebben en controleert ook of om de uitbreiding toe te voegen of niet.

```xml
${ link @ extension = 'html' }
```
