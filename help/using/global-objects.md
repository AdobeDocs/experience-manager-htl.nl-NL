---
title: Globale HTL-objecten
description: Leer meer over opsombare objecten, door Java ondersteunde objecten en door JavaScript ondersteunde objecten in HTML.
exl-id: ca590b92-f1b3-4e44-a04a-a2c10dff256f
source-git-commit: 88edbd2fd66de960460df5928a3b42846d32066b
workflow-type: tm+mt
source-wordcount: '200'
ht-degree: 1%

---


# Globale HTL-objecten {#htl-global-objects}

Als u niets hoeft op te geven, biedt HTML toegang tot veel objecten die nuttig zijn voor de ontwikkelaar. Deze objecten vormen een aanvulling op de objecten die via het [Use-API.](java-use-api.md)

>[!NOTE]
>
>Voor ontwikkelaars die bekend zijn met JSP-ontwikkeling in AEM, biedt HTL toegang tot alle objecten die algemeen beschikbaar waren in JSP nadat `global.jsp`.

## Opsommbare objecten {#enumerable-objects}

Deze objecten bieden handige toegang tot veelgebruikte informatie. De inhoud ervan kan worden geopend met puntnotatie en kan worden doorlopen met behulp van `data-sly-list` of `data-sly-repeat`.

| Naam variabele | Beschrijving | Backed door |
|--- |--- |--- |
| `properties` | Lijst met eigenschappen van de huidige bron | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `pageProperties` | Lijst met pagina-eigenschappen van de huidige pagina | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `inheritedPageProperties` | Lijst met overgenomen pagina-eigenschappen van de huidige pagina | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |

## Door Java ondersteunde objecten {#java-backed-objects}

Elk van de volgende objecten wordt ondersteund door het bijbehorende Java-object.

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

HTML-logica kan met JavaScript worden ondersteund. De aanbevolen of aanbevolen methode is echter: [Sling Models.](https://sling.apache.org/documentation/bundles/models.html)
