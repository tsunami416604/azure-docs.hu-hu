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
ms.author: brahmnes
ms.openlocfilehash: 7ff93dffe2dd82bdbba204b4235a297b337438f5
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2019
ms.locfileid: "57864063"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>.NET-alkalmazások az Azure App Service-ben a Snapshot Debugger engedélyezése

Snapshot Debugger az ASP.NET és az ASP.NET Core-alkalmazások Windows service-csomagok az Azure App Service-ben futó jelenleg működik.

## <a id="installation"></a> Snapshot Debugger engedélyezése
Snapshot Debugger engedélyezése az alkalmazáshoz, hajtsa végre az alábbi utasításokat. Ha egy Azure-szolgáltatás különböző típusú futtatja, az alábbiakban útmutatást a Snapshot Debugger engedélyezése a támogatott platformokon:
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Az Azure Service Fabric-szolgáltatások](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Az Azure virtuális gépek és virtuálisgép-méretezési csoportokban](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [A helyi virtuális vagy fizikai gépek](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

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

* [Az Application Insights a Visual Studio használata](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

