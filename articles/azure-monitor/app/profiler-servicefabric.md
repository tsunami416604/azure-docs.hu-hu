---
title: A profil élő Azure Service Fabric alkalmazásai a Application Insightskal | Microsoft Docs
description: A Profiler engedélyezése Service Fabric alkalmazáshoz
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a7a64be3c73ea82c6bf3d905772f3278f9bda5df
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72818477"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>A profil élő Azure Service Fabric alkalmazásai Application Insights

A Application Insights Profiler a következő szolgáltatásokon is üzembe helyezhető:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>A környezet központi telepítési definíciójának beállítása

A Application Insights Profiler a Azure Diagnostics részét képezi. A Azure Diagnostics bővítményt a Service Fabric-fürthöz tartozó Azure Resource Manager sablonnal is telepítheti. Olyan sablon beszerzése [, amely Azure Diagnostics telepít egy Service Fabric-fürtön](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

A környezet beállításához végezze el a következő műveleteket:

1. A Profiler a .NET-keretrendszer és a .net Core használatát is támogatja. Ha .NET-keretrendszert használ, győződjön meg róla, hogy a [.NET-keretrendszer 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) -es vagy újabb verzióját használja. Elegendő meggyőződni arról, hogy a telepített operációs rendszer `Windows Server 2012 R2` vagy újabb. A Profiler támogatja a .NET Core 2,1-es és újabb alkalmazásokat.

1. Keresse meg a [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) bővítményt a telepítési sablon fájljában.

1. Adja hozzá a következő `SinksConfig` szakaszt `WadCfg`gyermek elemeként. Cserélje le a `ApplicationInsightsProfiler` tulajdonság értékét a saját Application Insights kialakítási kulcsára:  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      További információ a diagnosztikai bővítmény telepítési sablonhoz való hozzáadásáról: a [figyelés és a diagnosztika használata Windows rendszerű virtuális gépekkel és Azure Resource Manager sablonokkal](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

1. Telepítse a Service Fabric-fürtöt a Azure Resource Manager sablon használatával.  
  Ha a beállítások helyesek, Application Insights Profiler lesz telepítve és engedélyezve a Azure Diagnostics bővítmény telepítésekor. 

1. Application Insights hozzáadása a Service Fabric alkalmazáshoz.  
  Ahhoz, hogy a Profiler összegyűjtse a kérelmek profiljait, az alkalmazásnak nyomon kell követnie a Application Insights. Állapot nélküli API-k esetén a [profilkészítési kérelmek nyomon követésére](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)vonatkozó utasításokat tekintheti meg. További információ az egyéni műveletek más típusú alkalmazásokban való nyomon követéséről: [Egyéni műveletek nyomon követése Application Insights .net SDK-val](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Telepítse újra az alkalmazást.


## <a name="next-steps"></a>Következő lépések

* Adatforgalom létrehozása az alkalmazáshoz (például egy [rendelkezésre állási teszt](monitor-web-app-availability.md)elindításához). Ezután várjon 10 – 15 percet, amíg a Nyomkövetések el nem jutnak a Application Insights példányba.
* Lásd: [Profiler-Nyomkövetések](profiler-overview.md?toc=/azure/azure-monitor/toc.json) a Azure Portalban.
* A Profiler hibaelhárításával kapcsolatos segítségért lásd a [Profiler hibaelhárítását](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)ismertető témakört.
