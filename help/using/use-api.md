---
title: HTML Use-API
description: Er zijn twee API's beschikbaar voor HTL, namelijk Java Use-API en Javascript Use-API
translation-type: tm+mt
source-git-commit: d7efae3d1b4d1bc22c63c21f544a99bf0ae4b3c9
workflow-type: tm+mt
source-wordcount: '183'
ht-degree: 6%

---


# HTML Use-API {#htl-use-api}

HTL moedigt de scheiding van zorgen aan door bedrijfslogica niet toe te staan om met prijsverhoging te mengen. De bedrijfslogica kan door gebruik-API worden uitgevoerd.

In de volgende tabel vindt u een overzicht van de voor- en nadelen van elke API.

|  | **Java Use-API** | **JavaScript Use-API** |
|--- |--- |--- |
| **Voordelen** | <ul><li>Sneller</li><li>Kan worden geïnspecteerd met een foutopsporingsprogramma</li><li>Eenvoudige test</li></ul> | <ul><li>Kan worden gewijzigd door front-end ontwikkelaars</li><li>Is gevestigd binnen de component, die de meningslogica van een component dicht bij zijn overeenkomstige malplaatje houdt</li></ul> |
| **Nadelen** | <ul><li>Kan niet worden gewijzigd door front-end ontwikkelaars</li></ul> | <ul><li>Langzamer</li><li>Geen foutopsporing (nog)</li><li>Harder naar eenheid</li></ul> |

Voor paginacomponenten, wordt het geadviseerd om een gemengd model te gebruiken, waar al modellogica in Java wordt gevestigd, die duidelijke APIs verstrekken die aan om het even wat onbekend zijn die in de mening (d.w.z. binnen de componenten) gebeuren. AEM wordt geleverd met geweldige standaardmodellen zoals de pagina of de bron-API die de meeste gevallen moeten kunnen behandelen.

Alle weergavelogica die specifiek is voor een component, moet in die component worden geplaatst als JavaScript, omdat deze tot die component behoort.
