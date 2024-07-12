---
title: Aan de slag met HTML
description: Leer over HTML, het aangewezen en geadviseerde server-zijmalplaatjesysteem voor HTML in AEM, en begrijp de belangrijkste concepten van de taal en zijn fundamentele bouwstenen.
exl-id: c95eb1b3-3b96-4727-8f4f-d54e7136a8f9
source-git-commit: 88edbd2fd66de960460df5928a3b42846d32066b
workflow-type: tm+mt
source-wordcount: '2147'
ht-degree: 0%

---


# Aan de slag met HTML {#getting-started-with-htl}

HTML Sjabloontaal (HTL) is het voorkeurssjabloonsysteem en aanbevolen sjabloonsysteem voor HTML in Adobe Experience Manager. Net als in alle HTML server-side sjabloonsystemen definieert een HTML-bestand de uitvoer die naar de browser wordt verzonden door de HTML zelf, een aantal basispresentatielogica en de variabelen die tijdens runtime moeten worden geëvalueerd, op te geven.

Dit document geeft een overzicht van het doel van HTML en een inleiding tot fundamentele concepten en constructies van de taal.

>[!TIP]
>
>In dit document wordt het doel van HTL uiteengezet en wordt een overzicht gegeven van de fundamentele structuur en concepten ervan. Als u vragen over specifieke syntaxis hebt, gelieve te verwijzen naar de [ specificatie van HTML.](specification.md)

## HTML-lagen {#layers}

HTML zoals gebruikt in AEM kan door een aantal lagen worden bepaald.

1. **[de Specificatie van HTML](specification.md)** - HTML is een open-bron, platform-agnostieke specificatie, die iedereen vrij is uit te voeren.
1. **[het Verzamelen HTML Scripting Engine](specification.md)** - het het Verdelen project heeft tot de verwijzingsimplementatie van HTML geleid, die door AEM wordt gebruikt.
1. **[AEM Uitbreidingen](specification.md)** - AEM bouwt op de Motor van het SlingHTML Scripting voort om ontwikkelaars geschikte eigenschappen specifiek voor AEM aan te bieden.

Deze HTML-documentatie richt zich op het gebruik van HTML voor het ontwikkelen van AEM oplossingen. Als zodanig raakt het alle drie lagen, die externe middelen zonodig verbinden.

## Grondbeginselen van HTML {#fundamental-concepts-of-htl}

De Taal van het Malplaatje van de HTML gebruikt een uitdrukkingstaal om stukken van inhoud in de teruggegeven prijsverhoging op te nemen, en HTML5 gegevensattributen om verklaringen over blokken van prijsverhoging (als voorwaarden of herhalingen) te bepalen. Als HTML wordt gecompileerd in Java Servlets, worden de expressies en de HTML-gegevenskenmerken volledig geëvalueerd op de server en blijft er niets zichtbaar in de resulterende HTML.

>[!TIP]
>
>Om de meeste die voorbeelden in werking te stellen op deze pagina worden verstrekt, kan een levende uitvoeringsmilieu [ Gelezen Lijn van de Druk van de Ovaal ](https://github.com/adobe/aem-htl-repl) worden gebruikt.

### Blokken en expressies {#blocks-and-expressions}

Hier volgt een eerste voorbeeld, dat kan worden opgenomen zoals in een `template.html` -bestand:

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

Er kunnen twee verschillende soorten syntaxen worden onderscheiden:

* **Verklaringen van het Blok** - om het `<h1>` element voorwaardelijk te tonen, wordt een `data-sly-test` HTML5 gegevensattribuut gebruikt. HTL biedt meerdere van dergelijke kenmerken, die het koppelen van gedrag aan elk HTML-element toestaan en die allemaal worden voorafgegaan door `data-sly` .
* **Taal van de Uitdrukking** - de uitdrukkingen van HTML worden afgebakend door `${` en `}` karakters. Tijdens de runtime worden deze expressies geëvalueerd en wordt de waarde ervan in de uitgaande HTML-stream geïnjecteerd.

Zie de [ specificatie van HTML ](specification.md) voor details op beide syntaxis.

### Het SLY-element {#the-sly-element}

Een centraal concept van HTML is de mogelijkheid te bieden om bestaande HTML-elementen opnieuw te gebruiken voor het definiëren van blokinstructies, waardoor het niet nodig is extra scheidingstekens in te voegen om te bepalen waar de instructie begint en eindigt. Deze onopvallende aantekening van de prijsverhoging om een statische HTML in een functionerend dynamisch malplaatje om te zetten biedt het voordeel om de geldigheid van de code van de HTML niet te breken, en daarom nog behoorlijk te tonen, zelfs als statische dossiers.

Soms is er echter geen bestaand element op de exacte locatie waar een blokinstructie moet worden ingevoegd. In dergelijke gevallen is het mogelijk een speciaal `sly` -element in te voegen dat automatisch uit de uitvoer wordt verwijderd, terwijl de gekoppelde blokinstructies worden uitgevoerd en de inhoud dienovereenkomstig wordt weergegeven.

In het volgende voorbeeld...

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

...zal iets als volgende HTML uitvoeren, maar slechts als er allebei zijn, wordt een `jcr:title` en een `jcr:description` eigenschap bepaald, en als geen van beiden leeg zijn:

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

Een ding om in mening te houden is slechts het `sly` element te gebruiken wanneer geen bestaand element met de blokverklaring kon geannoteerd zijn. De reden hiervoor is dat `sly` -elementen de door de taal geboden waarde afleiden om de statische HTML niet te wijzigen wanneer deze dynamisch wordt gemaakt.

Als het vorige voorbeeld bijvoorbeeld zou zijn ondergebracht in een `div` -element, zou het toegevoegde `sly` -element misbruik maken:

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

en het element `div` had met de voorwaarde geannoteerd kunnen zijn:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

### HTML-opmerkingen {#htl-comments}

In het volgende voorbeeld ziet u een HTML-opmerking op de eerste regel en een HTML-opmerking op de tweede regel.

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

HTML-opmerkingen zijn HTML-opmerkingen met een extra JavaScript-achtige syntaxis. De hele HTML-opmerking en alles wat erin staat, wordt volledig genegeerd door de processor en uit de uitvoer verwijderd.

De inhoud van de standaardopmerkingen voor HTML wordt echter doorgegeven en de expressies in de opmerking worden geëvalueerd.

HTML-opmerkingen kunnen geen HTML-opmerkingen bevatten en andersom.

### Speciale contexten {#special-contexts}

Om optimaal gebruik te kunnen maken van HTL is het belangrijk dat u goed begrijpt wat de gevolgen zijn van het feit dat deze gebaseerd is op de syntaxis van de HTML.

Gelieve te verwijzen naar de [ sectie van de Context van de Vertoning ](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#121-display-context) van de specificatie HTML voor meer details.

### Element- en kenmerknamen {#element-and-attribute-names}

Expressies kunnen alleen in HTML-tekst of -kenmerkwaarden worden geplaatst, maar niet binnen elementnamen of kenmerknamen, of het zou geen geldige HTML meer zijn. Om elementnamen dynamisch in te stellen, kan de instructie `data-sly-element` worden gebruikt voor de gewenste elementen en om kenmerknamen dynamisch in te stellen, zelfs wanneer meerdere kenmerken tegelijk worden ingesteld, kan de instructie `data-sly-attribute` worden gebruikt.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### Context zonder instructies van het blok {#contexts-without-block-statements}

Aangezien HTML gegevenskenmerken gebruikt om blokinstructies te bepalen, is het niet mogelijk om dergelijke blokinstructies binnen van volgende contexten te bepalen, en slechts uitdrukkingen kunnen daar worden gebruikt:

* Opmerkingen HTML
* Scriptelementen
* Stijlelementen

De reden hiervoor is dat de inhoud van deze contexten tekst is en niet HTML, en dat ingesloten HTML-elementen worden beschouwd als eenvoudige tekengegevens. Zonder echte HTML-elementen kunnen er dus ook geen `data-sly` -kenmerken worden uitgevoerd.

Dit kan als een significante beperking klinken, nochtans is het gewenst, omdat de Taal van het Malplaatje van HTML niet zou moeten worden misbruikt om output te produceren die niet HTML is. [ gebruik-API voor de Toegang tot van Logische ](#use-api-for-accessing-logic) sectie introduceert hieronder hoe de extra logica van het malplaatje kan worden geroepen, dat kan worden gebruikt als het nodig is om complexe output voor deze contexten voor te bereiden. Bijvoorbeeld, een gemakkelijke manier om gegevens van het achterste eind naar een front-end manuscript te verzenden, is de logica van de component te hebben om een koord te produceren JSON, dat dan in een gegevensattribuut met een eenvoudige uitdrukking van HTML kan worden geplaatst.

In het volgende voorbeeld wordt het gedrag voor HTML-opmerkingen getoond, maar in script- of stijlelementen wordt hetzelfde gedrag waargenomen:

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

zal iets als volgende HTML uitvoeren:

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### Expliciete vereiste contexten {#explicit-contexts-required}

Zoals verklaard in de [ Automatische Context-bewust Escaping ](#automatic-context-aware-escaping) hieronder sectie, één doel van HTML is de risico&#39;s te verminderen om dwars-plaats scripting (XSS) kwetsbaarheid in te voeren door context-bewuste het ontsnapen aan alle uitdrukkingen automatisch toe te passen. Hoewel HTML automatisch de context kan detecteren van expressies die in een HTML-opmaak zijn geplaatst, wordt de syntaxis van inline JavaScript of CSS niet geanalyseerd en is daarom afhankelijk van de ontwikkelaar om expliciet aan te geven welke exacte context op dergelijke expressies moet worden toegepast.

Aangezien het niet toepassen van de correcte escaping in XSS kwetsbaarheid resulteert, verwijdert HTL daarom de output van alle uitdrukkingen die in manuscript en stijlcontexten zijn wanneer de context niet is verklaard.

Hier volgt een voorbeeld van hoe u de context instelt voor expressies die in scripts en stijlen worden geplaatst:

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

Voor meer details over hoe te om het ontsnappen te controleren, verwijs naar de ](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) sectie van de Context van de Vertoning van de Taal van de Uitdrukking van de [ Uitdrukking van de Specificaties HTML.

## Algemene mogelijkheden van HTL {#general-capabilities-of-htl}

Deze sectie doorloopt snel de algemene eigenschappen van de Taal van het Malplaatje van HTML.

### Use-API for Access Logic {#use-api-for-accessing-logic}

Met de Java Use-API (HTML-sjabloontaal) van HTML (HTML) kan een HTML-bestand via `data-sly-use` toegang krijgen tot hulplijnmethoden in een aangepaste Java-klasse. Hierdoor kunnen alle complexe bedrijfslogica worden ingekapseld in de code van Java, terwijl de code HTML slechts op directe prijsverhogingsproductie behandelt.

Zie het document [ HTML Java gebruiken-API ](java-use-api.md) voor meer details.

### Automatisch contextgevoelig Escaping {#automatic-context-aware-escaping}

Neem bijvoorbeeld het volgende voorbeeld:

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

In de meeste sjabloontalen zou in dit voorbeeld mogelijk een XSS-kwetsbaarheid (cross-site scripting) worden gemaakt, omdat zelfs wanneer alle variabelen automatisch met HTML worden beschermd, het kenmerk `href` nog steeds specifiek met URL moet worden beschermd. Dit verzuim is een van de meest voorkomende fouten, omdat het gemakkelijk kan worden vergeten en het moeilijk is om op een geautomatiseerde manier te merken.

Om daarmee te helpen, ontsnapt de Taal van het Malplaatje van HTML automatisch aan elke variabele aan de context waarin het wordt geplaatst. Dit is mogelijk dankzij het feit dat HTL de syntaxis van HTML begrijpt.

Ervan uitgaande dat het volgende `logic.js` -bestand:

```javascript
use(function () {
    return {
        link:  "#my link's safe",
        title: "my title's safe",
        text:  "my text's safe"
    };
});
```

Het eerste voorbeeld resulteert vervolgens in de volgende uitvoer:

```xml
<p>
    <a href="#my%20link%27s%20safe" title="my title&#39;s safe">
        my text&#39;s safe
    </a>
</p>
```

U ziet dat de twee kenmerken anders zijn beschermd, omdat HTML weet dat de kenmerken `href` en `src` moeten worden beschermd voor de URI-context. Ook, als URI met `javascript:` begon, zou het attribuut volledig verwijderd zijn, tenzij de context uitdrukkelijk in iets anders werd veranderd.

Voor meer details over hoe te om het ontsnappen te controleren, verwijs naar de ](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) sectie van de Context van de Vertoning van de Taal van de Uitdrukking van de [ Uitdrukking van de Specificaties HTML.

### Automatische verwijdering van lege kenmerken {#automatic-removal-of-empty-attributes}

Neem bijvoorbeeld het volgende voorbeeld:

```xml
<p class="${properties.class}">some text</p>
```

Als de waarde van de eigenschap `class` leeg blijkt te zijn, verwijdert de Sjabloontaal van HTML automatisch het volledige `class` -kenmerk uit de uitvoer.

Nogmaals, dit is mogelijk, omdat HTML de syntaxis van de HTML begrijpt en daarom voorwaardelijk attributen met dynamische waarden kan tonen slechts als hun waarde niet leeg is. Dit is erg handig omdat u geen voorwaardeblok rond kenmerken hoeft toe te voegen, waardoor de markering ongeldig en onleesbaar zou zijn geworden.

Bovendien is het type van de variabele die in de expressie wordt geplaatst van belang:

* **Koord:**
   * **niet leeg:** plaatst het koord als attributenwaarde.
   * **leeg:** verwijdert de attributen helemaal.

* **Aantal:** plaatst de waarde als attributenwaarde.

* **Van Boole:**
   * **waar:** toont de attributen zonder waarde (als attribuut Van Boole HTML)
   * **vals:** verwijdert de attributen helemaal.

Hier is een voorbeeld van hoe een uitdrukking Van Boole controle van een attribuut van de HTML van Boole zou toestaan:

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

Voor het instellen van kenmerken kan de instructie `data-sly-attribute` ook nuttig zijn.

## Algemene patronen met HTML {#common-patterns-with-htl}

Deze sectie introduceert een paar gemeenschappelijke scenario&#39;s en hoe te om hen met de Taal van het Malplaatje van de HTML het best op te lossen.

### Client-bibliotheken laden {#loading-client-libraries}

In HTML worden clientbibliotheken geladen via een hulpsjabloon die wordt geleverd door AEM, die toegankelijk is via `data-sly-use` . Dit bestand bevat drie sjablonen, die kunnen worden aangeroepen via `data-sly-call` :

* **`css`** - Laadt alleen de CSS-bestanden van de clientbibliotheken waarnaar wordt verwezen.
* **`js`** - Laadt alleen de JavaScript-bestanden van de clientbibliotheken waarnaar wordt verwezen.
* **`all`** - Laadt alle bestanden van de clientbibliotheken waarnaar wordt verwezen (zowel CSS als JavaScript).

Elke hulpsjabloon verwacht een `categories` optie voor het verwijzen naar de gewenste clientbibliotheken. Deze optie kan ofwel een array van tekenreekswaarden zijn, ofwel een tekenreeks met een lijst met door komma&#39;s gescheiden waarden.

Hieronder volgen twee korte voorbeelden.

#### Meerdere clientbibliotheken volledig tegelijk laden {#loading-multiple-client-libraries-fully-at-once}

```xml
<sly data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html"
     data-sly-call="${clientlib.all @ categories=['myCategory1', 'myCategory2']}"/>
```

#### Verwijzen naar een clientbibliotheek in verschillende secties van een pagina {#referencing-a-client-library-in-different-sections-of-a-page}

```xml
<!doctype html>
<html data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html">
    <head>
        <!-- HTML meta-data -->
        <sly data-sly-call="${clientlib.css @ categories='myCategory'}"/>
    </head>
    <body>
        <!-- page content -->
        <sly data-sly-call="${clientlib.js @ categories='myCategory'}"/>
    </body>
</html>
```

In dit voorbeeld geldt dat als de HTML `head` - en `body` -elementen in verschillende bestanden worden geplaatst, de `clientlib.html` -sjabloon vervolgens in elk bestand moet worden geladen dat deze nodig heeft.

De sectie over het malplaatje &amp; vraagverklaringen in de [ specificatie HTML ](specification.md) verstrekt meer details over hoe het verklaren van en het roepen van dergelijke malplaatjes werken.

### Gegevens doorgeven aan de client {#passing-data-to-the-client}

De beste en meest elegante manier om gegevens door te geven aan de client in het algemeen, maar nog meer met HTML, is door `data` -kenmerken te gebruiken.

In het volgende voorbeeld wordt getoond hoe de logica (die ook in Java kan worden geschreven) kan worden gebruikt om op een handige manier aan JSON het object te serialiseren dat aan de client moet worden doorgegeven. Dit object kan vervolgens gemakkelijk in een `data` -kenmerk worden geplaatst:

```xml
<!--/* template.html file: */-->
<div data-sly-use.logic="logic.js" data-json="${logic.json}">...</div>
```

```javascript
/* logic.js file: */
use(function () {
    var myData = {
        str: "foo",
        arr: [1, 2, 3]
    };

    return {
        json: JSON.stringify(myData)
    };
});
```

Vanaf dat punt is het gemakkelijk om zich voor te stellen hoe een cliënt-kant JavaScript tot dat attribuut kan toegang hebben en opnieuw JSON parseren. Dit zou bijvoorbeeld de overeenkomstige JavaScript zijn die in een clientbibliotheek wordt geplaatst:

```javascript
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### Werken met clientsjablonen {#working-with-client-side-templates}

Één speciaal geval, waar de techniek die in de sectie [ wordt verklaard die Beperkingen van Speciale contexten opheffen ](#lifting-limitations-of-special-contexts) kan rechtmatig worden gebruikt, is cliënt-zijmalplaatjes (zoals Handlebars bijvoorbeeld) te schrijven die binnen `scrip` elementen worden gevestigd. De reden dat deze techniek in dat geval veilig kan worden gebruikt, is omdat het `script` -element dan geen JavaScript bevat zoals wordt aangenomen, maar verdere HTML-elementen. Hier is een voorbeeld van hoe dat zou werken:

```xml
<!--/* template.html file: */-->
<script id="entry-section" type="text/template"
    data-sly-include="entry-section.html"></script>

<!--/* entry-section.html file: */-->
<div class="entry-section">
    <h2 data-sly-test="${properties.entrySectionTitle}">
        ${properties.entrySectionTitle}
    </h2>
    {{#each entry}}<h3><a href="{{link}}">{{title}}</a></h3>{{/each}}
</div>
```

Zoals hierboven wordt getoond, kan de prijsverhoging die in het `script` element zal worden omvat het blokverklaringen van HTML en de uitdrukkingen te hoeven niet om expliciete contexten te verstrekken, omdat de inhoud van het malplaatje Handlebars in zijn eigen dossier geïsoleerd is. In dit voorbeeld wordt ook getoond hoe serverzijde uitgevoerde HTML (zoals in het `h2` -element) kan worden gemengd met een client-side uitgevoerde sjabloontaal, zoals Handlebars (weergegeven op het `h3` -element).

Een modernere techniek zou echter het HTML `template` -element gebruiken, omdat het dan beter is dat de inhoud van de sjablonen niet in aparte bestanden hoeft te worden geïsoleerd.

### Beperkingen van bijzondere contexten opheffen {#lifting-limitations-of-special-contexts}

In de speciale gevallen waarin de beperkingen van het script, de stijl en de commentaarcontext moeten worden omzeild, is het mogelijk de inhoud ervan te isoleren in een afzonderlijk HTML-bestand. Alles wat zich in een eigen bestand bevindt, wordt door HTML geïnterpreteerd als een normaal HTML-fragment, waarbij de beperkende context wordt vergeten waarvan het mogelijk is opgenomen.

Zie [ het Werken met cliënt-KantMalplaatjes ](#working-with-client-side-templates) sectie verder neer voor een voorbeeld.

>[!CAUTION]
>
>Deze techniek kan XSS-kwetsbaarheden (cross-site scripting) introduceren en de beveiligingsaspecten moeten zorgvuldig worden bestudeerd als dit moet worden gebruikt. Meestal zijn er betere manieren om hetzelfde te doen dan op deze praktijk te vertrouwen.
