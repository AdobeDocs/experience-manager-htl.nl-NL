---
title: Extensies AEM
description: AEM biedt uitbreidingen van de HTML-specificatie aan AEM voor uw gemak als ontwikkelaar.
exl-id: d78cb84d-f958-45e2-9c6c-df86a68277d5
source-git-commit: 88edbd2fd66de960460df5928a3b42846d32066b
workflow-type: tm+mt
source-wordcount: '234'
ht-degree: 0%

---

# Extensies AEM {#aem-extensions}

Gelijkaardig aan [ Apache die uitbreidingen van de specificatie van HTML schikt, ](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#extensions-of-the-htl-specification-1) AEM biedt sommige extra uitdrukkingsopties aan die het werken met AEM concepten een beetje gemakkelijker direct in de manuscripten van HTML maken.

## i18n {#i18n}

Het zelfde [ drie extra opties ](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#i18n) zoals in Apache Sling kan samen met `i18n` worden gebruikt:

* `locale`
* `hint`
* `basename`

Nochtans in AEM, wordt de [ internationaliseringssteun ](https://experienceleague.adobe.com/docs/experience-manager-65/developing/components/internationalization/i18n-dev.html) voor HTML uitgevoerd met de hulp van API van het `com.day.cq.i18n` pakket.

## data-smart-include {#data-sly-include}

In AEM, `data-sly-include` kan een extra `wcmmode` optie nemen die de [ Wijze WCM ](https://developer.adobe.com/experience-manager/reference-materials/cloud-service/javadoc/com/day/cq/wcm/api/WCMMode.html) voor het inbegrepen manuscript controleert. De toegestane waarden zijn de namen van de beschikbare opsommingsconstanten.

## data-smart-resource {#data-sly-resource}

Naast paden en `Resources` kan het element `data-sly-resource` block ook werken met [`Maps` ](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Map.html) of [`Records` .](https://github.com/apache/sling-org-apache-sling-scripting-sightly-runtime/blob/master/src/main/java/org/apache/sling/scripting/sightly/Record.java) Bij beide methoden moet de eigenschap `resourceName` String worden opgegeven. Zijn waarde wordt gebruikt om a [ Synthetisch Middel ](https://www.javadoc.io/doc/org.apache.sling/org.apache.sling.api/latest/org/apache/sling/api/resource/SyntheticResource.html) tot stand te brengen dat in de het teruggeven context zal worden omvat. De overige eigenschappen van de eigenschap `Record` of de eigenschap `Map` die is doorgegeven aan `data-sly-resource` , worden gebruikt als normale `Resource` -eigenschappen. Als het `sling:resourceType` bezit van deze kaart mist, zal het middeltype worden verondersteld om of de waarde van de `resourceType` [ uitdrukkingsoptie ](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#229-resource) of het middeltype van het huidige middel te zijn dat het teruggeven drijft.

Op basis van de volgende eigenschappen voor map/record beschikbaar in het scriptbereik als `map` :

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
