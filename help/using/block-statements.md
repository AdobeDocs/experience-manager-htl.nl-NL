---
title: HTML-blokinstructies
description: HTML Template Language (HTL)-blokinstructies zijn aangepaste gegevenskenmerken die rechtstreeks aan bestaande HTML worden toegevoegd.
translation-type: tm+mt
source-git-commit: 2336ce8860056f5bc6470b23ee4cda135f7107d8
workflow-type: tm+mt
source-wordcount: '1555'
ht-degree: 1%

---


# HTML-blokinstructies {#htl-block-statements}

HTML Template Language (HTL)-blokinstructies zijn aangepaste `data`-kenmerken die rechtstreeks aan bestaande HTML worden toegevoegd. Hierdoor is het mogelijk een prototype van een statische HTML-pagina eenvoudig en onopvallend te annoteren, zodat deze kan worden omgezet in een functionerende dynamische sjabloon zonder de geldigheid van de HTML-code te verbreken.

## Blokoverzicht {#overview}

HTML-blokplug-ins worden gedefinieerd door `data-sly-*`-kenmerken die zijn ingesteld op HTML-elementen. Elementen kunnen een afsluitende tag hebben of zichzelf afsluiten. Kenmerken kunnen waarden hebben (statische tekenreeksen of expressies) of alleen Booleaanse kenmerken zijn (zonder waarde).

```xml
<tag data-sly-BLOCK></tag>                                 <!--/* A block is simply consists in a data-sly attribute set on an element. */-->
<tag data-sly-BLOCK/>                                      <!--/* Empty elements (without a closing tag) should have the trailing slash. */-->
<tag data-sly-BLOCK="string value"/>                       <!--/* A block statement usually has a value passed, but not necessarily. */-->
<tag data-sly-BLOCK="${expression}"/>                      <!--/* The passed value can be an expression as well. */-->
<tag data-sly-BLOCK="${@ myArg='foo'}"/>                   <!--/* Or a parametric expression with arguments. */-->
<tag data-sly-BLOCKONE="value" data-sly-BLOCKTWO="value"/> <!--/* Several block statements can be set on a same element. */-->
```

Alle geëvalueerde `data-sly-*` attributen worden verwijderd uit de geproduceerde prijsverhoging.

### Id&#39;s {#identifiers}

Een blokinstructie kan ook worden gevolgd door een id:

```xml
<tag data-sly-BLOCK.IDENTIFIER="value"></tag>
```

De id kan op verschillende manieren door de blokinstructie worden gebruikt. Hier volgen enkele voorbeelden:

```xml
<!--/* Example of statements that use the identifier to set a variable with their result: */-->
<div data-sly-use.navigation="MyNavigation">${navigation.title}</div>
<div data-sly-test.isEditMode="${wcmmode.edit}">${isEditMode}</div>
<div data-sly-list.child="${currentPage.listChildren}">${child.properties.jcr:title}</div>
<div data-sly-template.nav>Hello World</div>

<!--/* The attribute statement uses the identifier to know to which attribute it should apply it's value: */-->
<div data-sly-attribute.title="${properties.jcr:title}"></div> <!--/* This will create a title attribute */-->
```

De bovenste id&#39;s op hoofdniveau zijn hoofdlettergevoelig (omdat ze kunnen worden ingesteld via HTML-kenmerken die niet hoofdlettergevoelig zijn), maar alle eigenschappen ervan zijn hoofdlettergevoelig.

## Beschikbare blokinstructies {#available-block-statements}

Er is een aantal beschikbare blokinstructies. Wanneer gebruikt op hetzelfde element, definieert de volgende prioriteitslijst hoe blokinstructies worden geëvalueerd:

1. `data-sly-template`
1. `data-sly-set`, `data-sly-test`, `data-sly-use`
1. `data-sly-call`
1. `data-sly-text`
1. `data-sly-element`,  `data-sly-include`,  `data-sly-resource`
1. `data-sly-unwrap`
1. `data-sly-list`, `data-sly-repeat`
1. `data-sly-attribute`

Wanneer twee blokverklaringen de zelfde prioriteit hebben, is hun evaluatieorde van links naar rechts.

### use {#use}

`data-sly-use` initialiseert een hulpobject (gedefinieerd in JavaScript of Java) en maakt dit toegankelijk via een variabele.

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

Parameters kunnen met behulp van opties worden doorgegeven aan de initialisatie. Over het algemeen mag deze functie alleen worden gebruikt door HTML-code die zich zelf binnen een `data-sly-template`-blok bevindt:

```xml
<div data-sly-use.nav="${'navigation.js' @parentPage=currentPage}">${nav.foo}</div>
```

Initialiseer een andere malplaatje HTML dat dan kan worden geroepen gebruikend `data-sly-call`:

```xml
<div data-sly-use.nav="navTemplate.html" data-sly-call="${nav.foo}"></div>
```

>[!NOTE]
>
>Zie voor meer informatie over de Use-API:
>
>* [Java Use-API](use-api-java.md)
>* [JavaScript Use-API](use-api-javascript.md)


#### data-smart-use with resources {#data-sly-use-with-resources}

Hierdoor kunnen bronnen direct in HTML worden opgehaald met `data-sly-use` en hoeft er geen code te worden geschreven om deze op te halen.

Bijvoorbeeld:

```xml
<div data-sly-use.product=“/etc/commerce/product/12345”>
  ${ product.title }
</div>
```

>[!TIP]
>
>Zie ook de sectie [Pad niet altijd vereist.](#path-not-required)

### opheffen {#unwrap}

`data-sly-unwrap` verwijdert het hostelement uit de gegenereerde markering en behoudt de inhoud ervan. Hierdoor kunnen elementen worden uitgesloten die vereist zijn als onderdeel van de HTML-presentatielogica, maar die niet gewenst zijn in de werkelijke uitvoer.

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

### set {#set}

`data-sly-set` definieert een nieuwe id met een vooraf gedefinieerde waarde.

```xml
<span data-sly-set.profile="${user.profile}">Hello, ${profile.firstName} ${profile.lastName}!</span>
<a class="profile-link" href="${profile.url}">Edit your profile</a>
```

### text {#text}

`data-sly-text` vervangt de inhoud van zijn gastheerelement met de gespecificeerde tekst.

Dit

```xml
<p>${properties.jcr:description}</p>
```

is gelijk aan

```xml
<p data-sly-text="${properties.jcr:description}">Lorem ipsum</p>
```

Bij beide wordt de waarde `jcr:description` weergegeven als alineatekst. Het voordeel van de tweede methode is dat de onopvallende annotatie van HTML wordt toegestaan terwijl de statische plaatsaanduidingsinhoud van de oorspronkelijke ontwerper behouden blijft.

### kenmerk {#attribute}

`data-sly-attribute` voegt kenmerken toe aan het element host.

Dit

```xml
<div title="${properties.jcr:title}"></div>
```

is gelijk aan

```xml
<div title="Lorem Ipsum" data-sly-attribute.title="${properties.jcr:title}"></div>
```

Beide zullen het `title` attribuut aan de waarde van `jcr:title` plaatsen. Het voordeel van de tweede methode is dat de onopvallende annotatie van HTML wordt toegestaan terwijl de statische plaatsaanduidingsinhoud van de oorspronkelijke ontwerper behouden blijft.

Attributen worden van links naar rechts opgelost, waarbij de meest rechtse instantie van een kenmerk (letterlijk of gedefinieerd via `data-sly-attribute`) voorrang krijgt op instanties van hetzelfde kenmerk (letterlijk gedefinieerd of via `data-sly-attribute`) links gedefinieerd.

Een attribuut (ofwel `literal` of ingesteld via `data-sly-attribute`) waarvan de waarde naar de lege tekenreeks evalueert, wordt verwijderd uit de definitieve opmaak. De enige uitzondering op deze regel is dat een letterlijk kenmerk dat is ingesteld op een letterlijke lege tekenreeks, behouden blijft. Bijvoorbeeld,

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

### element {#element}

`data-sly-element` vervangt de elementnaam van het hostelement.

Bijvoorbeeld,

```xml
<h1 data-sly-element="${titleLevel}">text</h1>
```

Vervangt `h1` met de waarde van `titleLevel`.

Om veiligheidsredenen accepteert `data-sly-element` alleen de volgende elementnamen:

```xml
a abbr address article aside b bdi bdo blockquote br caption cite code col colgroup
data dd del dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header i ins
kbd li main mark nav ol p pre q rp rt ruby s samp section small span strong sub
sup table tbody td tfoot th thead time tr u var wbr
```

Voor het instellen van andere elementen moet XSS-beveiliging zijn uitgeschakeld ( `@context='unsafe'`).

### test {#test}

`data-sly-test` verwijdert voorwaardelijk het gastheerelement en het is inhoud. De waarde `false` verwijdert het element; Bij een waarde van `true` blijft het element behouden.

Het element `p` en de inhoud ervan worden bijvoorbeeld alleen gerenderd als `isShown` `true` is:

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

### herhalen {#repeat}

Met `data-sly-repeat` kunt u een element meerdere keren herhalen op basis van de opgegeven lijst.

```xml
<div data-sly-repeat="${currentPage.listChildren}">${item.name}</div>
```

Dit werkt de zelfde manier zoals `data-sly-list`, behalve dat hebt u geen containerelement nodig.

In het volgende voorbeeld wordt getoond dat u ook naar het *item* voor kenmerken kunt verwijzen:

```xml
<div data-sly-repeat="${currentPage.listChildren}" data-sly-attribute.class="${item.name}">${item.name}</div>
```

### list {#list}

`data-sly-list` herhaalt de inhoud van het hostelement voor elke opsombare eigenschap in het opgegeven object.

Hier volgt een eenvoudige lus:

```xml
<dl data-sly-list="${currentPage.listChildren}">
    <dt>index: ${itemList.index}</dt>
    <dd>value: ${item.title}</dd>
</dl>
```

De volgende standaardvariabelen zijn beschikbaar binnen het bereik van de lijst:

* `item`: Het huidige item in de herhaling.
* `itemList`: Object met de volgende eigenschappen:
* `index`: op nul gebaseerde teller (  `0..length-1`).
* `count`: op één gebaseerde teller (  `1..length`).
* `first`:  `true` als het huidige item het eerste item is.
* `middle`:  `true` als het huidige item noch het eerste noch het laatste item is.
* `last`:  `true` als het huidige item het laatste item is.
* `odd`:  `true` als  `index` het oneven is.
* `even`:  `true` als  `index` dat even is.

Door een id op de instructie `data-sly-list` te definiëren, kunt u de namen van de variabelen `itemList` en `item` wijzigen. `item` wordt  `<variable>` en  `itemList` wordt  `<variable>List`.

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

### resource {#resource}

`data-sly-resource` omvat het resultaat van het renderen van de opgegeven bron via de sling resolution and rendering process.

Een eenvoudige bron is:

```xml
<article data-sly-resource="path/to/resource"></article>
```

#### Pad niet altijd vereist {#path-not-required}

Merk op dat het gebruik van een weg met `data-sly-resource` niet wordt vereist als u reeds het middel hebt. Als u de bron al hebt, kunt u deze rechtstreeks gebruiken.

Het volgende is bijvoorbeeld juist.

```xml
<sly data-sly-resource="${resource.path @ decorationTagName='div'}"></sly>
```

Het volgende is echter ook volkomen aanvaardbaar.

```xml
<sly data-sly-resource="${resource @ decorationTagName='div'}"></sly>
```

Om de volgende redenen wordt aangeraden de bron zo mogelijk rechtstreeks te gebruiken.

* Als u reeds het middel hebt, is het re-oplossen gebruikend de weg extra, onnodig werk.
* Het gebruiken van de weg wanneer u reeds het middel hebt kan onverwachte resultaten introduceren aangezien het verdelen van middelen kan worden verpakt of synthetisch en niet bij de bepaalde weg verstrekt zijn.

#### Opties {#resource-options}

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

Standaard zijn de AEM decoration-tags uitgeschakeld, met de optie decorationTagName kunnen deze weer worden opgehaald en met de cssClassName kunt u klassen aan dat element toevoegen.

```xml
<article data-sly-resource="${'path/to/resource' @ decorationTagName='span',
cssClassName='className'}"></article>
```

>[!NOTE]
>
>AEM biedt duidelijke en eenvoudige logica die de decoratietags regelt die elementen bevatten. Zie [Decoratiemarkering](https://docs.adobe.com/content/help/en/experience-manager-65/developing/components/decoration-tag.html) in de documentatie over ontwikkelende componenten voor meer informatie.

### include {#include}

`data-sly-include` vervangt de inhoud van het hostelement door de opmaak die wordt gegenereerd door het opgegeven HTML-sjabloonbestand (HTL, JSP, ESP, enz.) wanneer het wordt verwerkt door de overeenkomstige sjabloonmotor. De renderingcontext van het opgenomen bestand bevat niet de huidige HTML-context (die van het include-bestand); Daarom moet voor het opnemen van HTML-bestanden de huidige `data-sly-use` in het opgenomen bestand worden herhaald (In een dergelijk geval is het doorgaans beter `data-sly-template` en `data-sly-call` te gebruiken)

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

### Request-attributes {#request-attributes}

In `data-sly-include` en `data-sly-resource` kunt u `requestAttributes` overgaan om hen in het ontvangende HTML-manuscript te gebruiken.

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

U kunt bijvoorbeeld via een Sling-Model de waarde van de opgegeven `requestAttributes` gebruiken.

In dit voorbeeld wordt de lay-out via de Kaart geïnjecteerd vanuit de Use-class:

```xml
@Model(adaptables=SlingHttpServletRequest.class)
public class ProductSettings {
  @Inject @Optional @Default(values="empty")
  public String layout;

}
```

### sjabloon en aanroep {#template-call}

Sjabloonblokken kunnen worden gebruikt als functieaanroepen: in hun verklaring kunnen zij parameters krijgen, die dan kunnen worden overgegaan wanneer het roepen van hen. Ze staan ook recursie toe.

`data-sly-template` definieert een sjabloon. Het hostelement en de inhoud ervan worden niet uitgevoerd door HTML

`data-sly-call` roept een malplaatje dat met gegeven-slim-malplaatje wordt bepaald. De inhoud van het geroepen malplaatje (naar keuze wordt bepaald) vervangt de inhoud van het gastheerelement van de vraag.

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

Sjablonen in een ander bestand kunnen worden geïnitialiseerd met `data-sly-use`. In dit geval kunnen `data-sly-use` en `data-sly-call` ook op hetzelfde element worden geplaatst:

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

## intelligent element {#sly-element}

De HTML-tag `<sly>` kan worden gebruikt om het huidige element te verwijderen, zodat alleen de onderliggende elementen kunnen worden weergegeven. De functionaliteit is vergelijkbaar met het blokelement `data-sly-unwrap`:

```xml
<!--/* This will display only the output of the 'header' resource, without the wrapping <sly> tag */-->
<sly data-sly-resource="./header"></sly>
```

Hoewel de tag `<sly>` geen geldige HTML 5-tag is, kan de tag &lt;a0/> in de uiteindelijke uitvoer worden weergegeven met `data-sly-unwrap`:

```xml
<sly data-sly-unwrap="${false}"></sly> <!--/* outputs: <sly></sly> */-->
```

Het doel van het element `<sly>` is het duidelijker te maken dat het element geen output is. Als u wilt kunt u nog `data-sly-unwrap` gebruiken.

Net als met `data-sly-unwrap`, probeer het gebruik van dit te minimaliseren.
