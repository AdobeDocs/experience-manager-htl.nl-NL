---
solution: Experience Manager
type: Documentation
product: adobe experience manager
git-repo: https://github.com/AdobeDocs/experience-manager-htl.nl-NL
index: y
recommendations: noDisplay
source-git-commit: 22f62868df0fcfc558e5d62434dde843a9f3ca83
workflow-type: tm+mt
source-wordcount: '81'
ht-degree: 0%

---


# Metagegevens voor intern gebruik

Het GitHub auteurssysteem bepaalt hiërarchisch meta-gegevens, met stijgende niveaus van precedent zoals die in het volgende worden gezien:

1. metadata.md
1. ToC
1. Artikel

De metagegevens die in het bestand metadata.md zijn gedefinieerd, zijn van toepassing op de volledige repo, maar kunnen worden overschreven op de ToC- en artikelniveaus. Als de metagegevens worden genegeerd, moet dat op het laagst mogelijke niveau gebeuren.

De metagegevens in de repo van `experience-manager-core-components.en` zijn het minimaal vereiste.

metadata.md

* `product`
* `git-repo`
* `index: y`

Niet meer gebruikt:

* `solution-title`
* `solution-hub-url`
* `getting-started-title`
* `getting-started-url`
* `tutorials-title`
* `tutorials-url`

ToCs

* `sub-product`
* `user-guide-title`

Artikel

* `title`
* `description`
* `index: n` (alleen voor vorige versies van componenten)

