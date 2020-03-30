---
title: Élő Azure App Service-alkalmazások profila Application Insights szolgáltatással | Microsoft dokumentumok
description: Élő alkalmazások profilaz Azure App Service-ben az Application Insights Profiler használatával.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ba9a2aca73dbdb8de298b68670fd6ab16f810a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275775"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Élő Azure App Service-alkalmazások profila az Application Insights segítségével

A Profiler futtatható ASP.NET és ASP.NET Core alkalmazások, amelyek az Azure App Service-en futnak az alapszintű szolgáltatási szint vagy magasabb használatával. A Profiler engedélyezése Linuxon jelenleg csak [ezzel a módszerrel](profiler-aspnetcore-linux.md)lehetséges.

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a>Profilozó engedélyezése az alkalmazáshoz
A Profiler engedélyezéséhez kövesse az alábbi utasításokat. Ha más típusú Azure-szolgáltatást futtat, az alábbi utasításokat olvashatja a Profiler más támogatott platformokon való engedélyezéséhez:
* [Felhőszolgáltatások](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Szolgáltatásháló-alkalmazások](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtuális gépek](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Az Application Insights Profiler előre telepítve van az App Services futásidejű részeként. Az alábbi lépések bemutatják, hogyan engedélyezheti azt az App Service számára. Kövesse ezeket a lépéseket akkor is, ha az App Insights SDK-t buildidő vel együtt felvette az alkalmazásba.

1. Engedélyezze a "Mindig bekapcsolva" beállítást az alkalmazásszolgáltatáshoz. A beállítást az App Service Konfiguráció lapján az Általános beállítások csoportban frissítheti.
1. Nyissa meg az **App Services** ablaktábláját az Azure Portalon.
1. Nyissa meg **a Beállítások > Az Application Insights** ablaktáblán.

   ![Az App Insights engedélyezése az App Services portálon](./media/profiler/AppInsights-AppServices.png)

1. Kövesse az ablaktáblán található utasításokat egy új erőforrás létrehozásához, vagy válasszon ki egy meglévő App Insights-erőforrást az alkalmazás figyeléséhez. Győződjön meg arról is, hogy a Profiler be van **kapcsolva.** Ha az Application Insights-erőforrás az App Service-től eltérő előfizetésben van, ezen a lapon nem konfigurálhatja az Application Insightsot. Manuálisan is megteheti, ha manuálisan hozza létre a szükséges alkalmazásbeállításokat. [A következő szakasz a Profiler manuális engedélyezésére vonatkozó utasításokat tartalmazza.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![App Insights-webhelybővítmény hozzáadása][Enablement UI]

1. A Profiler most már engedélyezve van egy App Services-alkalmazásbeállítás használatával.

    ![A Profiler alkalmazásbeállítása][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Profilkezelő engedélyezése manuálisan vagy az Azure Resource Manager rel
Az Application Insights Profiler az Azure App Service alkalmazásbeállításainak létrehozásával engedélyezhető. A fenti beállításokkal rendelkező lap hozza létre ezeket az alkalmazásbeállításokat. De automatizálhatja ezeknek a beállításoknak a létrehozását sablon vagy más módon. Ezek a beállítások akkor is működnek, ha az Application Insights-erőforrás az Azure App Service-től eltérő előfizetésben van.
A profilozó engedélyezéséhez szükséges beállítások a következők:

|Alkalmazásbeállítás    | Érték    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey az Application Insights-erőforráshoz    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


Ezeket az értékeket az [Azure Resource Manager-sablonok](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [az Azure Powershell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp), az [Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest)segítségével állíthatja be.

### <a name="enabling-profiler-for-other-clouds-manually"></a>A Profiler engedélyezése más felhőkhöz manuálisan

Ha engedélyezni szeretné a profilozót más felhők számára, használhatja az alábbi alkalmazásbeállításokat.

|Alkalmazásbeállítás    | Az Egyesült Államok kormányának értékei| Kína Felhő |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | https://agent.serviceprofiler.azure.us    | https://profiler.applicationinsights.azure.cn |
|ApplicationInsightsEndpoint | https://dc.applicationinsights.us | https://dc.applicationinsights.azure.cn |

## <a name="disable-profiler"></a>Profilozó letiltása

Ha le szeretné állítani vagy újra szeretné indítani a Profiler-t egy adott alkalmazás példányához, a **Webes feladatok**csoportban nyissa meg az alkalmazás-erőforrást. A Profiler törléséhez nyissa meg **a Bővítmények (Bővítmények) (Bővítmények) (Bővítmények) (Bővítmények) (Bővítmények) (Bővítmények) (Bővítmények) (Bővítmények**

![Profilozó letiltása webes feladathoz][disable-profiler-webjob]

Azt javasoljuk, hogy a Profiler engedélyezve legyen az összes alkalmazásában, hogy a lehető leghamarabb észlelhesse a teljesítményproblémákat.

A Profiler fájljai törölhetők, ha a WebDeploy használatával telepíti a webalkalmazás módosításait. A törlést megakadályozhatja, ha kizárja a App_Data mappa törlését a telepítés során. 


## <a name="next-steps"></a>További lépések

* [Az Application Insights használata a Visual Studióban](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
