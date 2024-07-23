---
title: HTML-overzicht
description: Ontdek hoe AEM HTML (de Taal van het Malplaatje van HTML) steunt om een productief ondernemingsniveau Webkader te verstrekken dat veiligheid verbetert. Met dit raamwerk kunnen ontwikkelaars van HTML zonder Java-kennis beter deelnemen aan AEM projecten.
exl-id: 5d06ff25-d681-4b95-8375-c28a8364eb7e
source-git-commit: c6bb6f0954ada866cec574d480b6ea5ac0b51a3f
workflow-type: tm+mt
source-wordcount: '645'
ht-degree: 0%

---


# Overzicht {#overview}

De Taal van het Malplaatje van HTML (HTML), gesteund door Adobe Experience Manager (AEM), streeft naar een hoogst productief ondernemingsniveau Webkader te verstrekken dat veiligheid verbetert. Ook kunnen ontwikkelaars van HTML zonder Java-kennis beter deelnemen aan AEM projecten.

[ die in AEM 6.0 ](history.md) wordt geïntroduceerd, is de Taal van het Malplaatje van de HTML het aangewezen en geadviseerde server-zijmalplaatjesysteem voor HTML in AEM. Voor webontwikkelaars die robuuste bedrijfswebsites moeten maken, helpt de Sjabloontaal van HTML om meer efficiëntie op het gebied van beveiliging en ontwikkeling te bereiken.

## Meer beveiliging {#increased-security}

De Taal van het Malplaatje van de HTML (HTL) verbetert plaatsveiligheid door context-bewuste ontsnapt automatisch toe te passen aan alle outputvariabelen, die het veiliger maken dan de meeste andere malplaatjesystemen. HTL maakt deze benadering mogelijk omdat het de syntaxis van de HTML begrijpt, en die kennis gebruikt om het vereiste ontsnapt voor uitdrukkingen aan te passen, die op hun positie in de prijsverhoging worden gebaseerd. Deze methode kan ertoe leiden dat expressies die in `href` - of `src` -kenmerken zijn geplaatst, anders worden beschermd dan expressies die in andere kenmerken zijn geplaatst, of elders.

Hoewel hetzelfde resultaat kan worden bereikt met sjabloontalen zoals JSP, moet de ontwikkelaar er handmatig voor zorgen dat de juiste escape wordt toegepast op elke variabele. Aangezien één enkele omissie of fout in de toegepaste escape potentieel voldoende is om een XSS-kwetsbaarheid (cross-site scripting) te veroorzaken, besloot de Adobe deze taak te automatiseren met HTML. Indien nodig, kunnen de ontwikkelaars nog een verschillende ontsnapt aan de uitdrukkingen specificeren, maar met HTML is het standaardgedrag veel waarschijnlijker om aan het gewenste gedrag te beantwoorden, die de waarschijnlijkheid van fouten verminderen.

## Vereenvoudigde ontwikkeling {#simplified-development}

De Taal van het Malplaatje van HTML is gemakkelijk te leren en zijn eigenschappen zijn opzettelijk beperkt om ervoor te zorgen dat het eenvoudig en recht-voorwaarts blijft. Het heeft ook krachtige mechanismen om de prijsverhoging te structureren en logica aan te halen, terwijl het altijd strikte scheiding van zorgen tussen prijsverhoging en logica handhaaft. HTML is standaard HTML5, die uitdrukkingen en gegevensattributen gebruikt om de prijsverhoging met dynamisch gedrag te annoteren. Deze benadering handhaaft de geldigheid en de leesbaarheid van de prijsverhoging. De evaluatie van de expressies en gegevenskenmerken vindt volledig op de server plaats en is niet zichtbaar op de client-kant, waar elk gewenst JavaScript-framework kan worden gebruikt zonder dat dit interfereert.

Met deze functies kunnen HTML-ontwikkelaars zonder Java-kennis HTML-sjablonen bewerken, integreren in het ontwikkelingsteam en de samenwerking met Java-ontwikkelaars in volledige stapels stroomlijnen. En omgekeerd, laat het de ontwikkelaars van Java zich op de achterste-eindcode concentreren zonder zich over HTML ongerust te maken.

## Lagere kosten {#reduced-costs}

De verhoogde veiligheid, vereenvoudigde ontwikkeling, en betere teamsamenwerking, vertaalt voor AEM Projecten in verminderde inspanning, snellere tijd aan markt (TTM), en lagere totale kosten van eigendom (TCO).

Het opnieuw uitvoeren van de Adobe.com plaats met de Taal van het Malplaatje van HTML heeft aangetoond dat de projectkosten en de duur tot ongeveer 25% worden verminderd.

![ efficiënt verhoging en kostendaling ](assets/chlimage_1.png)

Bovenstaand diagram laat de volgende verbeteringen zien in de efficiëntie die mogelijk door HTL mogelijk worden gemaakt:

* **HTML / CSS / JS:** de ontwikkelaars van de HTML kunnen de malplaatjes direct uitgeven HTML, toestaand front-end ontwerpen om rechtstreeks op AEM componenten worden uitgevoerd, die de behoefte aan afzonderlijke implementatie elimineren. Deze aanpak vermindert pijnlijke herhalingen met de full-stack Java-ontwikkelaars.
* **JSP/HTL:** omdat HTML zelf geen kennis van Java vereist en door:sturen om te schrijven is, wordt om het even welke ontwikkelaar met de deskundigheid van HTML gemachtigd om de malplaatjes uit te geven.
* **Java:** dankzij duidelijk en eenvoudig om gebruik-API te gebruiken die door HTML wordt verstrekt, wordt de interface met de bedrijfslogica verduidelijkt, die ook de ontwikkeling van Java over het algemeen bevordert.

## Video-introductie {#video}

De volgende video van een [ AEM Gems zitting ](https://experienceleague.adobe.com/en/docs/events/experience-manager-gems-recordings/gems2014/aem-introduction-to-htl), geeft een overzicht van het doel van HTML evenals implementatievoorbeelden.

>[!VIDEO](https://video.tv.adobe.com/v/19504/?quality=9)

Gelieve te merken op dat de video naar HTML door [ zijn vroegere naam, recht ](history.md) verwijst.

## Volgende stappen {#next-steps}

Nu je de doelstellingen en voordelen van HTML kent, kun je aan de slag met de taal. Zie [ Begonnen het Worden met de Taal van het Malplaatje van HTML ](getting-started.md).
