---
title: A .NET-alkalmazások Snapshot Debugger engedélyezése a Azure App Serviceban | Microsoft Docs
description: .NET-alkalmazások Snapshot Debuggerának engedélyezése Azure App Service
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0f6eb6376075337edd7656e4bc83b5b7fddde479
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899893"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>.NET-alkalmazások Snapshot Debuggerának engedélyezése Azure App Service

A Snapshot Debugger jelenleg a ASP.NET és a ASP.NET Core a Windows-szolgáltatási csomagok Azure App Service futó alkalmazásain működik.

## <a id="installation"></a>Snapshot Debugger engedélyezése
Az alkalmazások Snapshot Debuggerának engedélyezéséhez kövesse az alábbi utasításokat. Ha más típusú Azure-szolgáltatást futtat, akkor a Snapshot Debugger más támogatott platformokon való engedélyezésével kapcsolatban itt talál útmutatást:
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric szolgáltatások](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure-Virtual Machines és virtuálisgép-méretezési csoportok](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Helyszíni virtuális vagy fizikai gépek](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Ha a .NET Core előzetes verzióját használja, kövesse a [más környezetekhez való Snapshot Debugger engedélyezése](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) című témakör útmutatását, és vegye fel a [Microsoft. ApplicationInsights. snapshotcollector nugetcsomag](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-csomagot az alkalmazással. Ezután fejezze be az alábbi utasításokat. 

Application Insights Snapshot Debugger előre telepítve van a App Services futtatókörnyezet részeként, de be kell kapcsolni, hogy pillanatképeket kapjon a App Service alkalmazáshoz. Miután telepítette az alkalmazást, még akkor is, ha a forráskódban szerepel a Application Insights SDK, az alábbi lépésekkel engedélyezheti a pillanatkép-hibakeresőt.

1. Nyissa meg a Azure Portal **app Services** ablaktábláját.
2. Navigáljon a **beállítások > Application Insights** panelre.

   ![Az alkalmazás-felismerés engedélyezése App Services portálon](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Kövesse a panel utasításait egy új erőforrás létrehozásához, vagy válasszon ki egy meglévő alkalmazás-keresési erőforrást az alkalmazás figyeléséhez. Győződjön meg arról is, hogy a Snapshot Debugger mindkét kapcsolója **be van kapcsolva**.

   ![Alkalmazás-áttekintési hely kiterjesztésének hozzáadása][Enablement UI]

4. A Snapshot Debugger mostantól engedélyezve van egy App Services alkalmazás-beállítás használatával.

    ![Alkalmazás-beállítás a Snapshot Debugger][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Snapshot Debugger letiltása

Hajtsa végre az **Snapshot Debugger engedélyezésének**lépéseit, de mindkét kapcsolót a Snapshot Debuggerra kapcsolhatja **ki**.
Javasoljuk, hogy az alkalmazás-kivételek diagnosztizálásához az összes alkalmazásnál Snapshot Debugger engedélyezzen.

## <a name="next-steps"></a>Következő lépések

- Adatforgalom létrehozása az alkalmazás számára, amely kivételt indíthat. Ezután várjon 10 – 15 percet a pillanatképek Application Insights példányba való elküldésekor.
- A Azure Portal található [Pillanatképek](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) .
- Snapshot Debugger problémák elhárításával kapcsolatos segítségért lásd: [Snapshot Debugger hibaelhárítás](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

