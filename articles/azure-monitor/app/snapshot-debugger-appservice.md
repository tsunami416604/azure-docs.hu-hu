---
title: Pillanatkép-hibakereső engedélyezése .NET-alkalmazásokhoz az Azure App Service-ben | Microsoft dokumentumok
description: Pillanatkép-hibakereső engedélyezése .NET-alkalmazásokhoz az Azure App Service-ben
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 8af688e38003e0613a06d7d8622ce279a3838589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298273"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Pillanatkép-hibakereső engedélyezése .NET-alkalmazásokhoz az Azure App Service-ben

Snapshot Debugger jelenleg működik ASP.NET és ASP.NET Core alkalmazások, amelyek futnak az Azure App Service Windows-szolgáltatási csomagok.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a>Pillanatkép-hibakereső engedélyezése
A Snapshot Debugger engedélyezéséhez kövesse az alábbi utasításokat. Ha más típusú Azure-szolgáltatást futtat, az alábbi utasításokat olvashatja a Snapshot Debugger más támogatott platformokon való engedélyezéséhez:
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric-szolgáltatások](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Az Azure virtuális gépek és a virtuális gépek méretezési készletei](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Helyszíni virtuális vagy fizikai gépek](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Ha a .NET Core előzetes verzióját használja, kövesse a [Snapshot Debugger engedélyezése más környezetekben](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) című útmutatót, hogy a [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet csomagot tartalmazza az alkalmazással, majd töltse ki az alábbi utasításokat. 

Az Application Insights Snapshot Debugger előre telepítve van az App Services futásidejű részeként, de be kell kapcsolnia az App Service-alkalmazás pillanatképeinek lekérni. Miután telepített egy alkalmazást, még akkor is, ha az Application Insights SDK-t a forráskódba helyezte, kövesse az alábbi lépéseket a pillanatkép-hibakereső engedélyezéséhez.

1. Nyissa meg az **App Services** ablaktábláját az Azure Portalon.
2. Nyissa meg **a Beállítások > Az Application Insights** ablaktáblán.

   ![Az App Insights engedélyezése az App Services portálon](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Kövesse az ablaktáblán található utasításokat egy új erőforrás létrehozásához, vagy válasszon ki egy meglévő App Insights-erőforrást az alkalmazás figyeléséhez. Győződjön meg arról is, hogy a Snapshot Debugger mindkét kapcsolója be van **kapcsolva.**

   ![App Insights-webhelybővítmény hozzáadása][Enablement UI]

4. A Pillanatkép-hibakereső most már engedélyezve van egy App Services-alkalmazásbeállítás használatával.

    ![Alkalmazásbeállítás pillanatkép-hibakeresőhez][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Pillanatkép-hibakereső letiltása

Kövesse ugyanazokat a lépéseket, mint a **Snapshot Debugger engedélyezése,** de kapcsolja be mindkét kapcsolót a Snapshot Debugger beállításhoz. **Off**
Azt javasoljuk, hogy a Snapshot Debugger engedélyezve van az összes alkalmazáson az alkalmazáskivételek diagnosztikájának megkönnyítése érdekében.

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

Egy Azure App Service, beállíthatja az alkalmazás beállításait egy Azure Resource Manager sablon, hogy a Snapshot Debugger és profiler engedélyezheti. Hozzáad egy konfigurációs erőforrást, amely a webhely gyermekerőforrásaként tartalmazza az alkalmazásbeállításokat:

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  "tags": { 
    "[concat('hidden-related:', resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName')))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[parameters('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "name": "appsettings",
      "type": "config",
      "dependsOn": [
        "[parameters('webSiteName')]",
        "[concat('AppInsights', parameters('webSiteName'))]"
      ],
      "properties": {
        "APPINSIGHTS_INSTRUMENTATIONKEY": "[reference(resourceId('Microsoft.Insights/components', concat('AppInsights', parameters('webSiteName'))), '2014-04-01').InstrumentationKey]",
        "APPINSIGHTS_PROFILERFEATURE_VERSION": "1.0.0",
        "APPINSIGHTS_SNAPSHOTFEATURE_VERSION": "1.0.0",
        "DiagnosticServices_EXTENSION_VERSION": "~3",
        "ApplicationInsightsAgent_EXTENSION_VERSION": "~2"
      }
    }
  ]
},
```

## <a name="next-steps"></a>További lépések

- Hozzon létre forgalmat az alkalmazás, amely kivételt válthat ki. Ezután várjon 10–15 percet, amíg a pillanatképeket el kell küldeni az Application Insights-példány.
- Tekintse meg a [pillanatképek az](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) Azure Portalon.
- A Pillanatkép-hibakeresővel kapcsolatos problémák elhárításával kapcsolatos segítségért olvassa el a [Pillanatképhiba-elhárító hibaelhárításcímű témakört.](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

