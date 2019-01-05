---
title: SharePoint-hely megfigyelése az Application Insights segítségével
description: Új alkalmazás figyelésének megkezdése új kialakítási kulccsal
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 2bfe5910-d673-4cf6-a5c1-4c115eae1be0
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: mbullwin
ms.openlocfilehash: 5c050ce33e9e5b4df7d5bdd50ca1bfd6a775b4a8
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54045907"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>SharePoint-hely megfigyelése az Application Insights segítségével
Az Azure Application Insights figyeli alkalmazásai rendelkezésre állását, teljesítményét és használatát. Ebből a cikkből megismerheti, hogyan állíthatja be egy SharePoint-helyhez.

## <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása
Az [Azure Portalon](https://portal.azure.com) hozzon létre egy új Application Insights-erőforrást. Az alkalmazás típusának válassza az ASP.NET lehetőséget.

![Kattintson a Tulajdonságok elemre, válassza ki a kulcsot, és nyomja le a ctrl+C billentyűkombinációt.](./media/sharepoint/001.png)

A megnyíló ablakban megtekintheti az alkalmazása teljesítmény- és használati adatait. Amikor legközelebb bejelentkezik az Azure-ba, a kezdőképernyőn található csempére kattintva léphet közvetlenül erre a panelre. Vagy a Tallózás gombra kattintva is megkeresheti.

## <a name="add-the-script-to-your-web-pages"></a>A szkript hozzáadása weblapokhoz

```HTML
<!-- 
To collect user behavior analytics tools about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var appInsights=window.appInsights||function(a){
  function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
  }({
      instrumentationKey:"<your instrumentation key>"
  });
  
window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
</script>
```

Szúrja be a szkriptet minden olyan lap &lt;/head&gt; címkéje elé, amelyet nyomon szeretne követni. Ha a webhelye mesterlappal rendelkezik, ide helyezheti a szkriptet. Egy ASP.NET MVC-projektben a következő helyre helyezné a szkriptet: View\Shared\_Layout.cshtml

A szkript tartalmazza a kialakítási kulcsot, amely a telemetriát az Application Insights-erőforrásra irányítja.

### <a name="add-the-code-to-your-site-pages"></a>Kód hozzáadása webhely lapjaihoz
#### <a name="on-the-master-page"></a>A mesterlapon
Ha szerkeszti webhelye mesterlapját, az biztosítja a webhely összes lapjának figyelését.

Tekintse át a mesterlapot, és szerkessze a SharePoint Designerrel vagy más szerkesztővel.

![](./media/sharepoint/03-master.png)

Adja hozzá a kódot közvetlenül a(z) </head> címke elé. 

![](./media/sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>Vagy önálló lapokon
Adott számú lap figyeléséhez adja hozzá a szkriptet egyesével a lapokhoz. 

Szúrjon be egy kijelzőt, és ágyazza bele a kódrészletet.

![](./media/sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>Alkalmazás adatainak megtekintése
Helyezze ismét üzembe alkalmazását.

Térjen vissza alkalmazása paneljéhez az [Azure Portalon](https://portal.azure.com).

Az első események megjelennek a keresésben. 

![](./media/sharepoint/09-search.png)

Ha több adatot vár, néhány másodperc elteltével kattintson a Frissítés elemre.

## <a name="capturing-user-id"></a>Felhasználói azonosító rögzítése
A normál weblapkódrészlet nem rögzíti a felhasználói azonosítót a SharePointból, de ez egy apró módosítással elvégezhető.

1. Az Application Insights Alapvető erőforrások legördülő menüjéből másolja ki alkalmazása kialakítási kulcsát. 

    ![](./media/sharepoint/02-props.png)

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



## <a name="next-steps"></a>További lépések
* [Webes tesztek](../../azure-monitor/app/monitor-web-app-availability.md) webhelye rendelkezésre állásának figyeléséhez.
* [Application Insights](../../application-insights/app-insights-overview.md) más típusú alkalmazásokhoz.

<!--Link references-->


