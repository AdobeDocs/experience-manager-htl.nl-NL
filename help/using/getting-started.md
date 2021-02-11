---
title: Aan de slag met HTML
description: HTML die door AEM wordt ondersteund, vervangt JSP als het voorkeurssjabloonsysteem en het aanbevolen sjabloonsysteem voor de server voor HTML in AEM.
translation-type: tm+mt
source-git-commit: f7e46aaac2a4b51d7fa131ef46692ba6be58d878
workflow-type: tm+mt
source-wordcount: '2471'
ht-degree: 0%

---


# Aan de slag met HTML {#getting-started-with-htl}

De HTML Template Language (HTML) die door Adobe Experience Manager (AEM) wordt gesteund is het aangewezen en geadviseerde server-zijmalplaatjesysteem voor HTML in AEM. Deze pagina komt in de plaats van JSP (JavaServer Pages), zoals in eerdere versies van AEM werd gebruikt.

>[!NOTE]
>
>Voor het uitvoeren van de meeste voorbeelden op deze pagina kunt u een live uitvoeringsomgeving gebruiken met de naam [Eval Print Loop](https://github.com/Adobe-Marketing-Cloud/aem-htl-repl) lezen.

## HTL over JSP {#htl-over-jsp}

Het wordt aanbevolen dat nieuwe AEM projecten de HTML-sjabloontaal gebruiken, aangezien deze meerdere voordelen biedt in vergelijking met JSP. Voor bestaande projecten heeft een migratie echter alleen zin als deze naar schatting minder moeite kost dan het handhaven van de bestaande JSP&#39;s voor de komende jaren.

Maar het bewegen naar HTML is niet noodzakelijk een alleszins keus, omdat de componenten die in HTML worden geschreven compatibel zijn met componenten die in JSP of ESP worden geschreven. Betekenis dat de bestaande projecten zonder een probleem HTML voor nieuwe componenten kunnen gebruiken, terwijl het houden van JSP voor bestaande componenten.

Zelfs binnen dezelfde component, kunnen de HTML- dossiers naast JSPs en ESPs worden gebruikt. In het volgende voorbeeld wordt op **regel 1** getoond hoe u een HTML-bestand uit een JSP-bestand opneemt en op **regel 2** hoe u een JSP-bestand uit een HTML-bestand kunt opnemen:

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

### Veelgestelde vragen{#frequently-asked-questions}

Voordat u aan de slag gaat met de HTML-sjabloontaal, moeten we beginnen met het beantwoorden van vragen die betrekking hebben op het onderwerp JSP en HTL.

**Heeft HTML beperkingen die JSP niet kent?** - HTL heeft in vergelijking met JSP eigenlijk geen beperkingen in die zin dat wat met JSP kan worden gedaan ook haalbaar zou moeten zijn met HTL. HTML is echter in verschillende opzichten strenger dan JSP en wat in één JSP-bestand kan worden bereikt, moet mogelijk worden gescheiden in een Java-klasse of een JavaScript-bestand om haalbaar te zijn in HTML. Maar dit is over het algemeen gewenst om een goede scheiding tussen de logica en de opmaakcode te waarborgen.

**Biedt HTML ondersteuning voor JSP-tagbibliotheken?** - Nee, maar zoals wordt getoond in de  [Loading Client ](getting-started.md#loading-client-libraries) Library, bieden de  [sjabloon en ](block-statements.md#template-call) callstatements een vergelijkbaar patroon.

**Kunnen de HTML-functies worden uitgebreid voor een AEM project?** - Nee, dat kan niet. HTML heeft krachtige uitbreidingsmechanismen voor hergebruik van logica - [Gebruik-API](getting-started.md#use-api-for-accessing-logic) - en van prijsverhoging (de [malplaatje &amp; call](block-statements.md#template-call) verklaringen), die kunnen worden gebruikt om de code van projecten te modulariseren.

**Wat zijn de belangrijkste voordelen van HTL ten opzichte van JSP?** - Veiligheid en efficiëntie van projecten zijn de belangrijkste voordelen, die in het  [overzicht](overview.md) worden beschreven.

**Zal JSP uiteindelijk verdwijnen?** - Op dit moment zijn er geen plannen in die richting.

## Fundamentele concepten van HTML {#fundamental-concepts-of-htl}

De taal van het Malplaatje van HTML gebruikt een uitdrukkingstaal om stukken van inhoud in de teruggegeven prijsverhoging op te nemen, en de gegevensattributen van HTML5 om verklaringen over blokken van prijsverhoging (zoals voorwaarden of herhalingen) te bepalen. Als HTML wordt gecompileerd in Java Servlets, worden de expressies en de HTML-gegevenskenmerken volledig geëvalueerd op de server en blijft er niets zichtbaar in de resulterende HTML.

### Blokken en expressies {#blocks-and-expressions}

Hier is een eerste voorbeeld, dat zou kunnen worden bevat zoals in een **`template.html`** dossier is:

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

Er kunnen twee verschillende soorten syntaxen worden onderscheiden:

* **[Instructies](block-statements.md)**  blokkeren - Als u de instructies voorwaardelijk wilt weergeven  **&lt;h1>** -element, wordt een  [`data-sly-test`](block-statements.md#test) HTML5-gegevenskenmerk gebruikt. HTL biedt meerdere van dergelijke kenmerken, waarmee gedrag aan elk HTML-element kan worden gekoppeld. Deze kenmerken worden allemaal voorafgegaan door `data-sly`.

* **[Expressietaal](expression-language.md)**  - HTML-expressies worden gescheiden door tekens  `${` en  `}`. Tijdens runtime worden deze expressies geëvalueerd en wordt de waarde ervan in de uitgaande HTML-stream geïnjecteerd.

De twee bovenstaande pagina&#39;s bevatten een gedetailleerde lijst met functies die beschikbaar zijn voor syntaxis.

### Het SLY-element {#the-sly-element}

Een centraal concept van HTML is de mogelijkheid te bieden om bestaande HTML-elementen opnieuw te gebruiken voor het definiëren van blokinstructies, waardoor het niet nodig is extra scheidingstekens in te voegen om te definiëren waar de instructie begint en eindigt. Deze onopvallende annotatie van de markering om statische HTML in een functionerende dynamische malplaatje om te zetten biedt het voordeel om de geldigheid van de code van HTML niet te breken, en daarom nog behoorlijk te tonen, zelfs als statische dossiers.

Soms is er echter geen bestaand element op de exacte locatie waar een blokinstructie moet worden ingevoegd. In dergelijke gevallen is het mogelijk een speciaal SLY-element in te voegen dat automatisch wordt verwijderd uit de uitvoer, terwijl de bijgevoegde blokinstructies worden uitgevoerd en de inhoud dienovereenkomstig wordt weergegeven.

In het volgende voorbeeld:

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

zal iets als volgende HTML uitvoeren, maar slechts als er allebei zijn, worden bepaald **`jcr:title`** en een **`jcr:description`** bezit, en als geen van beiden leeg zijn:

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

Één ding in mening te houden is het SLY element slechts te gebruiken wanneer geen bestaand element met de blokverklaring kon worden geannoteerd, omdat de elementen van de SLY de waarde ontmoedigen die door de taal wordt aangeboden om statische HTML niet te veranderen wanneer het dynamisch maakt.

Als het vorige voorbeeld bijvoorbeeld al in een DIV-element zou zijn ondergebracht, zou het toegevoegde SLY-element misbruik maken:

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

en het element DIV had met de voorwaarde geannoteerd kunnen zijn:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

### HTML-opmerkingen {#htl-comments}

In het volgende voorbeeld wordt op **regel 1** een HTML-opmerking weergegeven en op **regel 2** een HTML-opmerking:

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

HTML-opmerkingen zijn HTML-opmerkingen met een extra JavaScript-achtige syntaxis. De hele HTML-opmerking en alle elementen daarin worden volledig genegeerd door de processor en uit de uitvoer verwijderd.

De inhoud van standaard HTML-opmerkingen wordt echter doorgegeven en expressies in de opmerking worden geëvalueerd.

HTML-opmerkingen kunnen geen HTML-opmerkingen bevatten en andersom.

### Speciale contexten {#special-contexts}

Om HTML optimaal te kunnen gebruiken, is het belangrijk om goed te begrijpen wat de gevolgen zijn van het gebaseerd zijn op de HTML-syntaxis.

### Elementen- en kenmerknamen {#element-and-attribute-names}

Expressies kunnen alleen in HTML-tekst of -kenmerkwaarden worden geplaatst, maar niet in elementnamen of kenmerknamen, of het zou geen geldige HTML meer zijn. Om elementnamen dynamisch in te stellen, kan de [`data-sly-element`](block-statements.md#element) verklaring op de gewenste elementen worden gebruikt, en om attributennamen dynamisch te plaatsen, zelfs het plaatsen van veelvoudige attributen tegelijkertijd, kan [`data-sly-attribute`](block-statements.md#attribute) verklaring worden gebruikt.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### Context zonder blokinstructies {#contexts-without-block-statements}

Aangezien HTML gegevenskenmerken gebruikt om blokinstructies te bepalen, is het niet mogelijk om dergelijke blokinstructies binnen van volgende contexten te bepalen, en slechts uitdrukkingen kunnen daar worden gebruikt:

* HTML-opmerkingen
* Scriptelementen
* Stijlelementen

De reden hiervoor is dat de inhoud van deze contexten tekst is en niet HTML, en HTML-elementen bevat die worden beschouwd als eenvoudige tekengegevens. Zonder echte HTML-elementen kunnen er dus ook geen **`data-sly`**-kenmerken worden uitgevoerd.

Dit kan als een grote beperking klinken, nochtans is het gewenst, omdat de Taal van het Malplaatje van HTML niet zou moeten worden misbruikt om output te produceren die geen HTML is. In de sectie [Use-API for Access Logic](getting-started.md#use-api-for-accessing-logic) hieronder wordt uitgelegd hoe extra logica kan worden aangeroepen vanuit de sjabloon. Deze kan worden gebruikt als complexe uitvoer voor deze contexten moet worden voorbereid. Bijvoorbeeld, een gemakkelijke manier om gegevens van het achterste eind naar een front-end manuscript te verzenden, is de logica van de component te hebben om een koord te produceren JSON, dat dan in een gegevensattribuut met een eenvoudige uitdrukking van HTML kan worden geplaatst.

In het volgende voorbeeld wordt het gedrag voor HTML-opmerkingen getoond, maar in script- of stijlelementen wordt hetzelfde gedrag waargenomen:

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

zal iets uitvoeren als de volgende HTML:

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### Expliciete vereiste contexten {#explicit-contexts-required}

Zoals in de [Automatische Context-Aware sectie ](getting-started.md#automatic-context-aware-escaping) hieronder wordt verklaard, één doel van HTML is de risico&#39;s van het invoeren van dwars-plaats scripting (XSS) kwetsbaarheid te verminderen door context-bewuste ontsnapt automatisch toe te passen op alle uitdrukkingen. Hoewel HTML automatisch de context kan detecteren van expressies die in HTML-opmaak zijn geplaatst, wordt de syntaxis van inline JavaScript of CSS niet geanalyseerd. Daarom is het afhankelijk van de ontwikkelaar om expliciet aan te geven welke exacte context op dergelijke expressies moet worden toegepast.

Aangezien het niet toepassen van de correcte escaping in XSS kwetsbaarheid resulteert, verwijdert HTL daarom de output van alle uitdrukkingen die in manuscript en stijlcontexten zijn wanneer de context niet is verklaard.

Hier volgt een voorbeeld van hoe u de context instelt voor expressies die in scripts en stijlen worden geplaatst:

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

Voor meer details over hoe te om het ontsnapen te controleren, verwijs naar de [sectie van de Vertoning van de Taal van de Uitdrukking Context](expression-language.md#display-context).

### Beperkingen van speciale contexten opheffen {#lifting-limitations-of-special-contexts}

In de speciale gevallen waarin de beperkingen van het script, de stijl en de commentaarcontext moeten worden omzeild, is het mogelijk de inhoud ervan te isoleren in een afzonderlijk HTML-bestand. Alles wat zich in een eigen bestand bevindt, wordt door HTML geïnterpreteerd als een normaal HTML-fragment. Hierbij wordt de beperkende context vergeten waarvan het fragment mogelijk is opgenomen.

Zie de sectie [Werken met clientsjablonen](getting-started.md#working-with-client-side-templates) verderop in het voorbeeld.

>[!CAUTION]
>
>Deze techniek kan XSS-kwetsbaarheden (cross-site scripting) introduceren en de beveiligingsaspecten moeten zorgvuldig worden bestudeerd als dit wordt gebruikt. Meestal zijn er betere manieren om hetzelfde te doen dan op deze praktijk te vertrouwen.

## Algemene mogelijkheden van HTML {#general-capabilities-of-htl}

Deze sectie doorloopt snel de algemene eigenschappen van de Taal van het Malplaatje van HTML.

### Use-API for Access Logic {#use-api-for-accessing-logic}

Neem bijvoorbeeld het volgende voorbeeld:

```xml
<p data-sly-use.logic="logic.js">${logic.title}</p>
```

En volgende `logic.js` serverzijde uitgevoerd JavaScript-bestand geplaatst naast het:

```javascript
use(function () {
    return {
        title: currentPage.getTitle().substring(0, 10) + "..."
    };
});
```

Aangezien de Taal van het Malplaatje van HTML niet toestaat om code binnen de prijsverhoging te mengen, biedt het in plaats daarvan het gebruik-API uitbreidingsmechanisme om code van het malplaatje gemakkelijk uit te voeren.

In het bovenstaande voorbeeld wordt via de server uitgevoerde JavaScript-code gebruikt om de titel in te korten tot 10 tekens, maar Java-code kan hiervoor ook worden gebruikt door een volledig gekwalificeerde Java-klassenaam op te geven. In het algemeen moet bedrijfslogica in Java worden ingebouwd, maar als de component bepaalde weergavespecifieke wijzigingen nodig heeft van wat de Java API biedt, kan het handig zijn om bepaalde uitgevoerde JavaScript op de server te gebruiken.

Meer informatie hierover vindt u in de volgende secties:

* In de sectie op de instructie [`data-sly-use`](block-statements.md#use) wordt alles uitgelegd wat u met die instructie kunt doen.
* Op de pagina [Use-API](use-api.md) vindt u informatie waarmee u kunt kiezen tussen het schrijven van de logica in Java of in JavaScript.
* En om te specificeren hoe te om de logica te schrijven, zouden [JavaScript gebruiken-API](use-api-javascript.md) en [Java gebruiken-API](use-api-java.md) pagina&#39;s moeten helpen.

### Automatisch contextgevoelig maken {#automatic-context-aware-escaping}

Neem bijvoorbeeld het volgende voorbeeld:

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

In de meeste sjabloontalen zou dit voorbeeld potentieel een XSS-kwetsbaarheid (cross-site scripting) creëren, omdat zelfs wanneer alle variabelen automatisch door HTML worden beschermd, het `href` attribuut nog specifiek URL-ontsnapt moet zijn. Dit verzuim is een van de meest voorkomende fouten, omdat het heel gemakkelijk kan worden vergeten en het moeilijk is om op een geautomatiseerde manier te merken.

Om dat te helpen, ontsnapt de Taal van het Malplaatje van HTML automatisch elke variabele aan de context waarin het wordt geplaatst. Dit is mogelijk dankzij het feit dat HTML de syntaxis van HTML begrijpt.

Veronderstellend volgende `logic.js` dossier:

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

U ziet hoe de twee kenmerken anders zijn beschermd, omdat HTL weet dat de kenmerken `href` en `src` moeten worden beschermd voor de URI-context. Ook, als URI met **`javascript:`** begon, zou het attribuut volledig verwijderd zijn, tenzij de context uitdrukkelijk in iets anders werd veranderd.

Voor meer details over hoe te om het ontsnapen te controleren, verwijs naar de [sectie van de Vertoning van de Taal van de Uitdrukking Context](expression-language.md#display-context).

### Automatische verwijdering van lege kenmerken {#automatic-removal-of-empty-attributes}

Neem bijvoorbeeld het volgende voorbeeld:

```xml
<p class="${properties.class}">some text</p>
```

Als de waarde van het `class` bezit leeg blijkt te zijn, zal de Taal van het Malplaatje van HTML automatisch het volledige `class` attribuut uit de output verwijderen.

Nogmaals, dit is mogelijk, omdat HTML de syntaxis van HTML begrijpt en daarom attributen met dynamische waarden slechts kan voorwaardelijk tonen als hun waarde niet leeg is. Dit is bijzonder handig omdat er geen voorwaardeblok hoeft te worden toegevoegd rondom kenmerken, waardoor de markering ongeldig en onleesbaar zou zijn geworden.

Bovendien is het type van de variabele die in de expressie wordt geplaatst van belang:

* **Tekenreeks:**
   * **not empty:** Stelt de tekenreeks in als kenmerkwaarde.
   * **leeg:** Hiermee verwijdert u het kenmerk helemaal.

* **Number:** Stelt de waarde in als kenmerkwaarde.

* **Boolean:**
   * **true:** geeft het kenmerk zonder waarde weer (als een Booleaans HTML-kenmerk)
   * **false:** Hiermee verwijdert u het kenmerk helemaal.

Hier is een voorbeeld van hoe een uitdrukking Van Boole zou toestaan om een attribuut van HTML van Boole te controleren:

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

Voor het plaatsen van attributen, zou de [`data-sly-attribute`](block-statements.md#attribute) verklaring ook nuttig kunnen zijn.

## Algemene patronen met HTML {#common-patterns-with-htl}

Deze sectie introduceert een paar gemeenschappelijke scenario&#39;s en hoe te om hen met de Taal van het Malplaatje van HTML het best op te lossen.

### Client-bibliotheken {#loading-client-libraries} laden

In HTML worden clientbibliotheken geladen via een hulpsjabloon die wordt geleverd door AEM, die toegankelijk is via [`data-sly-use`](block-statements.md#use). Er zijn drie sjablonen beschikbaar in dit bestand, dat kan worden aangeroepen via [`data-sly-call`](block-statements.md#template-call):

* **`css`** - Laadt alleen de CSS-bestanden van de clientbibliotheken waarnaar wordt verwezen.
* **`js`** - Hiermee worden alleen de JavaScript-bestanden geladen van de clientbibliotheken waarnaar wordt verwezen.
* **`all`** - Laadt alle bestanden van de clientbibliotheken waarnaar wordt verwezen (zowel CSS als JavaScript).

Elke helpermalplaatje verwacht een **`categories`** optie voor het van verwijzingen voorzien van de gewenste cliëntbibliotheken. Deze optie kan ofwel een array van tekenreekswaarden zijn, ofwel een tekenreeks met een lijst met door komma&#39;s gescheiden waarden.

Hier volgen twee korte voorbeelden:

### Meerdere clientbibliotheken tegelijk volledig laden {#loading-multiple-client-libraries-fully-at-once}

```xml
<sly data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html"
     data-sly-call="${clientlib.all @ categories=['myCategory1', 'myCategory2']}"/>
```

### Verwijzen naar een clientbibliotheek in verschillende gedeelten van een pagina {#referencing-a-client-library-in-different-sections-of-a-page}

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

In het tweede bovenstaande voorbeeld, als de HTML **`head`** en **`body`** elementen in verschillende dossiers worden geplaatst, zou **`clientlib.html`** malplaatje dan in elk dossier moeten worden geladen dat het nodig heeft.

De sectie op [malplaatje &amp; vraag](block-statements.md#template-call) verklaringen verstrekt meer details over hoe het verklaren van en het roepen van dergelijke malplaatjes werken.

### Gegevens aan de client doorgeven {#passing-data-to-the-client}

De beste en meest elegante manier om gegevens aan de cliënt in het algemeen, maar nog meer met HTML door te geven, is gegevensattributen te gebruiken.

In het volgende voorbeeld wordt getoond hoe de logica (die ook in Java kan worden geschreven) kan worden gebruikt om het object dat aan de client moet worden doorgegeven, op een handige manier aan JSON te serialiseren. Dit object kan dan heel eenvoudig in een gegevenskenmerk worden geplaatst:

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

Vanaf dat punt is het eenvoudig in te beelden hoe een client-side JavaScript toegang heeft tot dat kenmerk en de JSON opnieuw parseert. Dit zou bijvoorbeeld het overeenkomstige JavaScript zijn dat in een clientbibliotheek wordt geplaatst:

```javascript
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### Werken met clientsjablonen {#working-with-client-side-templates}

Een speciaal geval, waarbij de techniek die in de sectie [Lifting Limitings of Special Contextgevoelige](getting-started.md#lifting-limitations-of-special-contexts) wordt uitgelegd, rechtmatig kan worden gebruikt, is het schrijven van clientsjablonen (zoals bijvoorbeeld Handlebars) die zich binnen **script**-elementen bevinden. De reden dat deze techniek in dat geval veilig kan worden gebruikt, is omdat het element **script** dan JavaScript niet bevat zoals aangenomen, maar meer HTML-elementen. Hier is een voorbeeld van hoe dat zou werken:

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

Zoals hierboven getoond, kan de prijsverhoging die in het **`script`** element zal worden omvat HTTP blokverklaringen bevatten en de uitdrukkingen te hoeven niet om expliciete contexten te verstrekken, omdat de inhoud van het malplaatje Handlebars in zijn eigen dossier geïsoleerd is. In dit voorbeeld wordt ook getoond hoe serverzijde uitgevoerde HTML (zoals op het element **`h2`**) kan worden gemengd met een client-side uitgevoerde sjabloontaal, zoals Handlebars (weergegeven op het element **`h3`**).

Een modernere techniek zou echter het element HTML **`template`** in plaats daarvan gebruiken, aangezien het voordeel dan zou zijn dat het niet vereist om de inhoud van de sjablonen in afzonderlijke bestanden te isoleren.

**Volgende lezen:**

* [Expressietaal](expression-language.md) : voor meer informatie over wat er in HTML-expressies kan worden gedaan.
* [Instructies](block-statements.md)  blokkeren - om alle blokinstructies te ontdekken die beschikbaar zijn in HTML, en hoe te om hen te gebruiken.
