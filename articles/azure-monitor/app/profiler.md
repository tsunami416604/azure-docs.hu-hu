---
title: Alkalmazásprofilok szerkesztése az élő Azure App Service az Application insights segítségével |} A Microsoft Docs
description: Profil élő alkalmazások az Azure App Service az Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: b8f6a2d12e1a9920421e6491432b516520ae110b
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59493739"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profil élő Azure App Service-alkalmazások az Application insights segítségével

Futtathatja a Profiler az ASP.NET- és ASP.NET Core futó alkalmazások az Azure App Service használatával alapszintű szolgáltatásszint vagy újabb verziója. A linuxon futó Profiler engedélyezése jelenleg csak akkor lehetséges, keresztül [ezzel a módszerrel](profiler-aspnetcore-linux.md).

## <a id="installation"></a> Profiler engedélyezése az alkalmazáshoz
Profiler engedélyezése az alkalmazáshoz, hajtsa végre az alábbi utasításokat. Ha más típusú Azure-szolgáltatások rendszert használ, az alábbiakban útmutatást Profiler engedélyezése az egyéb támogatott platformon:
* [Cloud Services](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric-alkalmazások](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtuális gépek](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler az App Services modul részeként előre telepítve van. Az alábbi lépések bemutatják, hogyan engedélyezheti az App Service. Kövesse az alábbi lépéseket, akkor is, ha az összeállítás során az alkalmazás már szerepel az App Insights SDK.

1. Engedélyezze az app service "Mindig bekapcsolva" beállítást. A beállítás a konfigurációs lapja az App Service, az általános beállítások részben frissítheti.
1. Nyissa meg a **App Services** panel az Azure Portalon.
1. Navigáljon a **beállítások > Application Insights** ablaktáblán.

   ![Az App Insights engedélyezése az App Services portálon](./media/profiler/AppInsights-AppServices.png)

1. Lehet, kövesse az utasításokat a panelen hozzon létre egy új erőforrást, vagy válasszon egy meglévő App Insights-erőforrás az alkalmazás figyelésére. Ügyeljen arra, hogy a Profiler van **a**. Ha az Application Insights-erőforrást egy másik előfizetésben az App Service-ből, ezen a lapon nem használhat az Application Insights konfigurálásához. Továbbra is úgy teheti meg, manuálisan, ha a szükséges beállítások létrehozása manuálisan. [Ez a szakasz útmutatást manuálisan a Profiler engedélyezéséhez.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![App Insights-webhelybővítményt hozzáadása][Enablement UI]

1. Profiler engedélyezve van az App Services Alkalmazásbeállítás használatával.

    ![A Profiler Alkalmazásbeállítás][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Profiler engedélyezése manuálisan vagy az Azure Resource Managerrel
Application Insights Profiler az Azure App Service-beállításainak létrehozásával is engedélyezhetők. Az oldal a fenti beállítások ezen alkalmazás beállításokat hoz létre. De ezek a beállítások a sablonban vagy más módon létrehozását automatizálhatja. Ezek a beállítások is működik, ha az Application Insights-erőforrást az Azure App Service egy másik előfizetésben található.
Az alábbiakban a profiler engedélyezéséhez szükséges beállításokat:

|Alkalmazásbeállítás    | Érték    |
|---------------|----------|
|ÁLLÍTANI AZ APPINSIGHTS_INSTRUMENTATIONKEY         | a Rendszerállapotkulcsot az Application Insights-erőforrás    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


Beállíthatja, hogy ezeket az értékeket [Azure Resource Manager-sablonok](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [Azure PowerShell-lel](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp), [Azure CLI-vel](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest).


## <a name="disable-profiler"></a>Profiler letiltása

Profiler alatt az adott alkalmazáshoz-példány újraindítása vagy leállítása **Webjobs-feladatok**, nyissa meg az alkalmazás-erőforrást. Profiler törléséhez lépjen a **bővítmények**.

![Profiler letiltása a webjobs-feladat][disable-profiler-webjob]

Azt javasoljuk, hogy a Profiler engedélyezve van az összes alkalmazás okoznak teljesítményproblémákat a lehető leghamarabb felderítéséhez.

A Profiler fájlok törölhetők, WebDeploy használatakor, hogy a módosítások a webalkalmazás üzembe helyezése. Üzembe helyezés során törölhető az App_Data mappában kizárásával megakadályozhatja a törlés. 


## <a name="next-steps"></a>További lépések

* [Az Application Insights használata a Visual Studióban](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
