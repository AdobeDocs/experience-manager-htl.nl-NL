---
title: Historie van HTL
description: Voor gebruikers van AEM geeft dit document lange tijd de achtergrond op HTML, hoe JSP wordt vervangen en de naamswijziging van Sightly.
exl-id: 00985b35-2130-4946-959a-0a09a34a0f05
index: false
source-git-commit: 3a416e337337d31f7a7fdb2c78efac46ecf64096
workflow-type: tm+mt
source-wordcount: '530'
ht-degree: 0%

---


# Historie van HTL {#history-of-htl}

Voor gebruikers van AEM geeft dit document lange tijd de achtergrond op HTML, hoe JSP wordt vervangen en de naamswijziging van Sightly.

## Vroeger goed bekend als rechts {#sightly}

HTML Template Language (HTL) is het voorkeurssjabloonsysteem voor servers in HTML in Adobe Experience Manager. Deze pagina komt in de plaats van JSP (JavaServer Pages), zoals in eerdere versies van AEM werd gebruikt.

## HTML boven JSP {#htl-over-jsp}

Adobe raadt u aan om voor nieuwe AEM-projecten de HTML Template Language te gebruiken. De reden is dat het meerdere voordelen biedt in vergelijking met JSP. Voor bestaande projecten heeft een migratie echter alleen zin als deze naar schatting minder moeite kost dan het handhaven van de bestaande JSP&#39;s voor de komende jaren.

Verplaatsen naar HTML is niet noodzakelijkerwijs een all-or-none-keuze, omdat componenten die in HTML zijn geschreven compatibel zijn met componenten die in JSP of ESP zijn geschreven. Deze benadering betekent dat bestaande projecten HTML zonder probleem voor nieuwe componenten kunnen gebruiken, terwijl het handhaven van JSP voor bestaande componenten.

Zelfs binnen dezelfde component, kunnen de HTML- dossiers naast JSPs en ESPs worden gebruikt. Het volgende voorbeeld toont op **lijn 1** hoe te om een Htl- dossier van een JSP dossier, en op **lijn 2** te omvatten hoe een JSP dossier van een Htl- dossier kan worden omvat:

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

## Veelgestelde vragen {#frequently-asked-questions}

Ervaren AEM-ontwikkelaars die nieuw zijn voor HTML stellen vaak de volgende vragen:

### Heeft HTML beperkingen die JSP niet kent? {#limitations}

HTL heeft geen beperkingen ten opzichte van JSP in die zin dat wat met JSP kan worden gedaan ook haalbaar moet zijn met HTL. HTML is echter in verschillende opzichten strenger dan JSP. Wat in één enkel JSP dossier kan worden bereikt zou in een klasse van Java of een dossier van JavaScript kunnen moeten worden gescheiden om in HTML haalbaar te zijn. Maar deze benadering is over het algemeen gewenst om een goede scheiding tussen de logica en de opmaakcode te waarborgen.

### Biedt HTML ondersteuning voor JSP-tagbibliotheken? {#tag-libraries}

Nee. Nochtans, zoals aangetoond in de [ Ladende sectie van de Bibliotheken van de Cliënt ](getting-started.md#loading-client-libraries) van het Begonnen krijgen document, bieden het malplaatje &amp; de vraagverklaringen een gelijkaardig patroon aan.

### Kunnen de HTML-functies worden uitgebreid op een AEM-project? {#extended}

Nee. HTML heeft krachtige uitbreidingsmechanismen voor hergebruik van logica ([ gebruik-API ](#use-api-for-accessing-logic)) en van prijsverhoging (het malplaatje &amp; vraagverklaringen), die kunnen worden gebruikt om de code van projecten te modulariseren.

### Wat zijn de belangrijkste voordelen van HTL ten opzichte van JSP? {#benefits}

De veiligheid en de projectefficiency zijn de belangrijkste voordelen, die in het [ Overzicht ](overview.md) gedetailleerd zijn.

### Gaan JavaServer Pages (JSP) weg? {#go-away}

Nee. Er zijn geen plannen om het JSP te beëindigen.

## Wat staat er in een naam? {#what-is-in-a-name}

In AEM 6.0 en 6.1, werd HTML geroepen **recht**. Adobe hernoemde het aan **Taal van het Malplaatje van HTML** of **HTML** om te verduidelijken wat de specificatie voor is en met Adobe te richten noemend richtlijnen in het algemeen. Deze naamgevingswijziging was van kracht vanaf augustus 2016 en is van toepassing op AEM versie 6.0 en forward.

>[!NOTE]
>
>Deze naamgevingswijziging heeft geen invloed op de code of de API. De compatibiliteit wordt daarom niet beïnvloed.

Om meer over HTML te weten te komen, zie [ Begonnen het Worden met de Gids van de Templating van HTML Templating van de Taal (HTML) ](overview.md).
