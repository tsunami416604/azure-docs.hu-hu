---
title: Alkalmazásprofilok szerkesztése az élő Azure App Service az Application insights segítségével |} A Microsoft Docs
description: Profil élő alkalmazások az Azure App Service az Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 1d902f8d87b81fda9edf2c1c37ad40416ac6baf2
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54028454"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profil élő Azure App Service-alkalmazások az Application insights segítségével

Profiler az ASP.NET és az ASP.NET Core-alkalmazásokhoz az Azure App Service-ben futó jelenleg működik. Az alapszintű szolgáltatásszintet vagy újabb verziója szükséges Profiler használatára. A linuxon futó Profiler engedélyezése jelenleg csak akkor lehetséges, keresztül [ezzel a módszerrel](profiler-aspnetcore-linux.md).

## <a id="installation"></a> Profiler engedélyezése az alkalmazáshoz
Profiler engedélyezése az alkalmazáshoz, hajtsa végre az alábbi utasításokat. Ha más típusú Azure-szolgáltatások futnak, az alábbiakban útmutatást Profiler engedélyezése az egyéb támogatott platformon:
* [Felhőszolgáltatások](../../application-insights/app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric-alkalmazások](../../application-insights/app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtuális gépek](../../application-insights/app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler az App Services modul részeként előre telepítve van, de a get-profilok az App Service-alkalmazáshoz bekapcsolásához kell. Miután telepített egy alkalmazást, akkor is, ha a forráskódot is szerepelnek az App Insights SDK-t, akkor kövesse az alábbi lépéseket a profiler engedélyezéséhez.

1. Nyissa meg a **App Services** panel az Azure Portalon.
2. Navigáljon a **beállítások > Application Insights** ablaktáblán.

   ![Az App Insights engedélyezése az App Services portálon](./media/profiler/AppInsights-AppServices.png)

3. Lehet, kövesse az utasításokat a panelen hozzon létre egy új erőforrást, vagy válasszon egy meglévő App Insights-erőforrás az alkalmazás figyelésére. Ügyeljen arra, hogy a Profiler van **a**.

   ![App Insights-webhelybővítményt hozzáadása][Enablement UI]

4. Profiler engedélyezve van az App Services Alkalmazásbeállítás használatával.

    ![A Profiler Alkalmazásbeállítás][profiler-app-setting]

## <a name="disable-profiler"></a>Profiler letiltása

Profiler alatt az adott alkalmazáshoz-példány újraindítása vagy leállítása **Webjobs-feladatok**, nyissa meg az alkalmazás-erőforrást. Profiler törléséhez lépjen a **bővítmények**.

![Profiler letiltása a webjobs-feladat][disable-profiler-webjob]

Azt javasoljuk, hogy a Profiler engedélyezve van az összes alkalmazás okoznak teljesítményproblémákat a lehető leghamarabb felderítéséhez.

WebDeploy használatával a webes alkalmazások üzembe helyezése módosításokat, hogy az App_Data mappában kizárása az üzembe helyezés során törlése folyamatban. Ellenkező esetben a Profiler szerepkörbővítmény-fájlok törlődnek a következő alkalommal, amikor telepíti a webalkalmazás az Azure-bA.



## <a name="next-steps"></a>További lépések

* [Az Application Insights a Visual Studio használata](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
