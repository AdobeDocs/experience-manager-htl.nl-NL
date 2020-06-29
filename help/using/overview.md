---
title: AEM HTML-overzicht
description: Het doel van HTML dat door AEM wordt gesteund, is een hoogst productief ondernemingsniveau Webkader aan te bieden dat veiligheid verhoogt, en HTML ontwikkelaars zonder kennis van Java toestaat om beter aan AEM projecten deel te nemen.
translation-type: tm+mt
source-git-commit: d3426d87dce09ac34ff1aca431ff2bfad2f7134a
workflow-type: tm+mt
source-wordcount: '662'
ht-degree: 0%

---


# Overzicht {#overview}

Het doel van HTML Template Language (HTL), gesteund door Adobe Experience Manager (AEM), is een hoogst productief web kader op bedrijfsniveau aan te bieden dat veiligheid verhoogt, en HTML-ontwikkelaars zonder kennis van Java toestaat om beter aan AEM-projecten deel te nemen.

De taal van het Malplaatje van HTML werd geïntroduceerd met AEM 6.0, en neemt de plaats van JSP (de Pagina&#39;s van JavaServer) als aangewezen en geadviseerd server-zijmalplaatjesysteem voor HTML. Voor webontwikkelaars die robuuste bedrijfswebsites moeten maken, helpt de HTML-sjabloontaal om de efficiëntie van beveiliging en ontwikkeling te verhogen.

## Meer beveiliging {#increased-security}

De Taal van het Malplaatje van HTML verhoogt de veiligheid van plaatsen die het in hun implementatie gebruiken, in vergelijking met JSP en aan de meeste andere malplaatjesystemen, omdat HTML geschikt is om de juiste context-bewuste ontsnaping automatisch toe te passen op alle variabelen die output aan de presentatielaag zijn. HTML maakt dit mogelijk omdat het de syntaxis van HTML begrijpt, en die kennis gebruikt om het vereiste ontsnapt voor uitdrukkingen aan te passen, die op hun positie in de prijsverhoging worden gebaseerd. Dit leidt er bijvoorbeeld toe dat expressies die in `href` `src` of kenmerken zijn geplaatst anders worden beschermd dan expressies die in andere kenmerken zijn geplaatst, of elders.

Hoewel hetzelfde resultaat kan worden bereikt met sjabloontalen zoals JSP, moet de ontwikkelaar er handmatig voor zorgen dat de juiste escape wordt toegepast op elke variabele. Aangezien één enkele omissie of fout op toegepaste het ontsnapten potentieel voldoende is om een kwetsbaarheid van de dwars-plaats scripting (XSS) te veroorzaken, besloten wij om deze taak met HTML te automatiseren. Indien nodig, kunnen de ontwikkelaars nog een verschillende ontsnapt aan de uitdrukkingen specificeren, maar met HTML is het standaardgedrag veel waarschijnlijker om aan het gewenste gedrag te beantwoorden, die de waarschijnlijkheid van fouten verminderen.

## Vereenvoudigde ontwikkeling {#simplified-development}

De taal van het Malplaatje van HTML is gemakkelijk te leren en zijn eigenschappen zijn opzettelijk beperkt om ervoor te zorgen dat het eenvoudig en recht-voorwaarts blijft. Het heeft ook krachtige mechanismen om de prijsverhoging te structureren en logica aan te halen, terwijl het altijd strikte scheiding van zorgen tussen prijsverhoging en logica handhaaft. HTL zelf is standaard-HTML5 omdat het expressies en gegevenskenmerken gebruikt om de markering aan te brengen met het gewenste dynamische gedrag. Dit houdt in dat de geldigheid van de markering niet wordt verbroken en leesbaar blijft. Merk op dat de evaluatie van de uitdrukkingen en de gegevensattributen volledig server-kant wordt gedaan en niet op cliënt-kant zichtbaar zal zijn, waar om het even welk gewenst kader JavaScript kan worden gebruikt zonder het storen.

Dankzij deze mogelijkheden kunnen HTML-ontwikkelaars zonder Java-kennis en met weinig productspecifieke kennis HTML-sjablonen bewerken, zodat ze deel kunnen uitmaken van het ontwikkelingsteam en de samenwerking met de Java-ontwikkelaars in de volledige stapel kunnen stroomlijnen. En andersom kunnen Java-ontwikkelaars zich op de achterste code concentreren zonder zich zorgen te maken over HTML.

## Lagere kosten {#reduced-costs}

De verhoogde veiligheid, vereenvoudigde ontwikkeling en verbeterde teamsamenwerking, vertaalt voor projecten AEM in verminderde inspanning, snellere tijd aan markt (TTM), en lagere totale kosten van eigendom (TCO).

Uit hetgeen is waargenomen bij het opnieuw implementeren van de Adobe.com-site met de HTML-sjabloontaal blijkt dat de kosten en de duur van het project met ongeveer 25% kunnen worden verminderd.

![Effectieve toename en kostenverlaging](assets/chlimage_1.png)

Bovenstaand diagram laat de volgende verbeteringen zien in de efficiëntie die mogelijk door HTL mogelijk worden gemaakt:

* **HTML / CSS / JS:** Omdat de ontwikkelaars van HTML malplaatjes kunnen direct uitgeven HTML, moeten de front-end ontwerpen niet afzonderlijk van het AEM- project meer worden uitgevoerd, maar kunnen direct op de daadwerkelijke componenten worden uitgevoerd AEM. Dit vermindert pijnlijke herhalingen met de full-stack Java-ontwikkelaars.
* **JSP/HTL:** Aangezien HTML zelf geen kennis van Java vereist en door:sturen om te schrijven, is om het even welke ontwikkelaar met de deskundigheid van HTML gemachtigd om de malplaatjes uit te geven.
* **Java:** Dankzij de duidelijke en eenvoudige manier om gebruik-API te gebruiken die door HTML wordt verstrekt, wordt de interface met de bedrijfslogica verduidelijkt, die ook de ontwikkeling van Java over het algemeen bevordert.

**Volgende lezen:**

* [Aan de slag met de HTML-sjabloontaal](getting-started.md)
