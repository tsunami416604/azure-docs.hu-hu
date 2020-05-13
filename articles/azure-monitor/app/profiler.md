---
title: Profil élő Azure App Service alkalmazások a Application Insightskal | Microsoft Docs
description: A Azure App Serviceon futó élő alkalmazások a Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 4d41ece86240a20afea06bff3469b5c02c6e46ff
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121197"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profil élő Azure App Service alkalmazások Application Insights

A Profilert futtathatja a ASP.NET-on, és ASP.NET Core a Azure App Service-on futó alkalmazások alapszintű vagy magasabb szintű használatát. A Profiler Linux rendszeren való engedélyezése jelenleg csak [ezen a metóduson](profiler-aspnetcore-linux.md)keresztül lehetséges.

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a>A Profiler engedélyezése az alkalmazáshoz
A Profiler engedélyezéséhez kövesse az alábbi utasításokat. Ha más típusú Azure-szolgáltatást futtat, akkor a Profiler más támogatott platformokon való engedélyezésével kapcsolatban itt talál útmutatást:
* [Felhőszolgáltatások](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Alkalmazások Service Fabric](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler az App Services futtatókörnyezet részeként előre telepítve van. Az alábbi lépések bemutatják, hogyan engedélyezheti a App Service számára. Kövesse az alábbi lépéseket még akkor is, ha felépítésekor az alkalmazás betekintő SDK-t is feltöltötte az alkalmazásba.

1. Az App Service "always on" beállításának engedélyezése. Az általános beállítások területen a App Service konfiguráció lapján frissítheti a beállítást.
1. Nyissa meg a Azure Portal **app Services** ablaktábláját.
1. Navigáljon a **beállítások > Application Insights** panelre.

   ![Az alkalmazás-felismerés engedélyezése App Services portálon](./media/profiler/AppInsights-AppServices.png)

1. Kövesse a panel utasításait egy új erőforrás létrehozásához, vagy válasszon ki egy meglévő alkalmazás-keresési erőforrást az alkalmazás figyeléséhez. Győződjön meg arról is, hogy a Profiler **be van kapcsolva**. Ha a Application Insights erőforrás a App Servicetól eltérő előfizetésben található, nem használhatja ezt a lapot a Application Insights konfigurálásához. Ezt manuálisan is megteheti, ha manuálisan hozza létre a szükséges alkalmazás-beállításokat. [A következő szakasz a Profiler manuális engedélyezésére vonatkozó utasításokat tartalmazza.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Alkalmazás-áttekintési hely kiterjesztésének hozzáadása][Enablement UI]

1. A Profiler mostantól engedélyezve van egy App Services alkalmazás-beállítás használatával.

    ![A Profiler alkalmazásának beállítása][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>A Profiler manuális engedélyezése vagy Azure Resource Manager
Application Insights Profiler a Azure App Service alkalmazás-beállításainak létrehozásával engedélyezhető. A fent látható beállításokkal rendelkező oldal létrehozza ezeket az Alkalmazásbeállítások. Ezeket a beállításokat azonban a sablonok vagy más eszközök használatával automatizálhatja. Ezek a beállítások abban az esetben is működni fognak, ha a Application Insights erőforrás a Azure App Servicetól eltérő előfizetésben található.
A Profiler engedélyezéséhez a következő beállítások szükségesek:

|Alkalmazásbeállítás    | Érték    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | a Application Insights-erőforrás Rendszerállapotkulcsot    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~ 3 |


Ezeket az értékeket [Azure Resource Manager sablonokkal](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), az [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp)-lel és az [Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest)-vel is megadhatja.

### <a name="enabling-profiler-for-other-clouds-manually"></a>A Profiler engedélyezése más felhők számára manuálisan

Ha engedélyezni szeretné a Profilert más felhők esetében, használhatja az alábbi beállításokat.

|Alkalmazásbeállítás    | USA kormányzati értékei| Kínai felhő |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://agent.serviceprofiler.azure.us`    | `https://profiler.applicationinsights.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

## <a name="disable-profiler"></a>Profiler letiltása

Egy adott alkalmazás példányának leállításához vagy újraindításához a **webes feladatok**területen nyissa meg az alkalmazás erőforrását. A Profiler törléséhez nyissa meg a **bővítmények csomópontot**.

![A Profiler letiltása webes feladatokhoz][disable-profiler-webjob]

Azt javasoljuk, hogy az összes alkalmazáson engedélyezve legyen a Profiler, hogy a lehető leghamarabb észlelje a teljesítménnyel kapcsolatos problémákat.

A Profiler fájljai törölhető, ha a webalkalmazás használatával helyezi üzembe a webalkalmazás módosításait. A törlést megakadályozhatja, ha nem törli a App_Data mappát az üzembe helyezés során. 


## <a name="next-steps"></a>További lépések

* [Az Application Insights használata a Visual Studióban](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
