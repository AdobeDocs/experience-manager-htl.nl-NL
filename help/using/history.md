---
title: Historie van HTL
description: Voor gebruikers van AEM geeft dit document lange tijd de achtergrond op HTML, hoe het JSP vervangt en de naamswijziging van Rechts.
exl-id: 00985b35-2130-4946-959a-0a09a34a0f05
source-git-commit: 88edbd2fd66de960460df5928a3b42846d32066b
workflow-type: tm+mt
source-wordcount: '544'
ht-degree: 0%

---


# Historie van HTL {#history-of-htl}

Voor gebruikers van AEM geeft dit document lange tijd de achtergrond op HTML, hoe het JSP vervangt en de naamswijziging van Rechts.

## Vroeger goed bekend als rechts {#sightly}

HTML Sjabloontaal (HTL) is het voorkeurssjabloonsysteem en aanbevolen sjabloonsysteem voor HTML in Adobe Experience Manager. Deze pagina komt in de plaats van JSP (JavaServer Pages), zoals in eerdere versies van AEM werd gebruikt.

## HTML boven JSP {#htl-over-jsp}

Het wordt geadviseerd voor nieuwe AEM projecten om de Taal van het Malplaatje van de HTML te gebruiken, aangezien het veelvoudige voordelen in vergelijking met JSP aanbiedt. Voor bestaande projecten heeft een migratie echter alleen zin als deze naar schatting minder moeite kost dan het handhaven van de bestaande JSP&#39;s voor de komende jaren.

Maar het bewegen naar HTML is niet noodzakelijk een alleszins keus, omdat de componenten die in HTML worden geschreven compatibel zijn met componenten die in JSP of ESP worden geschreven. Betekenis dat de bestaande projecten zonder een probleem HTML voor nieuwe componenten kunnen gebruiken, terwijl het houden van JSP voor bestaande componenten.

Zelfs binnen dezelfde component, kunnen de HTML- dossiers naast JSPs en ESPs worden gebruikt. Het volgende voorbeeld toont op **lijn 1** hoe te om een HTML- dossier van een JSP dossier, en op **lijn 2** te omvatten hoe een JSP dossier van een HTML- dossier kan worden omvat:

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

## Veelgestelde vragen {#frequently-asked-questions}

Dit zijn een aantal vragen die ervaren AEM ontwikkelaars die nog niet vertrouwd zijn met HTML vaak stellen.

### Heeft HTML beperkingen die JSP niet kent? {#limitations}

HTL heeft geen beperkingen ten opzichte van JSP in die zin dat wat met JSP kan worden gedaan ook haalbaar zou moeten zijn met HTL. HTML is echter in verschillende opzichten strenger dan JSP. Wat in één enkel JSP dossier kan worden bereikt, zou in een klasse van Java of een dossier van JavaScript kunnen worden gescheiden om in HTML haalbaar te zijn. Maar dit is over het algemeen gewenst om een goede scheiding tussen de logica en de opmaakcode te waarborgen.

### Biedt HTML ondersteuning voor JSP-tagbibliotheken? {#tag-libraries}

Nr, maar zoals aangetoond in de [ Ladende sectie van de Bibliotheken van de Cliënt ](getting-started.md#loading-client-libraries) van het Begonnen krijgen document, bieden het malplaatje &amp; de vraagverklaringen een gelijkaardig patroon aan.

### Kunnen de HTML-functies worden uitgebreid voor een AEM project? {#extended}

Nee, dat kunnen ze niet. HTML heeft krachtige uitbreidingsmechanismen voor hergebruik van logica ([ gebruik-API ](#use-api-for-accessing-logic)) en van prijsverhoging (het malplaatje &amp; vraagverklaringen), die kunnen worden gebruikt om de code van projecten te modulariseren.

### Wat zijn de belangrijkste voordelen van HTL ten opzichte van JSP? {#benefits}

Veiligheid en projectefficiency zijn de belangrijkste voordelen, die op het [ Overzicht gedetailleerd zijn.](overview.md)

### Zal JSP uiteindelijk verdwijnen? {#go-away}

Er zijn geen plannen in die richting.

## Wat staat er in een naam? {#what-is-in-a-name}

In AEM 6.0 en 6.1, werd HTL bedoeld als **recht**. De Adobe hernoemde het aan **Taal van het Malplaatje van de HTML** of **HTML** om te verduidelijken wat de specificatie is en met de noemende richtsnoeren van de Adobe in het algemeen te richten. Deze naamswijziging was van kracht vanaf augustus 2016 en is van toepassing op AEM versie 6.0 en volgende.

>[!NOTE]
>
>Deze naamgevingswijziging is niet van invloed op de code of de API, dus de compatibiliteit wordt niet beïnvloed. Voor meer informatie, gelieve [ naar deze aankondigingsvideo te verwijzen.](https://helpx.adobe.com/experience-manager/how-to/announce-htl.html)

Om meer over HTML en een grote plaats te weten te komen om te beginnen is onze officiële [ Begonnen het worden met de Gids van de Templating van de Templating van de HTML (HTML).](overview.md)
