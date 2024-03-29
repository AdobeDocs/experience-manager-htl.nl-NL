---
title: HTML-overzicht
description: Leer hoe AEM HTML sjabloontaal (HTL) ondersteunt om een productief webframework op bedrijfsniveau te bieden dat de beveiliging verhoogt en HTML-ontwikkelaars zonder Java-kennis in staat stelt beter deel te nemen aan AEM projecten.
exl-id: 5d06ff25-d681-4b95-8375-c28a8364eb7e
source-git-commit: 88edbd2fd66de960460df5928a3b42846d32066b
workflow-type: tm+mt
source-wordcount: '711'
ht-degree: 0%

---


# Overzicht {#overview}

Het doel van de Taal van het Malplaatje van HTML (HTML), gesteund door Adobe Experience Manager (AEM), is een hoogst productief ondernemingsniveau Webkader aan te bieden dat veiligheid verhoogt, en HTML ontwikkelaars zonder kennis van Java toestaat om beter aan AEM projecten deel te nemen.

[Inleiding in AEM 6.0,](history.md) De Taal van het Malplaatje van HTML is het aangewezen en geadviseerde server-zijmalplaatjesysteem voor HTML in AEM. Voor webontwikkelaars die robuuste bedrijfswebsites moeten maken, helpt de Sjabloontaal van HTML om meer efficiëntie op het gebied van beveiliging en ontwikkeling te bereiken.

## Meer beveiliging {#increased-security}

De Taal van het Malplaatje van HTML verhoogt de veiligheid van plaatsen die het in hun implementatie gebruiken, in vergelijking met de meeste andere malplaatjesystemen, omdat HTML het juiste context-bewuste ontsnapt automatisch kan toepassen op alle variabelen die output aan de presentatielaag zijn. HTL maakt dit mogelijk omdat het de syntaxis van de HTML begrijpt, en gebruikt die kennis om het vereiste ontsnapt voor uitdrukkingen aan te passen, die op hun positie in de prijsverhoging worden gebaseerd. Dit resulteert bijvoorbeeld in expressies die in `href` of `src` kenmerken die anders moeten worden beschermd dan expressies die in andere kenmerken zijn geplaatst, of elders.

Terwijl het zelfde resultaat met malplaatjetalen zoals JSP kan worden bereikt, moet daar de ontwikkelaar manueel ervoor zorgen dat het juiste escaping op elke variabele wordt toegepast. Aangezien één enkele omissie of fout op toegepaste het ontsnapten potentieel voldoende is om een kwetsbaarheid van de dwars-plaats scripting (XSS) te veroorzaken, besloten wij om deze taak met HTML te automatiseren. Indien nodig, kunnen de ontwikkelaars nog een verschillende ontsnapt aan de uitdrukkingen specificeren, maar met HTML is het standaardgedrag veel waarschijnlijker om aan het gewenste gedrag te beantwoorden, die de waarschijnlijkheid van fouten verminderen.

## Vereenvoudigde ontwikkeling {#simplified-development}

De Taal van het Malplaatje van HTML is gemakkelijk te leren en zijn eigenschappen zijn opzettelijk beperkt om ervoor te zorgen dat het eenvoudig en recht-voorwaarts blijft. Het heeft ook krachtige mechanismen om de prijsverhoging te structureren en logica aan te halen, terwijl het altijd strikte scheiding van zorgen tussen prijsverhoging en logica handhaaft. HTL zelf is standaard HTML5 aangezien het uitdrukkingen en gegevensattributen gebruikt om de prijsverhoging met het gewenste dynamische gedrag aan te merken, betekenend dat het niet de geldigheid van de prijsverhoging breekt en het leesbaar houdt. Merk op dat de evaluatie van de uitdrukkingen en de gegevensattributen volledig server-kant wordt gedaan en niet op cliënt-kant zichtbaar zal zijn, waar om het even welk gewenst kader JavaScript kan worden gebruikt zonder het storen.

Dankzij deze mogelijkheden kunnen HTML-ontwikkelaars zonder Java-kennis en met weinig productspecifieke kennis HTML-sjablonen bewerken, zodat ze deel kunnen uitmaken van het ontwikkelingsteam en de samenwerking met Java-ontwikkelaars in de volledige stapel kunnen stroomlijnen. En andersom kunnen Java-ontwikkelaars zich op de achterste code concentreren zonder zich zorgen te maken over HTML.

## Lagere kosten {#reduced-costs}

De verhoogde veiligheid, vereenvoudigde ontwikkeling, en betere teamsamenwerking, vertaalt voor AEM projecten in verminderde inspanning, snellere tijd aan markt (TTM), en lagere totale kosten van eigendom (TCO).

Concreet, van wat is waargenomen toen het re-uitvoeren van de plaats Adobe.com met de Taal van het Malplaatje van HTML is dat de kosten en de duur van het project met ongeveer 25% konden worden verminderd.

![Efficiënt verhogen en verlagen van kosten](assets/chlimage_1.png)

Bovenstaand diagram laat de volgende verbeteringen zien in de efficiëntie die mogelijk door HTL mogelijk worden gemaakt:

* **HTML / CSS / JS:** Omdat de ontwikkelaars van HTML malplaatjes HTML kunnen direct uitgeven, moeten de front-end ontwerpen niet afzonderlijk van het AEM project meer worden uitgevoerd, maar kunnen direct op de daadwerkelijke AEM componenten worden uitgevoerd. Dit vermindert pijnlijke herhalingen met de full-stack Java-ontwikkelaars.
* **JSP/HTL:** Aangezien HTL zelf geen kennis van Java vereist en door:sturen om te schrijven, wordt om het even welke ontwikkelaar met HTML deskundigheid gemachtigd om de malplaatjes uit te geven.
* **Java:** Dankzij de duidelijke en eenvoudige manier om gebruik-API te gebruiken die door HTML wordt verstrekt, wordt de interface met de bedrijfslogica verduidelijkt, die ook de ontwikkeling van Java over het algemeen bevordert.

## Video-introductie {#video}

De volgende video van een [AEM Gems-sessie,](https://experienceleague.adobe.com/docs/experience-manager-gems-events/gems/gems2014/aem-introduction-to-htl.html) geeft een overzicht van het doel van HTML en voorbeelden van implementatie.

>[!VIDEO](https://video.tv.adobe.com/v/19504/?quality=9)

Let op: de video verwijst naar HTML door [haar vroegere naam, Sright.](history.md)

## Volgende stappen {#next-steps}

Nu kent u de doelstellingen en voordelen van HTML, begin met de taal door het document te herzien [Aan de slag met de Taal van het Malplaatje van HTML.](getting-started.md)
