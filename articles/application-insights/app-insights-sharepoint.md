---
title: "SharePoint-hely megfigyelése az Application Insights segítségével"
description: "Új alkalmazás figyelésének megkezdése új kialakítási kulccsal"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 2bfe5910-d673-4cf6-a5c1-4c115eae1be0
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/24/2016
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: f8c71601d033a2897c83a3a64306a39162360946
ms.contentlocale: hu-hu
ms.lasthandoff: 04/07/2017

---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>SharePoint-hely megfigyelése az Application Insights segítségével
Az Azure Application Insights figyeli alkalmazásai rendelkezésre állását, teljesítményét és használatát. Ebből a cikkből megismerheti, hogyan állíthatja be egy SharePoint-helyhez.

## <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása
Az [Azure Portalon](https://portal.azure.com) hozzon létre egy új Application Insights-erőforrást. Az alkalmazás típusának válassza az ASP.NET lehetőséget.

![Kattintson a Tulajdonságok elemre, válassza ki a kulcsot, és nyomja le a ctrl+C billentyűkombinációt.](./media/app-insights-sharepoint/01-new.png)

A megnyíló panelen megtekintheti az alkalmazása teljesítmény- és használati adatait. Amikor legközelebb bejelentkezik az Azure-ba, a kezdőképernyőn található csempére kattintva léphet közvetlenül erre a panelre. Vagy a Tallózás gombra kattintva is megkeresheti.

## <a name="add-our-script-to-your-web-pages"></a>A szkriptünk hozzáadása weblapokhoz
A Gyors üzembe helyezés területen kérje le a weblapok szkriptjét:

![](./media/app-insights-sharepoint/02-monitor-web-page.png)

Szúrja be a szkriptet minden olyan lap &lt;/head&gt; címkéje elé, amelyet nyomon szeretne követni. Ha a webhelye mesterlappal rendelkezik, ide helyezheti a szkriptet. Egy ASP.NET MVC-projektben a következő helyre helyezné a szkriptet: View\Shared\_Layout.cshtml

A szkript tartalmazza a kialakítási kulcsot, amely a telemetriát az Application Insights-erőforrásra irányítja.

### <a name="add-the-code-to-your-site-pages"></a>Kód hozzáadása webhely lapjaihoz
#### <a name="on-the-master-page"></a>A mesterlapon
Ha szerkeszti webhelye mesterlapját, az biztosítja a webhely összes lapjának figyelését.

Tekintse át a mesterlapot, és szerkessze a SharePoint Designerrel vagy más szerkesztővel.

![](./media/app-insights-sharepoint/03-master.png)

Adja hozzá a kódot közvetlenül a(z) </head> címke elé. 

![](./media/app-insights-sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>Vagy önálló lapokon
Adott számú lap figyeléséhez adja hozzá a szkriptet egyesével a lapokhoz. 

Szúrjon be egy kijelzőt, és ágyazza bele a kódrészletet.

![](./media/app-insights-sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>Alkalmazás adatainak megtekintése
Helyezze ismét üzembe alkalmazását.

Térjen vissza alkalmazása paneljéhez az [Azure Portalon](https://portal.azure.com).

Az első események megjelennek a keresésben. 

![](./media/app-insights-sharepoint/09-search.png)

Ha több adatot vár, néhány másodperc elteltével kattintson a Frissítés elemre.

A felhasználókkal, munkamenetekkel és lapmegtekintésekkel kapcsolatos diagramok megtekintéséhez kattintson a **Használatelemzés** elemre az áttekintési panelen:

![](./media/app-insights-sharepoint/06-usage.png)

Bármely diagramra kattintva további részleteket tekinthet meg, például a Lapmegtekintések diagram esetében:

![](./media/app-insights-sharepoint/07-pages.png)

Vagy a Felhasználók diagram esetében:

![](./media/app-insights-sharepoint/08-users.png)

## <a name="capturing-user-id"></a>Felhasználói azonosító rögzítése
A normál weblapkódrészlet nem rögzíti a felhasználói azonosítót a SharePointból, de ez egy apró módosítással elvégezhető.

1. Az Application Insights Alapvető erőforrások legördülő menüjéből másolja ki alkalmazása kialakítási kulcsát. 

    ![](./media/app-insights-sharepoint/02-props.png)

1. Az alábbi részletben szereplő „XXXX”-et cserélje le a kialakítási kulcsra. 
2. Ágyazza be a szkriptet a SharePoint alkalmazásába annak a kódrészletnek a helyére, amelyet a portálról töltött le.

```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 

<script type="text/javascript"> 
var personProperties; 

// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 

function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 

    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 

    personProperties = peopleManager.getMyProperties(); 

    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 

// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 

// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## <a name="next-steps"></a>Következő lépések
* [Webes tesztek](app-insights-monitor-web-app-availability.md) webhelye rendelkezésre állásának figyeléséhez.
* [Application Insights](app-insights-overview.md) más típusú alkalmazásokhoz.

<!--Link references-->



