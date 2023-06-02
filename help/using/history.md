---
title: Geschiedenis van HTL
description: Voor gebruikers van AEM geeft dit document lange tijd de achtergrond op HTML, hoe het JSP vervangt en de naamswijziging van Rechts.
exl-id: 00985b35-2130-4946-959a-0a09a34a0f05
source-git-commit: 88edbd2fd66de960460df5928a3b42846d32066b
workflow-type: tm+mt
source-wordcount: '542'
ht-degree: 0%

---


# Geschiedenis van HTL {#history-of-htl}

Voor gebruikers van AEM geeft dit document lange tijd de achtergrond op HTML, hoe het JSP vervangt en de naamswijziging van Rechts.

## Vroeger goed bekend als rechts {#sightly}

HTML Sjabloontaal (HTL) is het voorkeurssjabloonsysteem en het aanbevolen sjabloonsysteem voor HTML in Adobe Experience Manager. Deze pagina komt in de plaats van JSP (JavaServer Pages), zoals in eerdere versies van AEM werd gebruikt.

## HTML boven JSP {#htl-over-jsp}

Het wordt geadviseerd voor nieuwe AEM projecten om de Taal van het Malplaatje van de HTML te gebruiken, aangezien het veelvoudige voordelen in vergelijking met JSP aanbiedt. Voor bestaande projecten heeft een migratie echter alleen zin als deze naar schatting minder moeite kost dan het handhaven van de bestaande JSP&#39;s voor de komende jaren.

Maar het bewegen naar HTML is niet noodzakelijk een alleszins keus, omdat de componenten die in HTML worden geschreven compatibel zijn met componenten die in JSP of ESP worden geschreven. Betekenis dat de bestaande projecten zonder een probleem HTML voor nieuwe componenten kunnen gebruiken, terwijl het houden van JSP voor bestaande componenten.

Zelfs binnen dezelfde component, kunnen de HTML- dossiers naast JSPs en ESPs worden gebruikt. In het volgende voorbeeld wordt getoond: **regel 1** hoe u een HTML-bestand uit een JSP-bestand opneemt, en **lijn 2** hoe een JSP-bestand kan worden opgenomen vanuit een HTML-bestand:

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

## Veelgestelde vragen {#frequently-asked-questions}

Dit zijn een aantal vragen die ervaren AEM ontwikkelaars die nog niet vertrouwd zijn met HTML vaak stellen.

### Heeft HTML beperkingen die JSP niet kent? {#limitations}

HTL heeft geen beperkingen ten opzichte van JSP in die zin dat wat met JSP kan worden gedaan ook haalbaar zou moeten zijn met HTL. HTML is echter in verschillende opzichten strenger dan JSP. Wat in één enkel JSP dossier kan worden bereikt, zou in een klasse van Java of een dossier van JavaScript kunnen moeten worden gescheiden om in HTML haalbaar te zijn. Maar dit is over het algemeen gewenst om een goede scheiding tussen de logica en de opmaakcode te waarborgen.

### Biedt HTML ondersteuning voor JSP-tagbibliotheken? {#tag-libraries}

Nee, maar zoals in het dialoogvenster [Client-bibliotheken laden](getting-started.md#loading-client-libraries) in het document Aan de slag bieden de sjabloon- en callinstructies een vergelijkbaar patroon.

### Kunnen de HTML-functies worden uitgebreid voor een AEM project? {#extended}

Nee, dat kunnen ze niet. HTML beschikt over krachtige uitbreidingsmechanismen voor hergebruik van logica (de [Use-API](#use-api-for-accessing-logic)) en van opmaakcodes (de sjabloon- en callinstructies), die kunnen worden gebruikt om de projectcode te moduleren.

### Wat zijn de belangrijkste voordelen van HTL ten opzichte van JSP? {#benefits}

De veiligheid en de projectefficiency zijn de belangrijkste voordelen die op [Overzicht.](overview.md)

### Zal JSP uiteindelijk verdwijnen? {#go-away}

Er zijn geen plannen in die richting.

## Wat staat er in een naam? {#what-is-in-a-name}

In AEM 6.0 en 6.1 werd HTL **recht**. Adobe heeft de naam gewijzigd in **HTML Sjabloontaal** of **HTL** om te verduidelijken wat het productdossier dient en om zich aan te passen aan de naamgevingsrichtlijnen van Adobe in het algemeen. Deze naamswijziging was van kracht vanaf augustus 2016 en is van toepassing op AEM versie 6.0 en volgende.

>[!NOTE]
>
>Deze naamgevingswijziging is niet van invloed op de code of de API, dus de compatibiliteit wordt niet beïnvloed. Voor meer informatie, gelieve [raadpleeg deze video over aankondiging.](https://helpx.adobe.com/experience-manager/how-to/announce-htl.html)

Meer weten over HTML en een geweldige plek om te beginnen is onze ambtenaar [Aan de slag met de handleiding HTML Templating Language (HTL).](overview.md)
