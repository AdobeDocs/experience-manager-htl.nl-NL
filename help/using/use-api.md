---
title: HTML Use-API
seo-title: Adobe HTML-API voor gebruik
description: Er zijn twee API's beschikbaar voor HTL, namelijk Java Use-API en Javascript Use-API
seo-description: Er zijn twee API's beschikbaar voor Adobe HTL, namelijk Java Use-API en Javascript Use-API
uuid: ab44aa5c-ce7e-40b9-97fb-e86c6a28405c
contentOwner: User
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: reference
discoiquuid: 89004426-eb59-4b63-913f-51bf98662773
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 5cbaf9c747acf748d12559c2c8e3aba4600cf9a4

---


# HTML Use-API {#htl-use-api}

In de volgende tabel vindt u een overzicht van de voor- en nadelen van elke API.

|  | **Java Use-API** | **JavaScript Use-API** |
|--- |--- |--- |
| **Pros** | <ul><li>sneller</li><li>kan met debugger worden geïnspecteerd</li><li>eenvoudig te testen</li></ul> | <ul><li>kunnen worden gewijzigd door front-end ontwikkelaars</li><li>wordt gevestigd binnen de component, die de meningslogica van een component dichtbij het overeenkomstige malplaatje houden</li></ul> |
| **Cons** | <ul><li>kan niet worden gewijzigd door front-end ontwikkelaars</li></ul> | <ul><li>langzamer</li><li>geen foutopsporing (nog)</li><li>moeilijker per eenheid te testen</li></ul> |


Voor paginacomponenten, wordt het geadviseerd om een gemengd model te gebruiken, waar al modellogica in Java wordt gevestigd, die duidelijke APIs verstrekken die aan om het even wat onbekend zijn die in de mening (d.w.z. binnen de componenten) gebeuren. AEM wordt geleverd met geweldige standaardmodellen zoals de pagina of de bron-API die de meeste gevallen moeten kunnen behandelen.

Alle weergavelogica die specifiek is voor een component, moet in die component worden geplaatst als JavaScript, omdat deze tot die component behoort.
