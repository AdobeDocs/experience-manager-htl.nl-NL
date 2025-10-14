---
title: Aan de slag met HTML
description: Leer over HTML, het aangewezen en geadviseerde server-zijmalplaatjesysteem voor HTML in AEM, en begrijp de belangrijkste concepten van de taal en zijn fundamentele bouwstenen.
exl-id: c95eb1b3-3b96-4727-8f4f-d54e7136a8f9
source-git-commit: a496d23277902a5cd573a6a8af770f27b0269f05
workflow-type: tm+mt
source-wordcount: '2077'
ht-degree: 0%

---


# Aan de slag met HTML {#getting-started-with-htl}

HTML Template Language (HTL) is het voorkeurssjabloonsysteem voor servers in HTML in Adobe Experience Manager. Net als in alle HTML-serversjabloonsystemen definieert een HTML-bestand de uitvoer die naar de browser wordt verzonden door de HTML zelf, een aantal basispresentatielogica en variabelen die tijdens de runtime moeten worden geëvalueerd, op te geven.

Dit document geeft een overzicht van het doel van HTML en een inleiding tot fundamentele concepten en constructies van de taal.

>[!TIP]
>
>**hebt u Edge Delivery Services voor AEM overwogen?**
>
>U kunt de methoden die in dit document worden beschreven, blijven gebruiken voor bestaande projecten. Nochtans, voor nieuwe projecten, adviseert Adobe leveraging [&#x200B; Edge Delivery Services.](https://experienceleague.adobe.com/nl/docs/experience-manager-cloud-service/content/edge-delivery/overview)

>[!TIP]
>
>In dit document wordt het doel van HTL uiteengezet en wordt een overzicht gegeven van de fundamentele structuur en concepten ervan. Als u vragen over specifieke syntaxis hebt, zie de [&#x200B; specificatie van HTML &#x200B;](specification.md).

## HTML-lagen {#layers}

In AEM definieert een aantal lagen HTML.

1. **[de Specificatie van HTML](specification.md)** - HTML is een open-bron, platform-agnostieke specificatie, die iedereen vrij is uit te voeren.
1. **[`Sling`HTML Scripting Engine](specification.md)** - Het `Sling` -project heeft de referentie-implementatie van HTML gemaakt, die door AEM wordt gebruikt.
1. **[de Uitbreidingen van AEM](specification.md)** - AEM bouwt bovenop de `Sling` HTML Scripting Motor voort om ontwikkelaars geschikte eigenschappen specifiek voor AEM aan te bieden.

Deze HTML-documentatie richt zich op het gebruik van HTML voor het ontwikkelen van AEM-oplossingen. Als zodanig raakt het alle drie lagen, die externe middelen zonodig verbinden.

## Grondbeginselen van HTML {#fundamental-concepts-of-htl}

De taal van het Malplaatje van HTML gebruikt een uitdrukkingstaal om stukken van inhoud in de teruggegeven prijsverhoging op te nemen, en HTML5 gegevensattributen om verklaringen over blokken van prijsverhoging (zoals voorwaarden of herhalingen) te bepalen. Als HTML wordt gecompileerd in Java Servlets, worden de expressies en de HTML-gegevenskenmerken volledig geëvalueerd op de server en blijft er niets zichtbaar in de resulterende HTML.

>[!TIP]
>
>Om de meeste die voorbeelden in werking te stellen op deze pagina worden verstrekt, kan een levende uitvoeringsmilieu [&#x200B; Gelezen Lijn van de Druk van de Ovaal &#x200B;](https://github.com/adobe/aem-htl-repl) worden gebruikt.

### Blokken en expressies {#blocks-and-expressions}

Hier volgt een eerste voorbeeld, dat kan worden opgenomen zoals in een `template.html` -bestand:

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

Er kunnen twee verschillende soorten syntaxen worden onderscheiden:

* **Verklaringen van het Blok** - als u het `<h1>` element voorwaardelijk wilt tonen, gebruik a `data-sly-test` HTML5 gegevensattribuut. HTML biedt meerdere van dergelijke kenmerken, waardoor gedrag aan elk HTML-element kan worden gekoppeld. Deze kenmerken worden allemaal voorafgegaan door `data-sly` .
* **Taal van de Uitdrukking** - de `${` en `}` karakters scheiden de uitdrukkingen van HTML. Tijdens runtime worden deze expressies geëvalueerd en wordt de waarde ervan in de uitgaande HTML-stream geïnjecteerd.

Zie de [&#x200B; specificatie van HTML &#x200B;](specification.md) voor details op beide syntaxis.

### Het SLY-element {#the-sly-element}

Een centraal concept van HTML is de mogelijkheid te bieden bestaande HTML-elementen opnieuw te gebruiken om blokverklaringen te definiëren. Als u deze instructie opnieuw gebruikt, hoeft u geen extra scheidingstekens in te voegen om te definiëren waar de instructie begint en eindigt. Door de markering op onopvallende wijze te annoteren, verandert u statische HTML in een dynamische sjabloon zonder de geldigheid van HTML te onderbreken, zodat de markering ook correct wordt weergegeven als statische bestanden.

Soms is er echter geen bestaand element op de exacte locatie waar een blokinstructie moet worden ingevoegd. In dergelijke gevallen kunt u een speciaal `sly` -element invoegen. Dit element wordt automatisch verwijderd uit de uitvoer terwijl de instructies voor het gekoppelde blok worden uitgevoerd en de inhoud overeenkomstig wordt weergegeven.

In het volgende voorbeeld:

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

Voert iets als het volgende HTML uit, maar alleen als er zowel een eigenschap `jcr:title` als een eigenschap `jcr:description` is gedefinieerd en als geen van beide leeg zijn:

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

Een ding om in mening te houden is slechts het `sly` element te gebruiken wanneer geen bestaand element met de blokverklaring kon geannoteerd zijn. De reden hiervoor is dat `sly` -elementen de waarde afleiden die de taal aanbiedt om de statische HTML niet te wijzigen wanneer deze dynamisch wordt.

Als het vorige voorbeeld bijvoorbeeld zou zijn ondergebracht in een `div` -element, zou het toegevoegde `sly` -element misbruik maken:

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

En het element `div` had met de voorwaarde geannoteerd kunnen zijn:

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

HTML-opmerkingen zijn HTML-opmerkingen met een extra, op JavaScript lijkende syntaxis. De processor negeert volledig de hele HTML-opmerking en alles daarbinnen, waardoor deze uit de uitvoer wordt verwijderd.

De inhoud van standaard HTML-opmerkingen wordt echter doorgegeven en expressies in de opmerking worden geëvalueerd.

HTML-opmerkingen kunnen geen HTML-opmerkingen bevatten en andersom.

### Speciale contexten {#special-contexts}

Om HTML optimaal te kunnen gebruiken, is het belangrijk om goed te begrijpen wat de gevolgen zijn van het feit dat het gebaseerd is op de syntaxis van HTML.

Gelieve te verwijzen naar de [&#x200B; sectie van de Context van de Vertoning &#x200B;](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#121-display-context) van de specificatie HTML voor meer details.

### Element- en kenmerknamen {#element-and-attribute-names}

Expressies kunnen alleen worden geplaatst in HTML-tekst- of kenmerkwaarden, maar niet in elementnamen of kenmerknamen, of het zou geen geldige HTML meer zijn. Als u elementnamen dynamisch wilt instellen, kunt u de instructie `data-sly-element` gebruiken voor de gewenste elementen en om kenmerknamen dynamisch in te stellen, zelfs als u meerdere kenmerken tegelijk instelt, kunt u de instructie `data-sly-attribute` gebruiken.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### Context zonder instructies van het blok {#contexts-without-block-statements}

Aangezien HTML gegevenskenmerken gebruikt voor het definiëren van blokinstructies, is het niet mogelijk om dergelijke blokinstructies te definiëren binnen de volgende context, en kunnen daar alleen expressies worden gebruikt:

* HTML-opmerkingen
* Scriptelementen
* Stijlelementen

De reden hiervoor is dat de inhoud van deze contexten tekst is en niet HTML, en HTML-elementen bevat, worden beschouwd als eenvoudige tekengegevens. Zonder echte HTML-elementen kunnen er dus ook geen `data-sly` -kenmerken worden uitgevoerd.

Deze benadering kan als een significante beperking klinken. Deze optie heeft echter de voorkeur omdat de HTML Template Language alleen geldige HTML-uitvoer moet genereren. [&#x200B; gebruik-API voor de Toegang tot van Logische &#x200B;](#use-api-for-accessing-logic) sectie introduceert hieronder hoe de extra logica van het malplaatje kan worden geroepen, dat kan worden gebruikt als het nodig is om complexe output voor deze contexten voor te bereiden. Om gegevens van het achterste eind naar een front-end manuscript te verzenden, produceer een koord JSON met de logica van de component en plaats het in een gegevensattribuut gebruikend een eenvoudige uitdrukking van HTML.

In het volgende voorbeeld wordt het gedrag voor HTML-opmerkingen getoond, maar in script- of stijlelementen wordt hetzelfde gedrag waargenomen:

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

Voert iets als de volgende HTML uit:

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### Expliciete vereiste contexten {#explicit-contexts-required}

Zoals verklaard in de [&#x200B; Automatische Context-bewust Escaping &#x200B;](#automatic-context-aware-escaping) hieronder sectie, één doel van HTML is de risico&#39;s te verminderen om dwars-plaats scripting (XSS) kwetsbaarheid in te voeren door context-bewuste het ontsnapen aan alle uitdrukkingen automatisch toe te passen. HTML detecteert de context van expressies in HTML-opmaakcode maar analyseert inline JavaScript of CSS niet. Ontwikkelaars moeten daarom de exacte context voor deze expressies opgeven.

Aangezien het niet toepassen van de correcte escaping in XSS kwetsbaarheid resulteert, verwijdert HTL daarom de output van alle uitdrukkingen die in manuscript en stijlcontexten zijn wanneer de context niet is verklaard.

Hier volgt een voorbeeld van hoe u de context instelt voor expressies die in scripts en stijlen worden geplaatst:

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

Voor meer details over hoe te om het ontsnappen te controleren, verwijs naar de [&#x200B; sectie van de Context van de Vertoning van de Taal van de Uitdrukking van de &#x200B;](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) Uitdrukking van de Specificaties HTML.

## Algemene mogelijkheden van HTL {#general-capabilities-of-htl}

Deze sectie doorloopt snel de algemene eigenschappen van de Taal van het Malplaatje van HTML.

### Use-API for Access Logic {#use-api-for-accessing-logic}

Met de Java Use-API (HTML-sjabloontaal) van HTML Template Language kan een HTML-bestand via `data-sly-use` toegang krijgen tot hulpmethoden in een aangepaste Java-klasse. Dit proces laat alle complexe bedrijfslogica toe om in de code van Java worden ingekapseld, terwijl de code HTML slechts op directe prijsverhogingsproductie behandelt.

Zie het document [&#x200B; HTML Java gebruiken-API &#x200B;](java-use-api.md) voor meer details.

### Automatisch contextgevoelig Escaping {#automatic-context-aware-escaping}

Bekijk het volgende voorbeeld:

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

In de meeste sjabloontalen zou dit voorbeeld mogelijk een XSS-kwetsbaarheid (cross-site scripting) creëren, omdat zelfs wanneer alle variabelen automatisch door HTML worden beschermd, het kenmerk `href` nog steeds specifiek door URL moet worden beschermd. Dit verzuim is een van de meest voorkomende fouten, omdat het gemakkelijk kan worden vergeten en het moeilijk is om op een geautomatiseerde manier te merken.

Om te helpen, ontsnapt de Taal van het Malplaatje van HTML automatisch elke variabele aan de context waarin het wordt geplaatst. Deze benadering is mogelijk dankzij het feit dat HTL de syntaxis van HTML begrijpt.

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

Het eerste voorbeeld resulteert in de volgende uitvoer:

```xml
<p>
    <a href="#my%20link%27s%20safe" title="my title&#39;s safe">
        my text&#39;s safe
    </a>
</p>
```

U ziet dat de twee kenmerken anders zijn beschermd, omdat HTML weet dat de kenmerken `href` en `src` moeten worden beschermd voor de URI-context. Ook, als URI met `javascript:` begon, zou het attribuut volledig verwijderd zijn, tenzij de context uitdrukkelijk in iets anders werd veranderd.

Voor meer details over hoe te om het ontsnappen te controleren, verwijs naar de [&#x200B; sectie van de Context van de Vertoning van de Taal van de Uitdrukking van de &#x200B;](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) Uitdrukking van de Specificaties HTML.

### Automatische verwijdering van lege kenmerken {#automatic-removal-of-empty-attributes}

Bekijk het volgende voorbeeld:

```xml
<p class="${properties.class}">some text</p>
```

Als de waarde van de eigenschap `class` leeg blijkt te zijn, verwijdert de HTML-sjabloontaal automatisch het volledige kenmerk `class` uit de uitvoer.

Dit proces is ook hier mogelijk omdat HTML de syntaxis van HTML begrijpt en daarom alleen attributen met dynamische waarden voorwaardelijk kan weergeven als hun waarde niet leeg is. De reden is erg handig omdat er geen voorwaardeblok wordt toegevoegd rondom kenmerken, waardoor de markering ongeldig en onleesbaar zou zijn geworden.

Bovendien is het type van de variabele die in de expressie wordt geplaatst van belang:

* **Koord:**
   * **niet leeg:** plaatst het koord als attributenwaarde.
   * **leeg:** verwijdert de attributen helemaal.

* **Aantal:** plaatst de waarde als attributenwaarde.

* **Van Boole:**
   * **waar:** toont de attributen zonder waarde (als attribuut Van Boole HTML
   * **vals:** verwijdert de attributen helemaal.

Hier is een voorbeeld van hoe een uitdrukking Van Boole controle van een attribuut van HTML van Boole zou toestaan:

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

Voor het instellen van kenmerken kan de instructie `data-sly-attribute` ook nuttig zijn.

## Algemene patronen met HTML {#common-patterns-with-htl}

In deze sectie worden enkele algemene scenario&#39;s geïntroduceerd. Hierin wordt uitgelegd hoe u deze scenario&#39;s het beste kunt oplossen met de HTML-sjabloontaal.

### Client-bibliotheken laden {#loading-client-libraries}

In HTML worden clientbibliotheken geladen via een door AEM verschafte helpersjabloon, die toegankelijk is via `data-sly-use` . Dit bestand bevat drie sjablonen, die kunnen worden aangeroepen via `data-sly-call` :

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

In dit voorbeeld geldt dat als de HTML `head` - en `body` -elementen zich in afzonderlijke bestanden bevinden, de `clientlib.html` -sjabloon moet worden geladen in elk bestand dat deze vereist.

De sectie over het malplaatje &amp; vraagverklaringen in de [&#x200B; specificatie HTML &#x200B;](specification.md) verstrekt meer details over hoe het verklaren van en het roepen van dergelijke malplaatjes werken.

### Gegevens doorgeven aan de client {#passing-data-to-the-client}

De beste en meest elegante manier om gegevens door te geven aan de client in het algemeen, maar nog meer met HTML, is door `data` -kenmerken te gebruiken.

In het volgende voorbeeld ziet u hoe u een object serialiseert naar JSON (ook mogelijk in Java) om het door te geven aan de client. Vervolgens kan het eenvoudig in een `data` -kenmerk worden geplaatst:

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

Vanaf dat punt is het gemakkelijk om zich voor te stellen hoe een cliënt-kant JavaScript tot dat attribuut kan toegang hebben en opnieuw JSON parseren. Deze benadering zou de overeenkomstige JavaScript zijn om in een cliëntbibliotheek te plaatsen, bijvoorbeeld:

```javascript
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### Werken met clientsjablonen {#working-with-client-side-templates}

Één speciaal geval, waar de techniek die in de sectie [&#x200B; wordt verklaard die Beperkingen van Speciale contexten opheffen &#x200B;](#lifting-limitations-of-special-contexts) kan rechtmatig worden gebruikt, is cliënt-zijmalplaatjes (zoals Handlebars bijvoorbeeld) te schrijven die binnen `scrip` elementen worden gevestigd. De reden dat deze techniek in dat geval veilig kan worden gebruikt, is omdat het `script` -element dan geen JavaScript bevat zoals wordt aangenomen, maar meer HTML-elementen. Hier is een voorbeeld van hoe dat zou werken:

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

De opmaak van het element `script` kan HTML-blokinstructies zonder expliciete contexten bevatten, aangezien de inhoud van de sjabloon Handlebars geïsoleerd is in het eigen bestand. In dit voorbeeld wordt ook getoond hoe HTML aan de serverzijde (zoals in het `h2` -element) kan worden gemengd met een Sjabloontaal aan de clientzijde, zoals Handlebars (weergegeven op het `h3` -element).

Een modernere techniek zou echter het HTML `template` -element gebruiken, omdat het dan nuttig zou zijn de inhoud van de sjablonen niet in afzonderlijke bestanden te isoleren.

### Beperkingen van bijzondere contexten opheffen {#lifting-limitations-of-special-contexts}

In de speciale gevallen waarin de beperkingen van het script, de stijl en de commentaarcontext moeten worden omzeild, is het mogelijk de inhoud ervan te isoleren in een afzonderlijk HTML-bestand. HTML interpreteert alles in zijn eigen bestand als een standaard HTML-fragment en negeert elke beperkende context vanwaar het werd opgenomen.

Zie [&#x200B; het Werken met cliënt-KantMalplaatjes &#x200B;](#working-with-client-side-templates) sectie verder neer voor een voorbeeld.

>[!CAUTION]
>
>Deze techniek kan XSS-kwetsbaarheden (cross-site scripting) introduceren. De veiligheidsaspecten moeten zorgvuldig worden bestudeerd als deze aanpak wordt gebruikt. Meestal zijn er betere manieren om hetzelfde te doen dan op deze praktijk te vertrouwen.
