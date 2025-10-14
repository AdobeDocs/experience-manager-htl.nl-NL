---
title: AEM-extensies
description: AEM biedt AEM uitbreidingen van de HTML-specificatie voor uw gemak als ontwikkelaar.
exl-id: d78cb84d-f958-45e2-9c6c-df86a68277d5
index: false
source-git-commit: a496d23277902a5cd573a6a8af770f27b0269f05
workflow-type: tm+mt
source-wordcount: '228'
ht-degree: 0%

---


# AEM-extensies {#aem-extensions}

Gelijkaardig aan [&#x200B; Apache die uitbreidingen van de specificatie van HTML &#x200B;](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#extensions-of-the-htl-specification-1) schikt, biedt AEM sommige extra uitdrukkingsopties aan die het werken met de concepten van AEM een beetje gemakkelijker direct in de manuscripten van HTML maken.

## i18n {#i18n}

Het zelfde [&#x200B; drie extra opties &#x200B;](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#i18n) zoals in Apache Sling kan samen met `i18n` worden gebruikt:

* `locale`
* `hint`
* `basename`

Nochtans in AEM, wordt de [&#x200B; internationaliseringssteun &#x200B;](https://experienceleague.adobe.com/nl/docs/experience-manager-65/content/implementing/developing/components/internationalization/i18n-dev) voor HTML uitgevoerd met de hulp van API van het `com.day.cq.i18n` pakket.

## `data-sly-include` {#data-sly-include}

In AEM, `data-sly-include` kan een extra `wcmmode` optie nemen die de [&#x200B; Wijze WCM &#x200B;](https://developer.adobe.com/experience-manager/reference-materials/cloud-service/javadoc/com/day/cq/wcm/api/WCMMode.html) voor het inbegrepen manuscript controleren. De toegestane waarden zijn de namen van de beschikbare opsommingsconstanten.

## `data-sly-resource` {#data-sly-resource}

Naast paden en `Resources` kan het `data-sly-resource` block-element ook werken met [`Maps` &#x200B;](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Map.html) of [`Records` &#x200B;](https://github.com/apache/sling-org-apache-sling-scripting-sightly-runtime/blob/master/src/main/java/org/apache/sling/scripting/sightly/Record.java) . Bij beide methoden moet de eigenschap `resourceName` String worden opgegeven. Zijn waarde wordt gebruikt om a [&#x200B; Synthetisch Middel &#x200B;](https://www.javadoc.io/doc/org.apache.sling/org.apache.sling.api/latest/org/apache/sling/api/resource/SyntheticResource.html) tot stand te brengen dat in de het teruggeven context inbegrepen is. De rest van de eigenschappen van de eigenschap `Record` of `Map` die aan `data-sly-resource` is doorgegeven, worden als normale `Resource` -eigenschappen gebruikt. Als het `sling:resourceType` bezit van deze kaart mist, wordt het middeltype verondersteld om of de waarde van de `resourceType` [&#x200B; uitdrukkingsoptie &#x200B;](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#229-resource) of het middeltype van het huidige middel te zijn dat het teruggeven drijft.

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
