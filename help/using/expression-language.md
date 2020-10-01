---
title: HTML-expressietaal
description: De taal van het Malplaatje van HTML gebruikt een uitdrukkingstaal om tot de gegevensstructuren toegang te hebben die de dynamische elementen van de output van HTML verstrekken.
translation-type: tm+mt
source-git-commit: c7fa6014cd954a2ccb175e4c3a6be9deb83af890
workflow-type: tm+mt
source-wordcount: '1854'
ht-degree: 0%

---


# HTML-expressietaal {#htl-expression-language}

De taal van het Malplaatje van HTML gebruikt een uitdrukkingstaal om tot de gegevensstructuren toegang te hebben die de dynamische elementen van de output van HTML verstrekken. Deze expressies worden gescheiden door tekens `${` en `}`. Om misvormde HTML te voorkomen, kunnen expressies alleen worden gebruikt in kenmerkwaarden, in elementinhoud of in opmerkingen.

```xml
<!-- ${component.path} -->
<h1 class="${component.name}">
    ${properties.jcr:title}
</h1>
```

Expressies kunnen worden voorkomen door een `\` teken voor te zetten, bijvoorbeeld `\${test}` wordt het weergegeven `${test}`.

>[!NOTE]
>
>Om de voorbeelden uit te proberen die op deze pagina worden verstrekt, kan een levende uitvoeringsmilieu genoemd de [Gelezen Lijn](https://github.com/Adobe-Marketing-Cloud/aem-sightly-repl) van de Druk worden gebruikt.

De expressiesyntaxis bevat [variabelen](#variables), [literals](#literals), [operatoren](#operators) en [opties](#options):

## Variabelen {#variables}

Variabelen zijn containers waarin gegevenswaarden of objecten worden opgeslagen. De namen van variabelen worden id&#39;s genoemd.

Zonder het moeten om het even wat specificeren, verleent HTML toegang tot alle voorwerpen die algemeen in JSP na het omvatten beschikbaar waren. `global.jsp` De pagina [Algemene objecten](global-objects.md) bevat een lijst met alle objecten waartoe HTML toegang biedt.

### Toegang tot eigenschap {#property-access}

Er zijn twee manieren om toegang te krijgen tot eigenschappen van variabelen, met een puntnotatie of met een haakjesnotatie:

```xml
${currentPage.title}  
${currentPage['title']} or ${currentPage["title"]}
```

In de meeste gevallen verdient de voorkeur de eenvoudige puntnotatie te gebruiken en moet de haakjesnotatie worden gebruikt om toegang te krijgen tot eigenschappen die ongeldige id-tekens bevatten, of om dynamisch toegang te krijgen tot eigenschappen. In de volgende twee hoofdstukken worden nadere bijzonderheden over deze twee gevallen gegeven.

De betreden eigenschappen kunnen functies zijn, nochtans wordt het overgaan van argumenten niet gesteund, zodat slechts de functies die geen argumenten verwachten kunnen worden betreden, zoals getters. Dit is een gewenste beperking, die is bedoeld om de hoeveelheid logica te verminderen ingebed in uitdrukkingen. Indien nodig, kan de [`data-sly-use`](block-statements.md#use) verklaring worden gebruikt om parameters tot de logica over te gaan.

In het bovenstaande voorbeeld wordt ook getoond dat Java getter-functies, zoals `getTitle()`, toegankelijk zijn zonder het voorvoegsel `get`te wijzigen en door het hoofdlettergebruik van het volgende teken te verlagen.

### Geldige id-id-tekens {#valid-identifier-characters}

De namen van variabelen, genoemd herkenningstekens, voldoen aan bepaalde regels. Ze moeten beginnen met een letter (`A`-`Z` en- `a`-`z`) of een onderstrepingsteken (`_`) en volgende tekens kunnen ook cijfers (`0`-`9`) of dubbele punten (`:`) zijn. Unicode-letters zoals `å` `ü` en kunnen niet worden gebruikt in id&#39;s.

Aangezien de dubbele punt (`:`) karakter in AEM bezitsnamen gemeenschappelijk is, zou moeten worden benadrukt dat het een geldig herkenningsteken gemakkelijk is:

`${properties.jcr:title}`

De haakjesnotatie kan worden gebruikt om toegang te krijgen tot eigenschappen die ongeldige id-tekens bevatten, zoals het spatieteken in het onderstaande voorbeeld:

`${properties['my property']}`

### Leden dynamisch openen {#accessing-members-dynamically}

```xml
${properties[myVar]}
```

### Toestemming voor verwerking van null-waarden {#permissive-handling-of-null-values}

```xml
${currentPage.lastModified.time.toString}
```

## Literals {#literals}

Een letterlijke waarde is een notatie voor het weergeven van een vaste waarde.

### Boolean {#boolean}

Boolean vertegenwoordigt een logische entiteit en kan twee waarden hebben: `true`en `false`.

`${true} ${false}`

### Getallen {#numbers}

Er is slechts één getaltype: positieve gehele getallen. Andere getalnotaties, zoals zwevende komma, worden ondersteund in variabelen, maar kunnen niet worden uitgedrukt als letterlijke tekens.

`${42}`

### Tekenreeksen {#strings}

Tekenreeksen vertegenwoordigen tekstuele gegevens en kunnen enkelvoudig of dubbelgenoteerd zijn:

`${'foo'} ${"bar"}`

Naast gewone tekens kunnen de volgende speciale tekens worden gebruikt:

* `\\` Backslash-teken
* `\'` Enkel aanhalingsteken (of apostrof)
* `\"` Dubbel aanhalingsteken
* `\t` Tab
* `\n` Nieuwe regel
* `\r` Enter
* `\f` Formulierfeed
* `\b` Backspace
* `\uXXXX` Het Unicode-teken dat wordt opgegeven door de vier hexadecimale cijfers XXXX.\
   Enkele nuttige unicode-escapereeksen zijn:

   * `\u0022` for `"`
   * `\u0027` for `'`

Voor tekens die hierboven niet worden vermeld, wordt een fout weergegeven vóór een backslash-teken.

Hier volgen enkele voorbeelden van het gebruik van escape-tekenreeksen:

```xml
<p>${'it\'s great, she said "yes!"'}</p>
<p title="${'it\'s great, she said \u0022yes!\u0022'}">...</p>
```

Dit resulteert in de volgende uitvoer, omdat HTL contextspecifieke escape toepast:

```xml
<p>it&#39;s great, she said &#34;yes!&#34;</p>
<p title="it&#39;s great, she said &#34;yes!&#34;">...</p>
```

### Arrays {#arrays}

Een array is een geordende set waarden waarnaar met een naam en index kan worden verwezen. De typen elementen kunnen worden gemengd.

```xml
${[1,2,3,4]}
${myArray[2]}
```

Arrays zijn handig voor het weergeven van een lijst met waarden uit de sjabloon.

```xml
<ul data-sly-list="${[1,2,3,4]}">
  <li>${item}</li>
</ul>
```

## Operatoren {#operators}

### Logische operatoren {#logical-operators}

Deze operatoren worden doorgaans gebruikt met Booleaanse waarden, maar in JavaScript retourneren ze in feite de waarde van een van de opgegeven operanden. Wanneer ze dus worden gebruikt met niet-Booleaanse waarden, kunnen ze een niet-Booleaanse waarde retourneren.

Wanneer een waarde in kan worden omgezet, wordt de waarde &#39;true&#39; genoemd. `true` Wanneer een waarde in kan worden omgezet, wordt `false`de waarde ook wel false genoemd. Waarden die kunnen worden omgezet in `false` zijn ongedefinieerde variabelen, null-waarden, het getal nul en lege tekenreeksen.

#### Logische NOT {#logical-not}

`${!myVar}` retourneert `false` als de enkele operand kan worden omgezet in `true`; anders komt het erop neer `true`.

Dit kan bijvoorbeeld worden gebruikt om een testvoorwaarde om te keren, zoals het tonen van een element slechts als er geen kindpagina&#39;s zijn:

```xml
<p data-sly-test="${!currentPage.hasChild}">current page has no children</p>
```

#### Logisch AND {#logical-and}

`${varOne && varTwo}` retourneert `varOne` indien deze fout is; anders komt het erop neer `varTwo`.

Deze operator kan worden gebruikt om twee voorwaarden tegelijk te testen, zoals het controleren van het bestaan van twee eigenschappen:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

De logische operator AND kan ook worden gebruikt om HTML-kenmerken voorwaardelijk weer te geven, omdat HTML kenmerken verwijdert met waarden die dynamisch zijn ingesteld op false of op een lege tekenreeks. In het onderstaande voorbeeld wordt het `class` kenmerk alleen weergegeven als `logic.showClass` true is, als `logic.className` bestaat en niet leeg is:

```xml
<div class="${logic.showClass && logic.className}">...</div>
```

#### Logisch OR {#logical-or}

`${varOne || varTwo}` retourneert `varOne` indien deze waarheidsgetrouw is; anders komt het erop neer `varTwo`.

Deze operator kan worden gebruikt om te testen of een van de twee voorwaarden van toepassing is, zoals het controleren van het bestaan van ten minste één eigenschap:

```xml
<div data-sly-test="${properties.jcr:title || properties.jcr:description}">...</div>
```

Aangezien de logische exploitant OR de eerste variabele terugkeert die waarachtig is, kan het ook zeer geschikt worden gebruikt om reservewaarden te verstrekken.

Het kan ook worden gebruikt om HTML-kenmerken voorwaardelijk weer te geven, omdat HTML kenmerken verwijdert met waarden die zijn ingesteld door expressies die onwaar of leeg zijn. In het onderstaande voorbeeld wordt de **`properties.jcr:`** titel weergegeven als deze bestaat en niet leeg is. Als deze bestaat en niet leeg is, wordt de titel **`properties.jcr:description`** anders weergegeven als er geen titel of beschrijving is opgegeven:

```xml
<p>${properties.jcr:title || properties.jcr:description || "no title or description provided"}</p>
```

### Voorwaardelijke (ternaire) operator {#conditional-ternary-operator}

`${varCondition ? varOne : varTwo}` retourneert `varOne` indien `varCondition` waarheidsgetrouw is; anders wordt het geretourneerd `varTwo`.

Deze operator kan doorgaans worden gebruikt om voorwaarden binnen expressies te definiëren, zoals een ander bericht weergeven op basis van de status van de pagina:

```xml
<p>${currentPage.isLocked ? "page is locked" : "page can be edited"}</p>
```

>[!TIP]
>
>Aangezien dubbele tekens ook zijn toegestaan in id&#39;s, is het beter om ternaire operatoren te scheiden met een spatie om de parser helderheid te geven:

```xml
<p>${properties.showDescription ? properties.jcr:description : properties.jcr:title}</p>
```

### Vergelijkingsoperatoren {#comparison-operators}

De gelijkheids- en ongelijkheidsoperatoren ondersteunen alleen operands van identieke typen. Wanneer de typen niet overeenkomen, wordt een fout weergegeven.

* Tekenreeksen zijn gelijk wanneer ze dezelfde reeks tekens hebben.
* Getallen zijn gelijk wanneer ze dezelfde waarde hebben
* Booleaanse waarden zijn gelijk als beide `true` of beide gelijk zijn `false`.
* Null- of ongedefinieerde variabelen zijn gelijk aan zichzelf en aan elkaar.

`${varOne == varTwo}` retourneert `true` als `varOne` en `varTwo` gelijk zijn.

`${varOne != varTwo}` retourneert `true` als `varOne` en `varTwo` niet gelijk zijn.

Relationele operatoren ondersteunen alleen operanden die getallen zijn. Voor alle andere typen wordt een fout weergegeven.

`${varOne > varTwo}` retourneert `true` als `varOne` groter is dan `varTwo`.

`${varOne < varTwo}` retourneert `true` als de waarde kleiner `varOne` is dan `varTwo`.

`${varOne >= varTwo}` retourneert `true` als `varOne` groter of gelijk is aan `varTwo`.

`${varOne <= varTwo}` retourneert `true` als de waarde kleiner of gelijk `varOne` is aan `varTwo`.

### Haakjes groeperen {#grouping-parentheses}

De groeperingsoperator `()` bepaalt de prioriteit van de evaluatie in expressies.

`${varOne && (varTwo || varThree)}`

## Opties {#options}

De opties van de uitdrukking kunnen op de uitdrukking handelen en het wijzigen, of als parameters wanneer gebruikt in combinatie met blokverklaringen.

Alles na de gebeurtenis `@` is een optie:

```xml
${myVar @ optOne}
```

Opties kunnen een waarde hebben, die een variabele of een letterlijke waarde kan zijn:

```xml
${myVar @ optOne=someVar}
${myVar @ optOne='bar'}
${myVar @ optOne=10}
${myVar @ optOne=true}
```

Meerdere opties worden gescheiden door komma&#39;s:

```xml
${myVar @ optOne, optTwo=bar}
```

Parametrische expressies met alleen opties zijn ook mogelijk:

```xml
${@ optOne, optTwo=bar}
```

### Tekenreeksindeling {#string-formatting}

Optie die de opgesomde plaatsaanduidingen, {*n*}, vervangt door de corresponderende variabele:

```xml
${'Page {0} of {1}' @ format=[current, total]}
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

Het `@extension` werkt in alle scenario&#39;s, die of controleren om de uitbreiding toe te voegen of niet.

```xml
${ link @ extension = 'html' }
```

### Getal/datum-opmaak {#number-date-formatting}

HTML staat native opmaak van getallen en datums toe zonder aangepaste code te schrijven. Dit ondersteunt ook tijdzone en landinstelling.

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

### Internationalisatie {#internationalization}

Zet de tekenreeks om in de taal van de huidige *bron* (zie hieronder) met behulp van het huidige [woordenboek](https://docs.adobe.com/content/help/en/experience-manager-65/developing/components/internationalization/i18n-translator.html). Als er geen vertaling wordt gevonden, wordt de oorspronkelijke tekenreeks gebruikt.

```xml
${'Page' @ i18n}
```

U kunt de optie Tip gebruiken om een opmerking te maken voor vertalers en de context te bepalen waarin de tekst wordt gebruikt:

```xml
${'Page' @ i18n, hint='Translation Hint'}
```

De standaardbron voor de taal is `resource`, wat betekent dat de tekst wordt vertaald naar dezelfde taal als de inhoud. Dit kan worden veranderd in `user`, betekenend dat de taal van browser scène of van de scène van de het programma geopende gebruiker wordt genomen:

```xml
${'Page' @ i18n, source='user'}
```

Als u een expliciete landinstelling opgeeft, worden de broninstellingen genegeerd:

```xml
${'Page' @ i18n, locale='en-US'}
```

Als u variabelen in een vertaalde tekenreeks wilt insluiten, kunt u de indelingsoptie gebruiken:

```xml
${'Page {0} of {1}' @ i18n, format=[current, total]}
```

### Array samenvoegen {#array-join}

Bij het weergeven van een array als tekst geeft HTL standaard door komma&#39;s gescheiden waarden weer (zonder spatiëring).

Gebruik de verbindingsoptie om een ander scheidingsteken op te geven:

```xml
${['one', 'two'] @ join='; '}
```

### Weergavecontext {#display-context}

De weergavecontext van een HTML-expressie verwijst naar de locatie ervan binnen de structuur van de HTML-pagina. Bijvoorbeeld, als de uitdrukking op zijn plaats verschijnt die een tekstknoop zou produceren zodra teruggegeven, dan wordt het gezegd in een `text` context. Als het binnen de waarde van een attribuut wordt gevonden, dan wordt gezegd om in een `attribute` context, etc. te zijn.

Met uitzondering van script- (JS) en stijl- (CSS) contexten, detecteert HTL automatisch de context van expressies en ontspant deze op de juiste wijze om XSS-beveiligingsproblemen te voorkomen. In het geval van scripts en CSS moet het gewenste contextgedrag expliciet worden ingesteld. Bovendien kan het contextgedrag ook expliciet worden ingesteld in elk ander geval waarin een overschrijving van het automatische gedrag gewenst is.

Hier hebben we drie variabelen in drie verschillende contexten:

* `properties.link` ( `uri` context)
* `properties.title` (`attribute` context)
* `properties.text` (`text` context)

HTL zal elk van hen verschillend ontkomen in overeenstemming met de veiligheidsvereisten van hun respectieve contexten. In normale gevallen zoals deze is geen expliciete context-instelling vereist:

```xml
<a href="${properties.link}" title="${properties.title}">${properties.text}</a>
```

Voor een veilige uitvoermarkering (dat wil zeggen, waar de expressie zelf naar HTML evalueert) wordt de `html` context gebruikt:

```xml
<div>${properties.richText @ context='html'}</div>
```

Expliciete context moet worden ingesteld voor stijlcontexten:

```xml
<span style="color: ${properties.color @ context='styleToken'};">...</span>
```

Expliciete context moet worden ingesteld voor scriptcontexten:

```xml
<span onclick="${properties.function @ context='scriptToken'}();">...</span>
```

De optie Escaping en XSS-beveiliging kunnen ook worden uitgeschakeld:

```xml
<div>${myScript @ context='unsafe'}</div>
```

### Context-instellingen {#context-settings}

| Context | Wanneer gebruiken | Wat het doet |
|--- |--- |--- |
| `text` | Standaard voor inhoud binnen elementen | Codeert alle speciale HTML-tekens. |
| `html` | Uitvoer veilig markeren | Filtert HTML om aan de antiSamy beleidsregels te voldoen, verwijderend wat niet de regels aanpast. |
| `attribute` | Standaard voor kenmerkwaarden | Codeert alle speciale HTML-tekens. |
| `uri` | Koppelingen en paden weergeven Standaard voor href- en src-kenmerkwaarden | Valideert URI voor schrijven als href- of src-kenmerkwaarde en voert niets uit als validatie mislukt. |
| `number` | Getallen weergeven | Valideert URI voor het bevatten van een geheel getal, geeft nul als de validatie mislukt. |
| `attributeName` | Standaard voor data-SLUIT-kenmerk bij het instellen van kenmerknamen | Valideert de kenmerknaam, geeft niets als de validatie mislukt. |
| `elementName` | Standaard voor element data-smart | Valideert de elementnaam, geeft niets als de validatie mislukt. |
| `scriptToken` | Voor JS-id&#39;s, letterlijke getallen of letterlijke tekenreeksen | Hiermee wordt het JavaScript-token gevalideerd. Er wordt niets uitgevoerd als de validatie mislukt. |
| `scriptString` | Binnen JS-tekenreeksen | Codeert tekens die uit de tekenreeks zouden verdwijnen. |
| `scriptComment` | Binnen JS-opmerkingen | Valideert de JavaScript-opmerking en voert niets uit als de validatie mislukt. |
| `styleToken` | Voor CSS-id&#39;s, getallen, afmetingen, tekenreeksen, hexadecimale kleuren of functies. | Hiermee valideert u het CSS-token en geeft u niets als de validatie mislukt. |
| `styleString` | Binnen CSS-tekenreeksen | Codeert tekens die uit de tekenreeks zouden verdwijnen. |
| `styleComment` | Binnen CSS-opmerkingen | Hiermee valideert u de CSS-opmerking. Er wordt niets uitgevoerd als de validatie mislukt. |
| `unsafe` | Alleen als geen van de bovenstaande functies wordt uitgevoerd | Hiermee schakelt u de escaping en XSS-beveiliging volledig uit. |
