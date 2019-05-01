---
title: Az Azure Állapotfigyelőt v2 – áttekintés |} A Microsoft Docs
description: Az állapotfigyelő v2 áttekintése. A webhely újbóli üzembe helyezése nélkül webhely teljesítményének megfigyeléséhez. A helyszíni, valamint a virtuális gépeken, illetve az Azure-ban üzemeltetett ASP.NET-webappokhoz is használható.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: fac14281365ccf3c191684af8cfdebda69e734e0
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870447"
---
# <a name="status-monitor-v2"></a>Állapot figyelő v2

Állapot figyelő v2 egy közzétett PowerShell-modul a [PowerShell-Galériabeli](https://www.powershellgallery.com/packages/Az.ApplicationMonitor) és a helyettesíti a [Állapotfigyelőt](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now). Ez a modul adja meg az IIS-kiszolgálón üzemeltetett webalkalmazások .NET kód nélküli kialakítási.
Telemetriát küld az Azure Portalon ahol [figyelő](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) alkalmazását.

> [!IMPORTANT]
> Állapot figyelő v2 jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="powershell-gallery"></a>PowerShell-galéria

https://www.powershellgallery.com/packages/Az.ApplicationMonitor


## <a name="instructions"></a>Utasítások
- Tekintse át a [első lépések útmutató](status-monitor-v2-get-started.md) tömör Kódminták a kezdéshez.
- Tekintse át a [részletes utasítások](status-monitor-v2-detailed-instructions.md) az első lépésekről részletes bemutatása.

## <a name="powershell-api-reference"></a>PowerShell API-referencia
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)

## <a name="troubleshooting"></a>Hibaelhárítás
- [hibaelhárítással](status-monitor-v2-troubleshoot.md)
- [Ismert problémák](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>GYIK

- Támogatja az Állapotfigyelőt v2 proxy telepítések?

  **Igen**. Töltse le az Állapotfigyelőt v2 több lehetősége van. Ha a számítógép rendelkezik internet-hozzáféréssel, segítségével készítheti elő a PowerShell-galériából történő `-Proxy` paramétereket. Manuálisan is letöltheti a modult, és telepítse azt a gépen, vagy a modul közvetlenül használni. A felsorolt lehetőségek ismertetett a [részletes utasításokat](status-monitor-v2-detailed-instructions.md).
  
- Az engedélyezés ellenőrzése sikeres volt?

   A parancsmag nem rendelkezünk, ellenőrizze, hogy engedélyezése sikeres volt. Azt javasoljuk, [élő mérőszámok](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) gyorsan megfigyelni, ha az alkalmazás USA telemetriai adatokat küldenek.
