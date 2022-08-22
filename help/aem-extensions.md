---
title: Extensies AEM
description: AEM biedt uitbreidingen van de HTML-specificatie aan AEM voor uw gemak als ontwikkelaar.
source-git-commit: 6d97bc5d0ab89dffaf56a54c73c94d069bb31ca6
workflow-type: tm+mt
source-wordcount: '308'
ht-degree: 0%

---


# Extensies AEM {#aem-extensions}

Vergelijkbaar met de [Apache Sling-uitbreidingen van de HTL-specificatie,](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#extensions-of-the-htl-specification-1) AEM biedt enkele aanvullende expressieopties die het werken met AEM concepten iets makkelijker rechtstreeks in de HTML-scripts maken.

## i18n {#i18n}

Hetzelfde [drie extra opties](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#i18n) zoals in Apache Sling kan worden gebruikt in combinatie met `i18n`:

* `locale`
* `hint`
* `basename`

In AEM [internationalisatie-ondersteuning](https://experienceleague.adobe.com/docs/experience-manager-65/developing/components/internationalization/i18n-dev.html) voor HTML wordt geïmplementeerd met behulp van de API van de `com.day.cq.i18n` pakket.

## data-smart-include {#data-sly-include}

In AEM `data-sly-include` kan extra `wcmmode` optie die de [WCM-modus](https://developer.adobe.com/experience-manager/reference-materials/cloud-service/javadoc/com/day/cq/wcm/api/WCMMode.html) voor het opgenomen script. De toegestane waarden zijn de namen van de beschikbare opsommingsconstanten.

## data-smart-resource {#data-sly-resource}

Naast paden en `Resources`de `data-sly-resource` blokelement kan ook werken met [`Maps`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Map.html) of [`Records`.](https://github.com/apache/sling-org-apache-sling-scripting-sightly-runtime/blob/master/src/main/java/org/apache/sling/scripting/sightly/Record.java) Met beide benaderingen `resourceName` Tekenreekseigenschap moet worden opgegeven. De waarde ervan wordt gebruikt om een [Synthetische bron](https://www.javadoc.io/doc/org.apache.sling/org.apache.sling.api/latest/org/apache/sling/api/resource/SyntheticResource.html) die worden opgenomen in de renderingcontext. De rest van de eigenschappen van de `Record` of de `Map` die is doorgegeven aan `data-sly-resource` wordt gebruikt als normaal `Resource` eigenschappen. Als de `sling:resourceType` eigenschap ontbreekt in deze kaart. Het middeltype wordt aangenomen dat dit de waarde van de eigenschap `resourceType` [expressie, optie](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#229-resource) of het resourcetype van de huidige resource die de rendering aanstuurt.

Op basis van de volgende map-/recordeigenschappen die in het scriptbereik beschikbaar zijn als `map`:

```javascript
{
    resourceName: "myText",
    "sling:resourceType": "core/wcm/components/text/v2/text",
    "text": "Hello World!"
}
```

En gegeven de volgende prijsverhoging:

```html
<div class="outer" data-sly-resource="${map}"></div>
```

De volgende uitvoer wordt verwacht:

```html
<div class="outer">
    <div class="myText">
        <div data-cmp-data-layer="{&quot;text-e58d65c472&quot;:{&quot;@type&quot;:&quot;core/wcm/components/text/v2/text&quot;,&quot;xdm:text&quot;:&quot;<p>Hello world!</p>&quot;}}" id="text-e58d65c472" class="cmp-text">
            <p>Hello world!</p>
        </div>
  </div>
</div>
```
