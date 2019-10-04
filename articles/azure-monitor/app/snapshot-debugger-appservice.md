---
title: .NET-alkalmazások az Azure App Service-ben a Snapshot Debugger engedélyezése |} A Microsoft Docs
description: .NET-alkalmazások az Azure App Service-ben a Snapshot Debugger engedélyezése
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: bfung
ms.openlocfilehash: 3e8ce3c2eff7b1f7184bb37f141e62563d4fe714
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612686"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>.NET-alkalmazások az Azure App Service-ben a Snapshot Debugger engedélyezése

Snapshot Debugger az ASP.NET és az ASP.NET Core-alkalmazások Windows service-csomagok az Azure App Service-ben futó jelenleg működik.

## <a id="installation"></a> Snapshot Debugger engedélyezése
Snapshot Debugger engedélyezése az alkalmazáshoz, hajtsa végre az alábbi utasításokat. Ha egy Azure-szolgáltatás különböző típusú futtatja, az alábbiakban útmutatást a Snapshot Debugger engedélyezése a támogatott platformokon:
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Az Azure Service Fabric-szolgáltatások](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Az Azure virtuális gépek és virtuálisgép-méretezési csoportokban](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [A helyi virtuális vagy fizikai gépek](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Egy előzetes verziója a .NET Core használatakor kövesse az utasításokat [Snapshot Debugger engedélyezése más környezetek](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) először felvenni a [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet csomag az alkalmazással együtt, és ezután fejezze be az alábbi utasításokat. 

Application Insights Snapshot Debugger előre telepítve van az App Services modul részeként, de a get-pillanatképeket az App Service-alkalmazáshoz bekapcsolásához kell. Miután telepített egy alkalmazást, akkor is, ha a forráskódot is szerepelnek az Application Insights SDK-t, akkor kövesse az alábbi lépéseket a snapshot debugger engedélyezése.

1. Nyissa meg a **App Services** panel az Azure Portalon.
2. Navigáljon a **beállítások > Application Insights** ablaktáblán.

   ![Az App Insights engedélyezése az App Services portálon](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Lehet, kövesse az utasításokat a panelen hozzon létre egy új erőforrást, vagy válasszon egy meglévő App Insights-erőforrás az alkalmazás figyelésére. Ügyeljen arra, hogy a pillanatkép-hibakereső mindkét kapcsolók a következők **a**.

   ![App Insights-webhelybővítményt hozzáadása][Enablement UI]

4. Pillanatkép-hibakereső engedélyezve van az App Services Alkalmazásbeállítás használatával.

    ![A pillanatkép-hibakereső Alkalmazásbeállítás][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Tiltsa le a pillanatkép-hibakereső

Ugyanezekkel a lépésekkel megegyezik a **Snapshot Debugger engedélyezése**, de mindkét kapcsolók váltson a Snapshot Debuggert a **ki**.
Azt javasoljuk, hogy a pillanatkép-hibakereső diagnosztikai alkalmazás kivételek megkönnyítése érdekében az összes alkalmazás engedélyezve.

## <a name="next-steps"></a>További lépések

- Hozzon létre a forgalmat az alkalmazásához, elindíthat egy kivételt. Várjon 10 – 15 percet kell küldeni az Application Insights-példány pillanatképeket.
- Lásd: [pillanatképek](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) az Azure Portalon.
- Segítség a pillanatkép-hibakereső kapcsolatos hibák elhárítása: [pillanatkép-hibakereső hibaelhárítási](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

