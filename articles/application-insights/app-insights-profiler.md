---
title: Profil élő Azure-webalkalmazások az Application insights segítségével |} A Microsoft Docs
description: Profil élő webalkalmazások az Application Insights Profiler az Azure-ban.
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
ms.openlocfilehash: 4d957c26bd4e4ae278c0909c9df1476b02954b86
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138007"
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Profil élő Azure-webalkalmazások az Application insights segítségével

Profiler az ASP.NET és az ASP.NET Core web Apps, Web Apps szolgáltatást futtató jelenleg működik. Az alapszintű szolgáltatásszintet vagy újabb verziója szükséges Profiler használatára. A linuxon futó Profiler engedélyezése jelenleg csak akkor lehetséges, keresztül [ezzel a módszerrel](app-insights-profiler-aspnetcore-linux.md).

## <a id="installation"></a> A Web Apps számára Profiler engedélyezése
Profiler engedélyezése webes alkalmazásokhoz, kövesse az alábbi utasításokat. Ha más típusú Azure-szolgáltatások futnak, az alábbiakban útmutatást Profiler engedélyezése az egyéb támogatott platformon:
* [Felhőszolgáltatások](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric-alkalmazások](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtuális gépek](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler az App Services modul részeként előre telepítve van, de a bekapcsolásához és get-profilok az Azure Web Apps kell. Akkor is, ha a forráskódot is szerepelnek az App Insights SDK egy webalkalmazás üzembe helyezett, kövesse az alábbi lépéseket a profiler engedélyezéséhez.

1. Nyissa meg a **App Services** panel az Azure Portalon.
1. Navigáljon a **beállítások > Application Insights** ablaktáblán.

   ![Az App Insights engedélyezése az App Services portálon](./media/app-insights-profiler/AppInsights-AppServices.png)

1. Lehet, kövesse az utasításokat a panelen hozzon létre egy új erőforrást, vagy válasszon egy meglévő App Insights-erőforrás a webes alkalmazás figyelésére. Ügyeljen arra, hogy a Profiler van **a**.

   ![App Insights-webhelybővítményt hozzáadása][Enablement UI]

1. Profiler engedélyezve van az App Services Alkalmazásbeállítás használatával.

    ![A Profiler Alkalmazásbeállítás][profiler-app-setting]

## <a name="disable-profiler"></a>Profiler letiltása

Profiler alatt egy adott webalkalmazás-példány újraindítása vagy leállítása **Webjobs-feladatok**, nyissa meg a Web Apps erőforrás. Profiler törléséhez lépjen a **bővítmények**.

![Profiler letiltása a webjobs-feladat][disable-profiler-webjob]

Azt javasoljuk, hogy a Profiler engedélyezve a web apps a lehető legkorábbi felderítéséhez okoznak teljesítményproblémákat.

WebDeploy használatával a webes alkalmazások üzembe helyezése módosításokat, hogy az App_Data mappában kizárása az üzembe helyezés során törlése folyamatban. Ellenkező esetben a Profiler szerepkörbővítmény-fájlok törlődnek a következő alkalommal, amikor telepíti a webalkalmazás az Azure-bA.



## <a name="next-steps"></a>További lépések

* [Az Application Insights a Visual Studio használata](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[Enablement UI]: ./media/app-insights-profiler/Enablement_UI.png
[profiler-app-setting]:./media/app-insights-profiler/profiler-app-setting.png
[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png

