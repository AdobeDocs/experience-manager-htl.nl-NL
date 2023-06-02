---
title: Aan de slag met HTML
description: Leer over HTML, het aangewezen en geadviseerde server-zijmalplaatjesysteem voor HTML in AEM, en begrijp de belangrijkste concepten van de taal en zijn fundamentele bouwstenen.
exl-id: c95eb1b3-3b96-4727-8f4f-d54e7136a8f9
source-git-commit: 88edbd2fd66de960460df5928a3b42846d32066b
workflow-type: tm+mt
source-wordcount: '2170'
ht-degree: 0%

---


# Aan de slag met HTML {#getting-started-with-htl}

HTML Sjabloontaal (HTL) is het voorkeurssjabloonsysteem en het aanbevolen sjabloonsysteem voor HTML in Adobe Experience Manager. Net als in alle HTML server-side sjabloonsystemen definieert een HTML-bestand de uitvoer die naar de browser wordt verzonden door de HTML zelf, een aantal basispresentatielogica en de variabelen die tijdens runtime moeten worden geëvalueerd, op te geven.

Dit document geeft een overzicht van het doel van HTML en een inleiding tot fundamentele concepten en constructies van de taal.

>[!TIP]
>
>In dit document wordt het doel van HTML uiteengezet en wordt een overzicht gegeven van de fundamentele structuur en concepten ervan. Als u vragen hebt over specifieke syntaxis, raadpleegt u de [HTML-specificatie.](specification.md)

## HTML-lagen {#layers}

HTML zoals gebruikt in AEM kan door een aantal lagen worden bepaald.

1. **[HTML-specificatie](specification.md)** - HTL is een open-source, platformagnostische specificatie, die iedereen vrij kan implementeren.
1. **[Sling HTML Scripting Engine](specification.md)** - Het Sling-project heeft de referentie-implementatie van HTML tot stand gebracht, die door AEM wordt gebruikt.
1. **[Extensies AEM](specification.md)** - AEM bouwt verder op de Sling HTML Scripting Engine om ontwikkelaars geschikte functies te bieden die specifiek zijn voor AEM.

Deze HTML-documentatie richt zich op het gebruik van HTML voor het ontwikkelen van AEM oplossingen. Als zodanig raakt het alle drie lagen, die externe middelen zonodig verbinden.

## Grondbeginselen van HTML {#fundamental-concepts-of-htl}

De Taal van het Malplaatje van de HTML gebruikt een uitdrukkingstaal om stukken van inhoud in de teruggegeven prijsverhoging op te nemen, en HTML5 gegevensattributen om verklaringen over blokken van prijsverhoging (als voorwaarden of herhalingen) te bepalen. Als HTML wordt gecompileerd in Java Servlets, worden de expressies en de HTML-gegevenskenmerken volledig geëvalueerd op de server en blijft er niets zichtbaar in de resulterende HTML.

>[!TIP]
>
>Als u de meeste voorbeelden op deze pagina wilt uitvoeren, wordt een live uitvoeringsomgeving de [Eval-afdruklap lezen](https://github.com/adobe/aem-htl-repl) kan worden gebruikt.

### Blokken en expressies {#blocks-and-expressions}

Hier is een eerste voorbeeld dat kan worden opgenomen zoals in een `template.html` bestand:

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

Er kunnen twee verschillende soorten syntaxen worden onderscheiden:

* **Instructies blokkeren** - De voorwaardelijke weergave van de `<h1>` element, a `data-sly-test` HTML5-gegevenskenmerk wordt gebruikt. HTL biedt meerdere van dergelijke kenmerken, waardoor gedrag aan elk HTML-element kan worden gekoppeld en alle kenmerken zijn vooraf ingesteld op `data-sly`.
* **Expressietaal** - HTML-expressies worden gescheiden door de `${` en `}` tekens. Tijdens de runtime worden deze expressies geëvalueerd en wordt de waarde ervan in de uitgaande HTML-stream geïnjecteerd.

Zie de [HTL-specificatie](specification.md) voor meer informatie over beide syntaxen.

### Het SLY-element {#the-sly-element}

Een centraal concept van HTML is de mogelijkheid te bieden om bestaande HTML-elementen opnieuw te gebruiken voor het definiëren van blokinstructies, waardoor het niet nodig is extra scheidingstekens in te voegen om te bepalen waar de instructie begint en eindigt. Deze onopvallende aantekening van de prijsverhoging om een statische HTML in een functionerend dynamisch malplaatje om te zetten biedt het voordeel om de geldigheid van de code van de HTML niet te breken, en daarom nog behoorlijk te tonen, zelfs als statische dossiers.

Soms is er echter geen bestaand element op de exacte locatie waar een blokinstructie moet worden ingevoegd. In dergelijke gevallen is het mogelijk een speciale `sly` -element dat automatisch wordt verwijderd uit de uitvoer tijdens het uitvoeren van de gekoppelde blokinstructies en het overeenkomstig weergeven van de inhoud.

In het volgende voorbeeld...

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

...zal iets als volgende HTML uitvoeren, maar slechts als er allebei zijn, `jcr:title` en `jcr:description` gedefinieerde eigenschap, en als geen van beide leeg zijn:

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

Het enige wat u in gedachten moet houden, is dat u de `sly` element when no existing element could be annoated with the block statement. Dit komt omdat `sly` elementen ontmoedigen de waarde die door de taal wordt aangeboden om de statische HTML niet te veranderen wanneer het dynamisch maken van het.

Als het vorige voorbeeld bijvoorbeeld zou zijn ondergebracht in een `div` element, dan toegevoegd `sly` het element zou misbruikt zijn :

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

en de `div` element had met de voorwaarde geannoteerd kunnen zijn:

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

HTML-opmerkingen zijn HTML-opmerkingen met een extra JavaScript-achtige syntaxis. De hele HTML-opmerking en alle elementen daarin worden volledig genegeerd door de processor en uit de uitvoer verwijderd.

De inhoud van de standaardopmerkingen voor HTML wordt echter doorgegeven en de expressies in de opmerking worden geëvalueerd.

HTML-opmerkingen kunnen geen HTML-opmerkingen bevatten en andersom.

### Speciale contexten {#special-contexts}

Om optimaal gebruik te kunnen maken van HTL is het belangrijk dat u goed begrijpt wat de gevolgen zijn van het feit dat deze gebaseerd is op de syntaxis van de HTML.

Raadpleeg de [Contextsectie weergeven](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#121-display-context) van de HTL-specificatie voor meer informatie.

### Element- en kenmerknamen {#element-and-attribute-names}

Expressies kunnen alleen in HTML-tekst of -kenmerkwaarden worden geplaatst, maar niet binnen elementnamen of kenmerknamen, of het zou geen geldige HTML meer zijn. Als u elementnamen dynamisch wilt instellen, kunt u de opdracht `data-sly-element` De instructie kan op de gewenste elementen worden gebruikt en om kenmerknamen dynamisch in te stellen, zelfs wanneer meerdere kenmerken tegelijk worden ingesteld, wordt de `data-sly-attribute` -instructie kan worden gebruikt.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### Context zonder instructies van het blok {#contexts-without-block-statements}

Aangezien HTML gegevenskenmerken gebruikt om blokinstructies te bepalen, is het niet mogelijk om dergelijke blokinstructies binnen van volgende contexten te bepalen, en slechts uitdrukkingen kunnen daar worden gebruikt:

* Opmerkingen HTML
* Scriptelementen
* Stijlelementen

De reden hiervoor is dat de inhoud van deze contexten tekst is en niet HTML, en dat ingesloten HTML-elementen worden beschouwd als eenvoudige tekengegevens. Zonder echte HTML-elementen kan er dus ook geen sprake zijn van `data-sly` uitgevoerde kenmerken.

Dit kan als een significante beperking klinken, nochtans is het gewenst, omdat de Taal van het Malplaatje van HTML niet zou moeten worden misbruikt om output te produceren die niet HTML is. De [Use-API for Access Logic](#use-api-for-accessing-logic) in de onderstaande sectie wordt uitgelegd hoe extra logica kan worden aangeroepen vanuit de sjabloon, die kan worden gebruikt als dit nodig is om complexe uitvoer voor deze contexten voor te bereiden. Bijvoorbeeld, een gemakkelijke manier om gegevens van het achterste eind naar een front-end manuscript te verzenden, is de logica van de component te hebben om een koord te produceren JSON, dat dan in een gegevensattribuut met een eenvoudige uitdrukking van HTML kan worden geplaatst.

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

Zoals uiteengezet in de [Automatisch contextgevoelig pannen](#automatic-context-aware-escaping) in de onderstaande sectie is één doel van HTML om de risico&#39;s van het introduceren van XSS-kwetsbaarheden (cross-site scripting) te verminderen door context-bewuste escape automatisch toe te passen op alle expressies. Hoewel HTML automatisch de context kan detecteren van expressies die in een HTML-opmaak zijn geplaatst, wordt de syntaxis van inline JavaScript of CSS niet geanalyseerd. Daarom is het afhankelijk van de ontwikkelaar om expliciet aan te geven welke exacte context op dergelijke expressies moet worden toegepast.

Aangezien het niet toepassen van de correcte escaping in XSS kwetsbaarheid resulteert, verwijdert HTL daarom de output van alle uitdrukkingen die in manuscript en stijlcontexten zijn wanneer de context niet is verklaard.

Hier volgt een voorbeeld van hoe u de context instelt voor expressies die in scripts en stijlen worden geplaatst:

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

Voor meer details over hoe te om het ontsnappen te controleren, verwijs naar [Weergavecontext van expressietaal](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) van de HTL-specificaties.

## Algemene mogelijkheden van HTL {#general-capabilities-of-htl}

Deze sectie doorloopt snel de algemene eigenschappen van de Taal van het Malplaatje van HTML.

### Use-API for Access Logic {#use-api-for-accessing-logic}

Met de Java Use-API (HTML-sjabloontaal) van HTML (HTML) kan een HTML-bestand via `data-sly-use`. Hierdoor kunnen alle complexe bedrijfslogica worden ingekapseld in de code van Java, terwijl de code HTML slechts op directe prijsverhogingsproductie behandelt.

Zie het document [HTML Java Use-API](java-use-api.md) voor meer informatie .

### Automatisch contextgevoelig pannen {#automatic-context-aware-escaping}

Neem bijvoorbeeld het volgende voorbeeld:

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

In de meeste sjabloontalen zou dit voorbeeld mogelijk een XSS-kwetsbaarheid (cross-site scripting) creëren, omdat zelfs wanneer alle variabelen automatisch op de HTML zijn ingesteld, de `href` kenmerk moet nog steeds specifiek URL-escaped zijn. Dit verzuim is een van de meest voorkomende fouten, omdat het gemakkelijk kan worden vergeten en het moeilijk is om op een geautomatiseerde manier te merken.

Om daarmee te helpen, ontsnapt de Taal van het Malplaatje van HTML automatisch aan elke variabele aan de context waarin het wordt geplaatst. Dit is mogelijk dankzij het feit dat HTL de syntaxis van HTML begrijpt.

Veronderstellend volgende `logic.js` bestand:

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

Let op: de twee kenmerken zijn anders beschermd, omdat HTL weet dat `href` en `src` attributen moeten voor de context van URI worden beschermd. Ook als de URI is gestart met `javascript:`, zou het attribuut volledig verwijderd zijn, tenzij de context uitdrukkelijk in iets anders werd veranderd.

Voor meer details over hoe te om het ontsnappen te controleren, verwijs naar [Weergavecontext van expressietaal](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) van de HTL-specificaties.

### Automatische verwijdering van lege kenmerken {#automatic-removal-of-empty-attributes}

Neem bijvoorbeeld het volgende voorbeeld:

```xml
<p class="${properties.class}">some text</p>
```

Als de waarde van de `class` de eigenschap is leeg, de HTML Sjabloontaal verwijdert automatisch de gehele `class` kenmerk van de uitvoer.

Nogmaals, dit is mogelijk, omdat HTML de syntaxis van de HTML begrijpt en daarom voorwaardelijk attributen met dynamische waarden kan tonen slechts als hun waarde niet leeg is. Dit is erg handig omdat er geen voorwaardeblok wordt toegevoegd rondom kenmerken, waardoor de markering ongeldig en onleesbaar zou zijn geworden.

Bovendien is het type van de variabele die in de expressie wordt geplaatst van belang:

* **Tekenreeks:**
   * **niet leeg:** Stelt de tekenreeks in als kenmerkwaarde.
   * **leeg:** Hiermee verwijdert u het kenmerk helemaal.

* **Aantal:** Hiermee wordt de waarde ingesteld als kenmerkwaarde.

* **Booleaans:**
   * **true:** Hiermee geeft u het kenmerk zonder waarde weer (als een Booleaans HTML-kenmerk)
   * **false:** Hiermee verwijdert u het kenmerk helemaal.

Hier is een voorbeeld van hoe een uitdrukking Van Boole controle van een attribuut van de HTML van Boole zou toestaan:

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

Als u kenmerken wilt instellen, `data-sly-attribute` kan ook nuttig zijn.

## Algemene patronen met HTML {#common-patterns-with-htl}

Deze sectie introduceert een paar gemeenschappelijke scenario&#39;s en hoe te om hen met de Taal van het Malplaatje van de HTML het best op te lossen.

### Client-bibliotheken laden {#loading-client-libraries}

In HTML worden clientbibliotheken geladen via een helpersjabloon die wordt geleverd door AEM en die toegankelijk is via `data-sly-use`. Dit bestand bevat drie sjablonen, die u kunt aanroepen via `data-sly-call`:

* **`css`** - Laadt alleen de CSS-bestanden van de clientbibliotheken waarnaar wordt verwezen.
* **`js`** - Hiermee worden alleen de JavaScript-bestanden geladen van de clientbibliotheken waarnaar wordt verwezen.
* **`all`** - Laadt alle bestanden van de clientbibliotheken waarnaar wordt verwezen (zowel CSS als JavaScript).

Elke hulpsjabloon verwacht een `categories` voor het verwijzen naar de gewenste clientbibliotheken. Deze optie kan ofwel een array van tekenreekswaarden zijn, ofwel een tekenreeks met een lijst met door komma&#39;s gescheiden waarden.

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

In dit voorbeeld geldt voor het geval dat de HTML `head` en `body` elementen worden in verschillende bestanden geplaatst, `clientlib.html` sjabloon moet dan worden geladen in elk bestand dat het nodig heeft.

De sectie over de sjabloon en de callinstructies in het dialoogvenster [HTL-specificatie](specification.md) verstrekt meer details over hoe het verklaren van en het roepen van dergelijke malplaatjes werken.

### Gegevens doorgeven aan de client {#passing-data-to-the-client}

De beste en meest elegante manier om gegevens door te geven aan de cliënt in het algemeen, maar nog meer in het geval van HTML, is om te gebruiken `data` kenmerken.

In het volgende voorbeeld wordt getoond hoe de logica (die ook in Java kan worden geschreven) kan worden gebruikt om het object dat aan de client moet worden doorgegeven, op een handige manier aan JSON te serialiseren. Dit kan vervolgens gemakkelijk in een `data` kenmerk:

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

Vanaf dat punt is het eenvoudig in te beelden hoe een client-side JavaScript toegang heeft tot dat kenmerk en de JSON opnieuw parseert. Dit zou bijvoorbeeld het overeenkomende JavaScript zijn dat in een clientbibliotheek wordt geplaatst:

```javascript
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### Werken met clientsjablonen {#working-with-client-side-templates}

Eén speciaal geval, waarbij de techniek in de sectie wordt toegelicht [Beperkingen van bijzondere contexten opheffen](#lifting-limitations-of-special-contexts) kan legitiem worden gebruikt, moet cliënt-zijmalplaatjes (zoals Handlebars bijvoorbeeld) schrijven die binnen worden gevestigd `scrip` elementen. De reden waarom deze techniek in dat geval veilig kan worden gebruikt, is omdat de `script` bevat dan niet zoals aangenomen JavaScript, maar meer HTML-elementen. Hier is een voorbeeld van hoe dat zou werken:

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

Zoals hierboven getoond, zal de prijsverhoging die in `script` Het element kan de blokverklaringen van HTML bevatten en de uitdrukkingen te hoeven niet om expliciete contexten te verstrekken, omdat de inhoud van het malplaatje Handlebars in zijn eigen dossier geïsoleerd is. In dit voorbeeld wordt ook getoond hoe server-side HTML heeft uitgevoerd (zoals op de `h2` element) kan met een cliënt-kant uitgevoerde malplaatjetaal, zoals Handlebars (getoond op worden gemengd `h3` element).

Een modernere techniek zou echter alleen kunnen bestaan uit het gebruik van de HTML `template` in plaats daarvan, aangezien het voordeel dan zou zijn dat het niet vereist om de inhoud van de malplaatjes in afzonderlijke dossiers te isoleren.

### Beperkingen van bijzondere contexten opheffen {#lifting-limitations-of-special-contexts}

In de speciale gevallen waarin de beperkingen van het script, de stijl en de commentaarcontext moeten worden omzeild, is het mogelijk de inhoud ervan te isoleren in een afzonderlijk HTML-bestand. Alles wat zich in een eigen bestand bevindt, wordt door HTML geïnterpreteerd als een normaal HTML-fragment, waarbij de beperkende context wordt vergeten waarvan het mogelijk is opgenomen.

Zie de [Werken met clientsjablonen](#working-with-client-side-templates) verder naar beneden voor een voorbeeld.

>[!CAUTION]
>
>Deze techniek kan XSS-kwetsbaarheden (cross-site scripting) introduceren en de beveiligingsaspecten moeten zorgvuldig worden bestudeerd als dit moet worden gebruikt. Meestal zijn er betere manieren om hetzelfde te doen dan op deze praktijk te vertrouwen.
