---
title: Globale HTL-objecten
description: Meer informatie over opsombare objecten en objecten die door Java worden ondersteund, vindt u in HTML.
exl-id: ca590b92-f1b3-4e44-a04a-a2c10dff256f
source-git-commit: addc69e4b4e56a9b1c5f91ce9af26fa2d326d981
workflow-type: tm+mt
source-wordcount: '203'
ht-degree: 0%

---


# Globale HTL-objecten {#htl-global-objects}

Als u niets hoeft op te geven, biedt HTML toegang tot veel objecten die nuttig zijn voor de ontwikkelaar. Deze voorwerpen zijn naast om het even welk die door [ gebruik-API ](java-use-api.md) kunnen worden geïntroduceerd.

>[!NOTE]
>
>Voor ontwikkelaars die bekend zijn met JSP-ontwikkeling in AEM, biedt HTL toegang tot alle objecten die algemeen beschikbaar waren in JSP nadat `global.jsp` is opgenomen.

## Opsommbare objecten {#enumerable-objects}

Deze objecten bieden handige toegang tot veelgebruikte informatie. De inhoud ervan kan worden benaderd met puntnotatie en kan worden doorlopen met `data-sly-list` of `data-sly-repeat` .

| Naam variabele | Beschrijving | Backed door |
|--- |--- |--- |
| `properties` | Lijst met eigenschappen van de huidige bron | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `pageProperties` | Lijst met pagina-eigenschappen van de huidige pagina | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `inheritedPageProperties` | Lijst met overgenomen pagina-eigenschappen van de huidige pagina | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |

## Door Java ondersteunde objecten {#java-backed-objects}

Het corresponderende Java-object ondersteunt elk van de volgende objecten.

| Naam variabele | Beschrijving |
|---|---|
| `component` | `com.day.cq.wcm.api.components.Component` |
| `componentContext` | `com.day.cq.wcm.api.components.ComponentContext` |
| `currentContentPolicy` | `com.day.cq.wcm.api.policies.ContentPolicy` |
| `currentContentPolicyProperties` | `com.day.cq.wcm.api.policies.ContentPolicy` |
| `currentDesign` | `com.day.cq.wcm.api.designer.Design` |
| `currentNode` | `javax.jcr.Node` |
| `currentPage` | `com.day.cq.wcm.api.Page` |
| `currentSession` | `javax.servlet.http.HttpSession` |
| `currentStyle` | `com.day.cq.wcm.api.designer.Style` |
| `designer` | `com.day.cq.wcm.api.designer.Designer` |
| `editContext` | `com.day.cq.wcm.api.components.EditContext` |
| `log` | `org.slf4j.Logger` |
| `out` | `java.io.PrintWriter` |
| `pageManager` | `com.day.cq.wcm.api.PageManager` |
| `reader` | `java.io.BufferedReader` |
| `request` | `org.apache.sling.api.SlingHttpServletRequest` |
| `resolver` | `org.apache.sling.api.resource.ResourceResolver` |
| `resource` | `org.apache.sling.api.resource.Resource` |
| `resourceDesign` | `com.day.cq.wcm.api.designer.Design` |
| `resourcePage` | `com.day.cq.wcm.api.Page` |
| `response` | `org.apache.sling.api.SlingHttpServletResponse` |
| `sling` | `org.apache.sling.api.scripting.SlingScriptHelper` |
| `slyWcmHelper` | `com.adobe.cq.sightly.WCMScriptHelper` |
| `wcmmode` | `com.adobe.cq.sightly.SightlyWCMMode` |
| `xssAPI` | `com.adobe.granite.xss.XSSAPI` |

## Door JavaScript ondersteunde objecten {#javascript-backed-objects}

Het is mogelijk om HTML-logica te ondersteunen met JavaScript. Nochtans, is de aangewezen of geadviseerde methode door [ het Schuiven Modellen ](https://sling.apache.org/documentation/bundles/models.html) te gebruiken.

>[!NOTE]
>
>[ het Gebruik API van JavaScript ](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#42-javascript-use-api) is nu afgekeurd voor gebruik met AEM as a Cloud Service. In plaats daarvan, gebruik [ het Gebruik API van Java ](https://experienceleague.adobe.com/nl/docs/experience-manager-htl/content/java-use-api).
>
>Voor meer informatie over afgekeurde en verwijderde eigenschappen, zie de [ de versienota&#39;s van AEM as a Cloud Service ](https://experienceleague.adobe.com/nl/docs/experience-manager-cloud-service/content/release-notes/deprecated-removed-features).
